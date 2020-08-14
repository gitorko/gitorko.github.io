---
title: State Machine
date: 2020-02-11 00:00:00
tags: 
- state machine
categories:
- [Design Pattern]
- [Spring]
---

State machine lets you move to different states based on the events, you can also have listeners registered that get notified on state change events and carry out certain actions.

Github: [https://github.com/gitorko/project77](https://github.com/gitorko/project77)

<!-- more -->

<!-- toc -->

## State machine

We will use the shopping cart state machine diagram as a reference to implement our code. If any invalid events are sent then an exception is thrown.

{% asset_img statemachine.png %}

## Code

Here we use state design pattern and observer pattern to design a state machine.

Application.java

```java
package com.demo.project77.simple;

import java.util.ArrayList;
import java.util.List;

import lombok.Builder;
import lombok.Data;

public class Application {
    public static void main(String[] args) throws RuntimeException {

        NotifyListener notifyListener = new NotifyListener();
        notifyListener.registerObserver(new ShippedEventObserver());

        StateMachineContext stateMachine = StateMachineContext.builder()
                        .state(new BeginState())
                        .notifyListener(notifyListener)
                        .build();
        stateMachine.sendEvent(ShoppingCartEvent.ADD_ITEM);
        if (stateMachine.getId() != ShoppingCartState.SHOPPING_STATE) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(ShoppingCartEvent.ADD_ITEM);
        if (stateMachine.getId() != ShoppingCartState.SHOPPING_STATE) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(ShoppingCartEvent.MAKE_PAYMENT);
        if (stateMachine.getId() != ShoppingCartState.PAYMENT_STATE) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(ShoppingCartEvent.PAYMENT_FAIL);
        if (stateMachine.getId() != ShoppingCartState.SHOPPING_STATE) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(ShoppingCartEvent.MAKE_PAYMENT);
        stateMachine.sendEvent(ShoppingCartEvent.PAYMENT_SUCESS);
        if (stateMachine.getId() != ShoppingCartState.SHIPPED_STATE) throw new RuntimeException("ERROR");

    }
}

@Data
@Builder
class StateMachineContext {
    State state;
    ShoppingCartState id;
    NotifyListener notifyListener;

    public void sendEvent(ShoppingCartEvent event) {
        state.nextState(this, event);
        notifyListener.notifyObservers(this.state.getClass().getSimpleName());
    }
}

enum ShoppingCartState {
    BEGIN_STATE,
    SHOPPING_STATE,
    PAYMENT_STATE,
    SHIPPED_STATE;
}

enum ShoppingCartEvent {
    ADD_ITEM,
    MAKE_PAYMENT,
    PAYMENT_SUCESS,
    PAYMENT_FAIL;
}


interface State {
    void nextState(StateMachineContext stateMachine, ShoppingCartEvent event);
}

@Data
class BeginState implements State {
    public ShoppingCartState id = ShoppingCartState.BEGIN_STATE;
    @Override
    public void nextState(StateMachineContext stateMachine, ShoppingCartEvent event) {
        switch (event) {
        case ADD_ITEM: {
            ShoppingState nextState = new ShoppingState();
            stateMachine.setState(nextState);
            stateMachine.setId(nextState.id);
            break;
        }
        default:
            throw new UnsupportedOperationException("Not Supported!");
        }
    }
}

@Data
class ShoppingState implements State {
    ShoppingCartState id = ShoppingCartState.SHOPPING_STATE;
    @Override
    public void nextState(StateMachineContext stateMachine, ShoppingCartEvent event) {
        switch (event) {
        case ADD_ITEM: {
            ShoppingState nextState = new ShoppingState();
            stateMachine.setState(nextState);
            stateMachine.setId(nextState.id);
            break;
        }
        case MAKE_PAYMENT: {
            PaymentState nextState = new PaymentState();
            stateMachine.setState(nextState);
            stateMachine.setId(nextState.id);
            break;
        }
        default:
            throw new UnsupportedOperationException("Not Supported!");
        }
    }
}

@Data
class PaymentState implements State {
    ShoppingCartState id = ShoppingCartState.PAYMENT_STATE;
    @Override
    public void nextState(StateMachineContext stateMachine, ShoppingCartEvent event) {
        switch (event) {
        case PAYMENT_SUCESS: {
            ShippedState nextState = new ShippedState();
            stateMachine.setState(nextState);
            stateMachine.setId(nextState.id);
            break;
        }
        case PAYMENT_FAIL:
            ShoppingState nextState = new ShoppingState();
            stateMachine.setState(nextState);
            stateMachine.setId(nextState.id);
            break;
        default:
            throw new UnsupportedOperationException("Not Supported!");
        }
    }
}

@Data
class ShippedState implements State {
    ShoppingCartState id = ShoppingCartState.SHIPPED_STATE;
    @Override
    public void nextState(StateMachineContext stateMachine, ShoppingCartEvent event) {
        throw new UnsupportedOperationException("Not Supported!");
    }
}

interface Observer {
    public void notify(String message);
}

class ShippedEventObserver implements Observer {
    @Override
    public void notify(String message) {
        if (message.startsWith("ShippedState")) {
            //This observer is interested only in shipped events.
            System.out.println("ShippedEventObserver got Message: " + message);
        }
    }
}

interface Subject {
    public void registerObserver(Observer observer);

    public void notifyObservers(String tick);
}

class NotifyListener implements Subject {
    List<Observer> notifyList = new ArrayList<>();

    @Override
    public void registerObserver(Observer observer) {
        notifyList.add(observer);
    }

    @Override
    public void notifyObservers(String message) {
        notifyList.forEach(e -> e.notify(message));
    }
}

```

We can also use the spring state machine libraries

Application.java

```java
package com.demo.project77.spring;

import java.util.EnumSet;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.Message;
import org.springframework.messaging.support.MessageBuilder;
import org.springframework.statemachine.StateMachine;
import org.springframework.statemachine.action.Action;
import org.springframework.statemachine.config.EnableStateMachineFactory;
import org.springframework.statemachine.config.EnumStateMachineConfigurerAdapter;
import org.springframework.statemachine.config.StateMachineFactory;
import org.springframework.statemachine.config.builders.StateMachineConfigurationConfigurer;
import org.springframework.statemachine.config.builders.StateMachineStateConfigurer;
import org.springframework.statemachine.config.builders.StateMachineTransitionConfigurer;
import org.springframework.statemachine.listener.StateMachineListenerAdapter;
import org.springframework.statemachine.state.State;
import reactor.core.publisher.Mono;

@SpringBootApplication
@Slf4j
public class Application implements ApplicationRunner {

    @Autowired
    private StateMachineFactory<ShoppingCartState, ShoppingCartEvent> stateMachineFactory;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(ApplicationArguments args) throws Exception {
        StateMachine<ShoppingCartState, ShoppingCartEvent> stateMachine = stateMachineFactory.getStateMachine(
                "mymachine");
        stateMachine.sendEvent(getEventMessage(ShoppingCartEvent.ADD_ITEM)).subscribe();
        if (!(stateMachine.getState().getId().equals(ShoppingCartState.SHOPPING_STATE))) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(getEventMessage(ShoppingCartEvent.ADD_ITEM)).subscribe();
        if (!(stateMachine.getState().getId().equals(ShoppingCartState.SHOPPING_STATE))) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(getEventMessage(ShoppingCartEvent.MAKE_PAYMENT)).subscribe();
        if (!(stateMachine.getState().getId().equals(ShoppingCartState.PAYMENT_STATE))) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(getEventMessage(ShoppingCartEvent.PAYMENT_FAIL)).subscribe();
        if (!(stateMachine.getState().getId().equals(ShoppingCartState.SHOPPING_STATE))) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(getEventMessage(ShoppingCartEvent.MAKE_PAYMENT)).subscribe();
        if (!(stateMachine.getState().getId().equals(ShoppingCartState.PAYMENT_STATE))) throw new RuntimeException("ERROR");
        stateMachine.sendEvent(getEventMessage(ShoppingCartEvent.PAYMENT_SUCESS)).subscribe();
        if (!(stateMachine.getState().getId().equals(ShoppingCartState.SHIPPED_STATE))) throw new RuntimeException("ERROR");
        log.info("Final State: {}", stateMachine.getState().getId());
    }

    private Mono<Message<ShoppingCartEvent>> getEventMessage(ShoppingCartEvent event) {
       return Mono.just(MessageBuilder.withPayload(event).build());
    }
}

enum ShoppingCartEvent {
    ADD_ITEM,
    MAKE_PAYMENT,
    PAYMENT_SUCESS,
    PAYMENT_FAIL
}

enum ShoppingCartState {
    BEGIN_STATE,
    SHOPPING_STATE,
    PAYMENT_STATE,
    SHIPPED_STATE;
}

@Configuration
@EnableStateMachineFactory
@Slf4j
class ShoppingStateMachineConfig extends EnumStateMachineConfigurerAdapter<ShoppingCartState, ShoppingCartEvent> {

    @Override
    public void configure(StateMachineStateConfigurer<ShoppingCartState, ShoppingCartEvent> states) throws Exception {
        states
                .withStates()
                .initial(ShoppingCartState.BEGIN_STATE)
                .end(ShoppingCartState.SHIPPED_STATE)
                .states(EnumSet.allOf(ShoppingCartState.class));
    }

    @Override
    public void configure(StateMachineTransitionConfigurer<ShoppingCartState, ShoppingCartEvent> transitions)
            throws Exception {
        transitions
                .withExternal()
                .source(ShoppingCartState.BEGIN_STATE)
                .target(ShoppingCartState.SHOPPING_STATE)
                .event(ShoppingCartEvent.ADD_ITEM)
                .action(initAction())
                .and()
                .withExternal()
                .source(ShoppingCartState.SHOPPING_STATE)
                .target(ShoppingCartState.SHOPPING_STATE)
                .event(ShoppingCartEvent.ADD_ITEM)
                .and()
                .withExternal()
                .source(ShoppingCartState.SHOPPING_STATE)
                .target(ShoppingCartState.PAYMENT_STATE)
                .event(ShoppingCartEvent.MAKE_PAYMENT)
                .and()
                .withExternal()
                .source(ShoppingCartState.PAYMENT_STATE)
                .target(ShoppingCartState.SHIPPED_STATE)
                .event(ShoppingCartEvent.PAYMENT_SUCESS)
                .and()
                .withExternal()
                .source(ShoppingCartState.PAYMENT_STATE)
                .target(ShoppingCartState.SHOPPING_STATE)
                .event(ShoppingCartEvent.PAYMENT_FAIL);
    }

    @Override
    public void configure(StateMachineConfigurationConfigurer<ShoppingCartState, ShoppingCartEvent> config)
            throws Exception {
        config
                .withConfiguration()
                .autoStartup(true)
                .listener(new GlobalStateMachineListener());
    }

    @Bean
    public Action<ShoppingCartState, ShoppingCartEvent> initAction() {
        log.info("init action called!");
        return ctx -> log.info("Id: {}", ctx.getTarget().getId());
    }
}

@Slf4j
class GlobalStateMachineListener extends StateMachineListenerAdapter<ShoppingCartState, ShoppingCartEvent> {
    @Override
    public void stateChanged(State<ShoppingCartState, ShoppingCartEvent> from, State<ShoppingCartState, ShoppingCartEvent> to) {
        log.info("State changed to : {}", to.getId());
    }
}
```


application.properties

```yaml
spring.main.web-application-type=none
```

build.gradle

```groovy
plugins {
    id 'org.springframework.boot' version '2.3.2.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'java'
}

group = 'com.demo'
version = '1.0.0'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
    maven { url "https://repo.spring.io/milestone" }
}

ext {
    springStatemachineVersion = '3.0.0.M2'
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.statemachine:spring-statemachine-bom:${springStatemachineVersion}"
    }
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    compile 'org.springframework.statemachine:spring-statemachine-starter'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

## References
[https://spring.io/projects/spring-statemachine](https://spring.io/projects/spring-statemachine)
