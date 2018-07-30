---
title: Design Patterns
date: 2018-07-29 00:00:00
tags: java design patterns
---

We will learn the java design patterns. Creational, Structural & Behavioural design patterns. 

<!-- more -->

The examples use lombok annotations for constructor,setter,getter methods to reduce the noise in the code. Add lombok to classpath or pom.xml if you want to run the examples. Now if there is a change in tax rules then we need to change just one class.

## Structural Design Patterns


## 6. Bridge Pattern

Bridge Pattern is used to decouple the interfaces from implementation. Prefer Composition over inheritance. There are interface hierarchies in both interfaces as well a implementations

First lets look at how a problematic code looks like and then refractor it to bridge pattern.

```java
package com.tutor.designpattern.bridge.badway;

public class Main {

  public static void main(String[] args) {
    PullSwitch switch1 = new PullSwitchFan();
    PressSwitch switch2 = new PressSwitchLight();
    switch1.toggle();
    switch2.toggle();
  }
}

abstract class Switch {
  abstract public void toggle();
}

abstract class PullSwitch extends Switch {
}

abstract class PressSwitch extends Switch {
}

class PullSwitchFan extends PullSwitch {

  boolean state;

  @Override
  public void toggle() {
    if (state) {
      System.out.println("Pulled Switch, Now turning off fan");
      state = Boolean.FALSE;
    } else {
      System.out.println("Pulled Switch, Now turning on fan");
      state = Boolean.TRUE;
    }
  }
}

class PullSwitchLight extends PullSwitch {

  boolean state;

  @Override
  public void toggle() {
    if (state) {
      System.out.println("Pulled Switch, Now turning off light");
      state = Boolean.FALSE;
    } else {
      System.out.println("Pulled Switch, Now turning on light");
      state = Boolean.TRUE;
    }
  }
}

class PressSwitchFan extends PressSwitch {

  boolean state;

  @Override
  public void toggle() {
    if (state) {
      System.out.println("Pressed Switch, Now turning off fan");
      state = Boolean.FALSE;
    } else {
      System.out.println("Pressed Switch, Now turning on fan");
      state = Boolean.TRUE;
    }
  }
}

class PressSwitchLight extends PressSwitch {

  boolean state;

  @Override
  public void toggle() {
    if (state) {
      System.out.println("Pressed Switch, Now turning off light");
      state = Boolean.FALSE;
    } else {
      System.out.println("Pressed Switch, Now turning on light");
      state = Boolean.TRUE;
    }
  }
}
```

Output:

```bash
Pulled Switch, Now turning on fan
Pressed Switch, Now turning on light
```

UML Diagram of problematic code, you can see that heirarchy exists.

{% asset_img bridge-bad.PNG %}

As you see in the above problem adding a new switch or electric device quickly causes the code to become large and unmanageable. Now lets refractor it to bridge pattern.

```java
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {
    Switch switch1 = new PullSwitch(new Light());
    switch1.toggle();
    System.out.println("----------------");
    Switch switch2 = new PressSwitch(new Fan());
    switch2.toggle();
  }

}

interface ElectricDevice {
  public void doSomething();
}

class Fan implements ElectricDevice {

  @Override
  public void doSomething() {
    System.out.println("Fan!");
  }
}

class Light implements ElectricDevice {

  @Override
  public void doSomething() {
    System.out.println("Light!");
  }
}

@AllArgsConstructor
abstract class Switch {

  protected ElectricDevice eDevice;

  public abstract void toggle();
}

class PressSwitch extends Switch {

  boolean state;

  public PressSwitch(ElectricDevice d) {
    super(d);
  }

  @Override
  public void toggle() {
    if (state) {
      System.out.print("Pressed Switch, Now turning off :");
      eDevice.doSomething();
      state = Boolean.FALSE;
    } else {
      System.out.print("Pressed Switch, Now turning on :");
      eDevice.doSomething();
      state = Boolean.TRUE;
    }
  }
}

class PullSwitch extends Switch {

  boolean state;

  public PullSwitch(ElectricDevice d) {
    super(d);
  }

  @Override
  public void toggle() {
    if (state) {
      System.out.print("Pulled Switch, Now turning off :");
      eDevice.doSomething();
      state = Boolean.FALSE;
    } else {
      System.out.print("Pulled Switch, Now turning on :");
      eDevice.doSomething();
      state = Boolean.TRUE;
    }
  }
}

```

Output:

```bash
Pulled Switch, Now turning on :Light!
----------------
Pressed Switch, Now turning on :Fan!
```

With the change we can now add new switches or new electric devices without increasing complexity.

UML of Bridge Pattern. There is a bridge between Switch class and ElectricDevice class.

{% asset_img bridge.PNG %}

## 7. Decorator Pattern

Decorator design pattern is used to modify the functionality of an object at runtime. Disadvantage of decorator pattern is that it uses a lot of similar kind of objects

```java
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {
    Car sportsCar = new SportsCar(new BasicCar());
    sportsCar.assemble();
    System.out.println("--------------------------");
    Car sportsLuxuryCar = new SportsCar(new LuxuryCar(new BasicCar()));
    sportsLuxuryCar.assemble();
  }

}

interface Car {

  public void assemble();
}

class BasicCar implements Car {

  @Override
  public void assemble() {
    System.out.println("Basic Car assembled.");
  }
}

@AllArgsConstructor
abstract class CarDecorator implements Car {

  protected Car car;

  @Override
  public void assemble() {
    System.out.println("Decorator");
    this.car.assemble();
  }

}

class LuxuryCar extends CarDecorator {

  public LuxuryCar(Car c) {
    super(c);
  }

  @Override
  public void assemble() {
    car.assemble();
    System.out.println("Adding features of Luxury Car.");
  }

}

class SportsCar extends CarDecorator {

  public SportsCar(Car c) {
    super(c);
  }

  @Override
  public void assemble() {
    car.assemble();
    System.out.println("Adding features of Sports Car.");
  }

}

```

Output:

```bash
Basic Car assembled.
Adding features of Sports Car.
--------------------------
Basic Car assembled.
Adding features of Luxury Car.
Adding features of Sports Car.
```

UML of Decorator Pattern

{% asset_img decorator.PNG %}

## Behavioural Design Patterns

Behavioral patterns help design classes with better interaction between objects and provide lose coupling.

### 1. Template Pattern

Template Pattern used to create a method stub and deferring some of the steps of implementation to the subclasses. Template method defines the steps to execute an algorithm and it can provide default implementation that might be common for all or some of the subclasses.

```java
public class Main {

    public static void main(String[] args) {
        HouseTemplate houseType = new WoodenHouse();
        houseType.buildHouse();
        System.out.println("-------------------------");
        houseType = new GlassHouse();
        houseType.buildHouse();
    }
}
class GlassHouse extends HouseTemplate {

  @Override
  public void buildWalls() {
      System.out.println("Building Glass Walls");
  }

  @Override
  public void buildPillars() {
      System.out.println("Building Pillars with glass coating");
  }
}

class WoodenHouse extends HouseTemplate{

  @Override
  public void buildWalls() {
      System.out.println("Building Wooden Walls");
  }

  @Override
  public void buildPillars() {
      System.out.println("Building Pillars with Wood coating");
  }
  
}


abstract class HouseTemplate {

  //template method, final so subclasses can't override
  public final void buildHouse() {
      buildFoundation();
      buildPillars();
      buildWalls();
      buildWindows();
      System.out.println("House is built.");
  }

  //default implementation
  private void buildWindows() {
      System.out.println("Building Glass Windows");
  }

  //methods to be implemented by subclasses
  public abstract void buildWalls();
  public abstract void buildPillars();

  private void buildFoundation() {
      System.out.println("Building foundation with cement,iron rodsand sand");
  }
}
```

Output:

```bash
Building foundation with cement,iron rodsand sand
Building Pillars with Wood coating
Building Wooden Walls
Building Glass Windows
House is built.
-------------------------
Building foundation with cement,iron rodsand sand
Building Pillars with glass coating
Building Glass Walls
Building Glass Windows
House is built.

```

### 2. Mediator Pattern

Mediator pattern is used to provide a centralized communication medium between different objects.

```java
import java.util.ArrayList;
import java.util.List;
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {
    ChatMediator mediator = new ChatMediatorImpl();
    User user1 = new User(mediator, "Raj");
    User user2 = new User(mediator, "Jacob");
    User user3 = new User(mediator, "Henry");
    User user4 = new User(mediator, "Stan");
    mediator.addUser(user1);
    mediator.addUser(user2);
    mediator.addUser(user3);
    mediator.addUser(user4);
    user1.send("Hi All");

  }
}

interface ChatMediator {

  public void sendMessage(String msg, User user);

  void addUser(User user);
}

class ChatMediatorImpl implements ChatMediator {

  private List<User> users = new ArrayList<>();

  @Override
  public void addUser(User user) {
    this.users.add(user);
  }

  @Override
  public void sendMessage(String msg, User user) {
    for (User u : this.users) {
      if (u != user) {
        u.receive(msg);
      }
    }
  }
}

@AllArgsConstructor
class User {

  private ChatMediator mediator;
  private String name;

  public void send(String msg) {
    System.out.println(this.name + ": Sending Message=" + msg);
    mediator.sendMessage(msg, this);
  }

  public void receive(String msg) {
    System.out.println(this.name + ": Received Message:" + msg);
  }
}

```

Output:

```bash
Raj: Sending Message=Hi All
Jacob: Received Message:Hi All
Henry: Received Message:Hi All
Stan: Received Message:Hi All
```

### 3. Chain of Responsibility Pattern

Chain of responsibility pattern is used when a request from client is passed to a chain of objects to process them

```java
import lombok.AllArgsConstructor;
import lombok.Data;

public class Main {

  public static void main(String[] args) {
    ATMDispenseChain atmDispenser = new ATMDispenseChain();
    int amount = 530;
    if (amount % 10 != 0) {
      System.out.println("Amount should be in multiple of10s.");
    }else {
      atmDispenser.c1.dispense(new Currency(amount));  
    }
  }
}

class ATMDispenseChain {

  public DispenseChain c1;

  public ATMDispenseChain() {
    
    DispenseChain c1 = new Dollar50Dispenser();
    DispenseChain c2 = new Dollar20Dispenser();
    DispenseChain c3 = new Dollar10Dispenser();
    
    this.c1 = c1;
    c1.setNextChain(c2);
    c2.setNextChain(c3);
  }

}

@AllArgsConstructor
@Data
class Currency {
  private int amount;
}

interface DispenseChain {

  void setNextChain(DispenseChain nextChain);

  void dispense(Currency cur);
}

class Dollar10Dispenser implements DispenseChain {

  private DispenseChain chain;

  @Override
  public void setNextChain(DispenseChain nextChain) {
    this.chain = nextChain;
  }

  @Override
  public void dispense(Currency cur) {
    if (cur.getAmount() >= 10) {
      int num = cur.getAmount() / 10;
      int remainder = cur.getAmount() % 10;
      System.out.println("Dispensing " + num + " 10$ note");
      if (remainder != 0) {
        this.chain.dispense(new Currency(remainder));
      }
    } else {
      this.chain.dispense(cur);
    }
  }
}

class Dollar20Dispenser implements DispenseChain {

  private DispenseChain chain;

  @Override
  public void setNextChain(DispenseChain nextChain) {
    this.chain = nextChain;
  }

  @Override
  public void dispense(Currency cur) {
    if (cur.getAmount() >= 20) {
      int num = cur.getAmount() / 20;
      int remainder = cur.getAmount() % 20;
      System.out.println("Dispensing " + num + " 20$ note");
      if (remainder != 0) {
        this.chain.dispense(new Currency(remainder));
      }
    } else {
      this.chain.dispense(cur);
    }
  }
}

class Dollar50Dispenser implements DispenseChain {

  private DispenseChain chain;

  @Override
  public void setNextChain(DispenseChain nextChain) {
    this.chain = nextChain;
  }

  @Override
  public void dispense(Currency cur) {
    if (cur.getAmount() >= 50) {
      int num = cur.getAmount() / 50;
      int remainder = cur.getAmount() % 50;
      System.out.println("Dispensing " + num + " 50$ note");
      if (remainder != 0) {
        this.chain.dispense(new Currency(remainder));
      }
    } else {
      this.chain.dispense(cur);
    }
  }
}
```

Output:

```bash
Dispensing 10 50$ note
Dispensing 1 20$ note
Dispensing 1 10$ note
```

### 4. Observer Pattern

Observer design pattern is used when we want to get notified about state changes of a object. An Observer watches the Subject here and any changes on Subject are notified to the Observer.

```java
import java.util.ArrayList;
import java.util.List;

public class Main {

  public static void main(String[] args) {
    Feed feed = new Feed();
    feed.registerObserver(new AppleStockObserver());
    feed.registerObserver(new GoogleStockObserver());
    feed.notifyObservers("APPL: 162.33");
    feed.notifyObservers("GOOGL: 1031.22");
  }
}

class AppleStockObserver implements Observer {
  @Override
  public void notify(String tick) {
    if (tick != null && tick.contains("APPL")) {
      System.out.println("Apple Stock Price: " + tick);
    }
  }
}

class GoogleStockObserver implements Observer {
  @Override
  public void notify(String tick) {
    if (tick != null && tick.contains("GOOGL")) {
      System.out.println("Google Stock Price: " + tick);
    }
  }
}

class Feed implements Subject {
  List<Observer> observerLst = new ArrayList<>();

  @Override
  public void registerObserver(Observer observer) {
    observerLst.add(observer);
  }

  @Override
  public void notifyObservers(String tick) {
    observerLst.forEach(e -> e.notify(tick));
  }
}

interface Observer {
  public void notify(String tick);
}

interface Subject {
  public void registerObserver(Observer observer);

  public void notifyObservers(String tick);
}
```

Output:

```bash
Apple Stock Price: APPL: 162.33
Google Stock Price: GOOGL: 1031.22
```

### 5. Strategy Pattern

Strategy pattern is used when we have multiple algorithm for a specific task and client decides the actual implementation to be used at runtime. This is also known as Policy Pattern.

```java
public class Main {

  public static void main(String[] args) {
    new ShoppingCart().pay(new CreditCardStrategy(), 10);
    new ShoppingCart().pay(new PaypalStrategy(), 10);
  }
}

class CreditCardStrategy implements PaymentStrategy {

  @Override
  public void pay(int amount) {
    System.out.println("Paid by creditcard: " + amount);
  }

}

class PaypalStrategy implements PaymentStrategy {

  @Override
  public void pay(int amount) {
    System.out.println("Paid by paypall: " + amount);
  }

}

class ShoppingCart {

  public void pay(PaymentStrategy paymentMethod, Integer amount) {
    paymentMethod.pay(amount);
  }
}

interface PaymentStrategy {

  public void pay(int amount);
}
```

Output:

```bash
Paid by creditcard: 10
Paid by paypall: 10
```

### 6. Command Pattern

Command pattern is used when request is wrapped and passed to invoker which then inturn invokes the encapsulated command. Here Command is our command interface, Stock class is our request. BuyStock and SellStock implementing Order interface which does the actual command processing.

```java
import java.util.ArrayList;
import java.util.List;
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {

    Stock stock1 = new Stock("GOOGL", 10);
    Stock stock2 = new Stock("IBM", 20);

    BuyStock buyStockCmd = new BuyStock(stock1);
    SellStock sellStockCmd = new SellStock(stock2);

    Broker broker = new Broker();
    broker.takeOrder(buyStockCmd);
    broker.takeOrder(sellStockCmd);

    broker.placeOrders();

  }
}

interface Command {
  void execute();
}

@AllArgsConstructor
class Stock {

  private String name;
  private int quantity;

  public void buy() {
    System.out.println("Stock [ Name: " + name + ", Quantity: " + quantity + " ] bought");
  }

  public void sell() {
    System.out.println("Stock [ Name: " + name + ", Quantity: " + quantity + " ] sold");
  }
}

@AllArgsConstructor
class BuyStock implements Command {
  private Stock stock;

  public void execute() {
    stock.buy();
  }
}

@AllArgsConstructor
class SellStock implements Command {
  private Stock stock;

  public void execute() {
    stock.sell();
  }
}

class Broker {
  private List<Command> cmdLst = new ArrayList<Command>();

  public void takeOrder(Command cmd) {
    cmdLst.add(cmd);
  }

  public void placeOrders() {
    for (Command cmd : cmdLst) {
      cmd.execute();
    }
    cmdLst.clear();
  }
}
```

Output:

```bash
Stock [ Name: GOOGL, Quantity: 10 ] bought
Stock [ Name: IBM, Quantity: 20 ] sold
```

### 7. State Pattern

State pattern is used when object changes its behaviour based on internal state. You avoid writing the conditional if-else logic to determine the type of action to be taken based on state of object. Notice that GameContext also implements State along with StartState,StopState classes.


```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

public class Main {
  public static void main(String[] args) {
    GameContext game = new GameContext();

    StartState startState = new StartState();
    StopState stopState = new StopState();

    game.setState(startState);
    game.doAction();

    game.setState(stopState);
    game.doAction();
  }

}

interface State {
  public void doAction();
}

class StartState implements State {

  public void doAction() {
    System.out.println("Roll the dice!");
  }
}

class StopState implements State {

  public void doAction() {
    System.out.println("Game Over!");
  }
}

@AllArgsConstructor
@NoArgsConstructor
@Data
class GameContext implements State {
  private State state;

  @Override
  public void doAction() {
    this.state.doAction();
  }
}
```

Output:

```bash
Roll the dice!
Game Over!
```

### 8. Visitor Pattern

Visitor pattern is used to add methods to different types of classes without altering those classes. Here we have moved the tax calculation outside each item.

```java

import lombok.AllArgsConstructor;
import lombok.Data;

public class Main {
  public static void main(String[] args) {

    Visitor taxCalculator = new TaxVisitor();
    Liquor liquor = new Liquor("Black Dog", 12.00d);
    System.out.println("Price of liquor: " + liquor.accept(taxCalculator));

    Grocery grocery = new Grocery("Potato Chips", 12.00d);
    System.out.println("Price of grocery: " + grocery.accept(taxCalculator));

  }
}

interface Visitable {
  public double accept(Visitor visitor);
}

@AllArgsConstructor
@Data
class Liquor implements Visitable {
  String name;
  double price;

  @Override
  public double accept(Visitor visitor) {
    return visitor.visit(this);
  }
}

@AllArgsConstructor
@Data
class Grocery implements Visitable {
  String name;
  double price;

  @Override
  public double accept(Visitor visitor) {
    return visitor.visit(this);
  }
}

interface Visitor {
  public double visit(Liquor item);

  public double visit(Grocery item);
}

class TaxVisitor implements Visitor {

  @Override
  public double visit(Liquor item) {
    return item.price * .30 + item.price;
  }

  @Override
  public double visit(Grocery item) {
    return item.price * .10 + item.price;
  }
}

```

Output:

```bash
Price of liquor: 15.6
Price of grocery: 13.2
```

### 9. Interpreter Pattern

Interpreter pattern provides a way to evaluate language grammar or expression.

```java
import lombok.AllArgsConstructor;
import lombok.Data;

public class Main {
  public static void main(String[] args) {
    String input = "30 in binary";
    if(input.contains("binary")) {
      int val = Integer.parseInt(input.substring(0,input.indexOf(" ")));
      System.out.println(new IntToBinaryExpression(val).interpret(new InterpreterContext()));
    }
    
    input = "30 in hexadecimal";
    if(input.contains("hexadecimal")) {
      int val = Integer.parseInt(input.substring(0,input.indexOf(" ")));
      System.out.println(new IntToHexExpression(val).interpret(new InterpreterContext()));
    }
  }
  
}

class InterpreterContext {
  public String getBinaryFormat(int val) {
    return Integer.toBinaryString(val);
  }

  public String getHexFormat(int val) {
    return Integer.toHexString(val);
  }
}

interface Expression {
  String interpret(InterpreterContext ctx);
}

@Data
@AllArgsConstructor
class IntToBinaryExpression implements Expression {

  int val;

  @Override
  public String interpret(InterpreterContext ctx) {
    return ctx.getBinaryFormat(val);
  }
}

@Data
@AllArgsConstructor
class IntToHexExpression implements Expression {

  int val;

  @Override
  public String interpret(InterpreterContext ctx) {
    return ctx.getHexFormat(val);
  }
}
```

Output:

```bash
11110
1e
```

### 10. Iterator Pattern

Iterator pattern is used to provide standard way to traverse through group of objects. In the example below we provide 2 types of iterators over the fruit collection, we could have let the user write his own iterator but if there are many clients using the iterator then it would be difficult to maintain. Notice that FruitIterator is private and inner class, this hides the implementation details from the client. Logic of iteration is internal to the collection.
 
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.Iterator;
import java.util.List;
import lombok.AllArgsConstructor;
import lombok.Data;

public class Main {
  public static void main(String[] args) {

    FruitCollectionImpl collection = new FruitCollectionImpl();

    for (Iterator iter = collection.getIterator("COLOR"); iter.hasNext();) {
      Fruit fruit = (Fruit) iter.next();
      System.out.println(fruit);
    }
    System.out.println("-------------------------------");
    for (Iterator iter = collection.getIterator("TYPE"); iter.hasNext();) {
      Fruit fruit = (Fruit) iter.next();
      System.out.println(fruit);
    }
  }
}

@AllArgsConstructor
@Data
class Fruit {
  String type;
  String color;
}

interface FruitCollection {
  public Iterator getIterator(String type);
}

class FruitCollectionImpl implements FruitCollection {

  List<Fruit> fruits;

  FruitCollectionImpl() {
    fruits = new ArrayList<>();
    fruits.add(new Fruit("Banana", "Green"));
    fruits.add(new Fruit("Apple", "Green"));
    fruits.add(new Fruit("Banana", "Yellow"));
    fruits.add(new Fruit("Cherry", "Red"));
    fruits.add(new Fruit("Apple", "Red"));
  }

  @Override
  public Iterator getIterator(String type) {
    if (type.equals("COLOR")) {
      return new FruitIterator("COLOR");
    } else {
      return new FruitIterator("TYPE");
    }
  }

  private class FruitIterator implements Iterator {
    int index;
    List<Fruit> sortedFruits = new ArrayList<>(fruits);

    FruitIterator(String iteratorType) {
      if (iteratorType.equals("COLOR")) {
        Collections.sort(sortedFruits, Comparator.comparing(Fruit::getColor));
      } else {
        Collections.sort(sortedFruits, Comparator.comparing(Fruit::getType));
      }
    }

    @Override
    public boolean hasNext() {
      if (index < sortedFruits.size()) {
        return true;
      }
      return false;
    }

    @Override
    public Object next() {
      if (this.hasNext()) {
        return sortedFruits.get(index++);
      }
      return null;
    }
  }
}
```

Output:

```bash
Fruit(type=Banana, color=Green)
Fruit(type=Apple, color=Green)
Fruit(type=Cherry, color=Red)
Fruit(type=Apple, color=Red)
Fruit(type=Banana, color=Yellow)
-------------------------------
Fruit(type=Apple, color=Green)
Fruit(type=Apple, color=Red)
Fruit(type=Banana, color=Green)
Fruit(type=Banana, color=Yellow)
Fruit(type=Cherry, color=Red)
```

### 11. Memento Pattern

Memento pattern is used to restore state of an object to a previous state.

Memento pattern involves three classes.
- Originator: The core class which holds a state. This state will need to be reverted to previous states. Think of this as your text editor text data.
- Memento: The class has all the same attributes as Originator class and is used to hold values that will be restored back to the Originator class. Think of this as a temporary variable. Each time you click on save a memento is created and added to the list so that it can be reverted later.
- CareTaker - This class takes ownership of creating and restoring memento.

In the example below you can create a Originator object and change its state many times, only when you call the CareTaker.save method a memento gets created so that an undo operation later on can revert to that state. The list mementoList is private so only caretaker has access to the memento objects ensuring integrity of data. 
Take special care if the attribute is immutable in the undoState method.


```java
import java.util.ArrayList;
import java.util.List;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.RequiredArgsConstructor;

public class Main {

  public static void main(String[] args) {

    Originator originator = new Originator();
    CareTaker careTaker = new CareTaker(originator);
    careTaker.save();
    
    originator.setState("State #1");
    originator.setState("State #2");
    careTaker.save();

    originator.setState("State #3");
    careTaker.save();

    originator.setState("State #4");
    System.out.println("Current State: " + originator.getState());

    careTaker.undo();
    System.out.println("Current State: " + originator.getState());

    careTaker.undo();
    System.out.println("Current State: " + originator.getState());

    careTaker.undo();
    careTaker.undo();
    careTaker.undo();
    System.out.println("Current State: " + originator.getState());
  }
}

@Data
@AllArgsConstructor
class Memento {
  private String state;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class Originator {
  private String state;

  public Memento saveState() {
    return new Memento(this.state);
  }

  public void undoState(Memento memento) {
    this.state = memento.getState();
  }

}

@RequiredArgsConstructor
class CareTaker {
  private List<Memento> mementoList = new ArrayList<Memento>();
  final Originator origin;

  public void save() {
    if(origin.getState() != null) {
      mementoList.add(origin.saveState());  
    }
  }

  public void undo() {
    if (!mementoList.isEmpty()) {
      origin.undoState(mementoList.get(mementoList.size() - 1));
      mementoList.remove(mementoList.size() - 1);
    }
  }
}
```

Output:

```bash
Current State: State #4
Current State: State #3
Current State: State #2
Current State: State #2

```

## Differences

#### 1. Difference between bridge pattern and adapter pattern
Bridge pattern is built upfront you break things at design time to make changes so that functionality can be added without tight coupling, adapter pattern works after code is already designed like legacy code. We are not adding new code in adapter pattern but we are just providing different interface.

#### 2. Difference between mediator pattern and observer pattern
In observer, many objects are interested in the state change of one object. They are not interested in each other. So the relation is one to many. In mediator, many objects are interested to communicate many other objects. Here the relation is many to many.


#### 3. Difference between chain of responsibility and command pattern
In chain of responsibility pattern, the request is passed to potential receivers, whereas the command pattern uses a command object that encapsulates a request.

