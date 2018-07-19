---
title: Reactive Web Development - Spring Webflux & Clarity UI
date: 2018-07-18 00:00:00
tags: Reactive Web,Reactor,Clarity UI,Angular,Docker,Reactive Mongo, Spring boot,
---

## Spring WebFlux Backend Service

We will now develop a reactive web application. We will use spring webflux,mongo database & clarity UI. We will also be using vscode as IDE for both java & angular development as it works seamlessly for both angular & java projects.

We will first create the backend rest services. Create the intial project using [start.spring.io](http://start.spring.io/). You can also use vscode 'Spring Initializr Java Support' plugin and generate the project. Cntrl+Shift+P on vscode IDE and key in 'Spring Initializer'.

{% asset_img image01.PNG %}

We will not create a rest controller which will get list of customers & save customer information,an initializer which will seed sample data,repository interface for JPA access, and a POJO class named Customer to hold the customer information.

```java
package com.demo.reactiveweb.service;

import java.time.Duration;
import java.util.Date;
import java.util.concurrent.TimeUnit;

import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.repository.ReactiveMongoRepository;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@SpringBootApplication
public class ServiceApplication {

  public static void main(String[] args) {
    SpringApplication.run(ServiceApplication.class, args);
  }
}

@RestController
@RequestMapping("/api")
@AllArgsConstructor
@Slf4j
class CustomerRestController {
  private final CustomerRepository repo;

  @GetMapping("/customers")
  public Flux<Customer> getCustomers() {
    log.info("get customers request!");
    return repo.findAll();
  }

  @PostMapping("/customers")
  public void save(@RequestBody Customer customer) {
    log.info("save request: {}", customer);
    repo.save(customer).subscribe();
  }

  @GetMapping("/hello")
  public Mono<String> hello() {
    log.info("hello world request!");
    return Mono.just("Hello World!");
  }
}

@Component
@AllArgsConstructor
@Slf4j
class Initializer implements ApplicationRunner {

  private final CustomerRepository repo;

  @Override
  public void run(ApplicationArguments args) throws Exception {
    log.info("Initializing repo!");
    Flux<String> names = Flux.just("ron", "raj", "david").delayElements(Duration.ofSeconds(2));
    Flux<String> colors = Flux.just("red", "blue", "green").delayElements(Duration.ofSeconds(3));
    Flux<Customer> customers = Flux.zip(names, colors).map(tupple -> {
      return new Customer(null, tupple.getT1(), new Date(), tupple.getT2());
    });

    repo.deleteAll().thenMany(customers.flatMap(repo::save).thenMany(repo.findAll())).subscribe(System.out::println);
    log.info("Main thread done!");
  }
}

interface CustomerRepository extends ReactiveMongoRepository<Customer, String> {
}

@Data
@Document
@AllArgsConstructor
@NoArgsConstructor
class Customer {
  @Id
  private String id;
  private String name;
  private Date creation;
  private String color;
}
```

Now run the command mentioned below, you should have a successfull build.

```
mvn clean install
```
We now need a data store to persist this data. The driver of the data store should support reactive api. Hence we choose reactive mongo. We will be using docker to stand up our mongo db instance. Ensure you have docker service running, run the command mentioned below.

```
docker run -p 27017:27017 -d mongo
```

You can now start the service

```
mvn spring-boot:run
```

This should bring up the service and you can now make a GET request in the browser to ensure that results are displayed. [http://localhost:8080/api/customers](http://localhost:8080/api/customers)

One of the advantages of adding dev tools in the pom.xml is that you can now make code changes on the java file and the changes get reloaded instantly. This allows for rapid development without having to follow a lengthy process to deploy the changes.

***

## Clarity UI Front End


We will now move on to the UI development using clarity  [Clartiy](https://vmware.github.io/clarity/). Clarity is an open source design system that brings together UX guidelines, an HTML/CSS framework, and Angular components. Ensure you have nodejs installed. [nodejs](https://nodejs.org/en/). You now have to install angular cli [Angular Cli](https://cli.angular.io/)

```
$ node --version
v9.11.1
$ npm --version
5.6.0
npm install -g @angular/cli
$ ng --version
Angular CLI: 6.0.8
```

You can now create your angular UI project.

```
$ ng new clarity-ui

$ cd clarity-ui

$ ng add @clr/angular@next
```

The project structure should look like

{% asset_img .left image02.PNG %}

Generate a home component, this will be your landing page.

```
$ ng generate component home
```

Generate the routing component.
```
$ ng generate module app-routing --flat --module=app
```

Now change the app-routing.module.ts. Notice the use of useHash:true this will be useful when we deploy the application in a single uber jar later. If we dont use this then the back button on the application will run into errors. It uses a hash based routing instead of the default location based routing.

```javascript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: true })],
  exports: [RouterModule]
})

export class AppRoutingModule { }
```

Now change the app.component.html

```html
<clr-main-container>
  <clr-header>
    <div class="branding">
      <a href="#" class="nav-link">
        <span class="title">My App</span>
      </a>
    </div>
      <div class="header-nav" [clr-nav-level]="1">
          <a class="nav-link" href="#" [routerLink]="['/home']" routerLinkActive="active"><span class="nav-text">Home</span></a>
      </div>
    <div class="header-actions">
    </div>
  </clr-header>
  <div class="content-container">
    <div class="content-area">
      <router-outlet></router-outlet>
    </div>
  </div>
</clr-main-container>

```

```
$ ng build

$ ng serve --open
```

Your angular application should now be accessible on [http://localhost:4200/](http://localhost:4200/)

{% asset_img .left image03.PNG %}

Create the entity class.
```
ng generate class customer
```
Change the customer.ts

```javascript
export class Customer {
  id: String;
  name: String;
  creation: Date;
  color: String;
}
```

Create the customer service class.
```
ng generate service customer
```
Change the customer.service.ts

```javascript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class CustomerService {

  constructor(private http: HttpClient) {
  }

  public getCustomers(): Observable<any> {
    return this.http.get('/api/customers');
  }

  public saveCustomer(customer) {
    return this.http.post('/api/customers', customer);
  }
}
```

Modify the app.module.ts to add imports of HttpClientModule,FormsModule. Providers array is also updated with the customer service provider we created.

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';
import { AppComponent } from './app.component';
import { ClarityModule } from '@clr/angular';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { AppRoutingModule } from './app-routing.module';
import { HomeComponent } from './home/home.component';
import { CustomerService } from './customer.service';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent
  ],
  imports: [
    BrowserModule,
    ClarityModule,
    BrowserAnimationsModule,
    AppRoutingModule,
    FormsModule,
    HttpClientModule
  ],
  providers: [CustomerService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
Change the home.component.ts to wire the service layer call.

```javascript
import { Component, OnInit } from '@angular/core';
import { CustomerService } from '../customer.service';
import { Customer } from '../customer';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {

  customers: String[] = [];
  customer: Customer;

  welcomeMessage = 'Welcome!';
  constructor(private customerService: CustomerService) {
  }

  ngOnInit() {
    this.loadData();
  }

  loadData(): void {
    this.customer = new Customer();
    this.customerService.getCustomers().subscribe(data => {
      console.log(data);
      this.customers = data;
    });
  }
  saveCustomer(): void {
    this.customerService.saveCustomer(this.customer)
      .subscribe(data => {
        console.log(data);
        this.loadData();
      });
  }
}
```

Now we will populate the customer details in the home page. Navigate to the clarity documentation and copy the code from the component data grid. You can look up similar snippets of code for other UI components. We have also added a form element to act as input. Your home.component.html should now look like

```html
<p>
  {{welcomeMessage}}
</p>
<clr-datagrid>
  <clr-dg-column>User ID</clr-dg-column>
  <clr-dg-column>Name</clr-dg-column>
  <clr-dg-column>Creation date</clr-dg-column>
  <clr-dg-column>Favorite color</clr-dg-column>
  <clr-dg-row *ngFor="let user of customers">
    <clr-dg-cell>{{user.id}}</clr-dg-cell>
    <clr-dg-cell>{{user.name}}</clr-dg-cell>
    <clr-dg-cell>{{user.creation | date}}</clr-dg-cell>
    <clr-dg-cell>{{user.color}}</clr-dg-cell>
  </clr-dg-row>
  <clr-dg-footer>{{customers.length}} users</clr-dg-footer>
</clr-datagrid>

<form>
  <section class="form-block">
    <label>Enter User</label>
    <div class="form-group">
      <label class="required">User Name</label>
      <input [(ngModel)]="customer.name" name="name" type="text" id="requiredInput">
    </div>
    <div class="form-group">
      <label for="creation">Creation</label>
      <input [(ngModel)]="customer.creation" name="creation" type="date" id="creation" size="35">
    </div>
    <div class="form-group">
      <label for="color">Color</label>
      <select id="color" name="color" [(ngModel)]="customer.color">
        <option>Red</option>
        <option>Blue</option>
        <option>Green</option>
      </select>
    </div>
    <button type="submit" class="btn btn-primary" (click)="saveCustomer()">Save</button>
  </section>
</form>

```

If the angular application running on port 4200 tries to access service layer rest API running on port 8080 we will get a CORS (Cross-Origin Resource Sharing) issue.A web application makes a cross-origin HTTP request when it requests a resource that has a different origin (domain, protocol, and port) than its own origin. We can use the annotation on spring application like @CrossOrigin(origins = "http://localhost:4200") but then this applies only to dev environment and we dont want to modify the source code. To address this we create a proxy. Create a proxy.config.json in clarity-ui folder. The proxy forwards all requests responses to the service layer.

```json
{
  "/api/*": {
    "target": "http://localhost:8080/",
    "secure": false,
    "logLevel": "debug"
  }
}
```

Stop the ng server and start it again but this time by providing the proxy file.

```
ng serve --proxy-config proxy.config.json --open
```

The application should now work, you should be able to add new customers.

{% asset_img .left image04.PNG %}

## Packaging

The next step is to bundle both the service layer and ui layer projects together into a single uber jar. You will be able to ship this jar and when it runs it will run the entire application. 

We will now package the angular project by running the below command. It create the final distribution folder with all the artifacts that can be deployed.

```
ng build --prod
```

Update the pom.xml in the service project

```xml
<plugin>
  <artifactId>maven-resources-plugin</artifactId>
  <executions>
    <execution>
      <id>copy-resources</id>
      <phase>validate</phase>
      <goals>
        <goal>copy-resources</goal>
      </goals>
      <configuration>
        <outputDirectory>${build.directory}/classes/static/</outputDirectory >
        <resources>
          <resource>
            <directory>../clarity-ui/dist</directory>
          </resource>
        </resources>
      </configuration>
    </execution>
  </executions>
</plugin>
```
The default location doesnt route to index.html in spring boot. To avoid running into this issue we will explicitly define a routing to index.html. Add the following code to ServiceApplication.class

```java
import org.springframework.core.io.Resource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.MediaType;
import org.springframework.web.reactive.function.server.RouterFunction;
import org.springframework.web.reactive.function.server.ServerResponse;
import static org.springframework.web.reactive.function.server.RequestPredicates.GET;
import static org.springframework.web.reactive.function.server.RouterFunctions.route;
import static org.springframework.web.reactive.function.server.ServerResponse.ok;

@Bean
  public RouterFunction<ServerResponse> indexRouter(@Value("classpath:/static/index.html") final Resource indexHtml) {
    return route(GET("/"), request -> ok().contentType(MediaType.TEXT_HTML).syncBody(indexHtml));
}
```

Run the following command on the service project.
```
$ mvn clean install

$ cd target

$ java -jar .\service-0.0.1-SNAPSHOT.jar
```
You should now have your web application running on [http://localhost:8080](http://localhost:8080)

## Docker Deployment


Now we will package this uber jar into a docker image. Till now mongo db is using the default parameter of hostname which is localhost. If we want to ship a docker image we would want to provide the actual hostname. Edit the application.properties file in the service project and add this line by providing the correct host details for the mongo db server. Note: If you provide IP address as localhost or loop back address the application will never connect to the database, as loop back address is within the context of the docker image and hence refers to self.

```
spring.data.mongodb.host=192.168.0.98
```

Build the service project again.

```
mvn clean install
```
Create a docker file Dockerfile in the service project

```bash
FROM java:8
WORKDIR /var/app
ADD target/service-*.jar /var/app/service.jar
ENTRYPOINT [ "java", "-jar", "/var/app/service.jar" ]
EXPOSE 8080
```

If you are using vscode you can right click the Dockerfile and trigger 'Build Image', else you can run the command

```
docker build --rm -f service\Dockerfile -t service:latest service
```
Please ensure that any previous server instances running on port 8080 are stopped else you will run into error as port is already in use.
If you are using vscode you can explore the newly created image in the images inventory under docker tab. You can run the docker image as well, else you can run the command

```
docker run --rm -d -p 8080:8080 service:latest
```

You should now have your web application running on [http://localhost:8080](http://localhost:8080) but this time being served as a docker container.

We will now deploy mongo db also as a docker image and orchestrate everything to get deployed as part of docker compose.

Create a docker-compose.yml file under the service directory

```yml
version : '3.6'
services:
  reactiveweb:
    build: .
    image: service
    container_name: "myservice"
    ports:
      - "8080:8080"
    links:
      - mongodb
    depends_on:
      - mongodb
  mongodb:
    image: mongo:latest
    container_name: "mongodb"
    ports: 
      - 27017:27017
    command: mongod
```

We need to point our mongo db hostname to the link name instead of the ip address. Change application.properties and build the service project again.
```
spring.data.mongodb.host=mongodb
```
```
$ mvn clean install
```
Ensure all previous instance of mongo and web application are stopped.
Either right click on docker-compose.yml file in vscode and trigger 'Compose Up' or run the command

```
docker-compose -f service\docker-compose.yml up -d --build
```

You should now have your web application running on [http://localhost:8080](http://localhost:8080) but this time the database & application deployed as docker container.

If you want to avoid modifying the application.properties file each time during development then you can pass the parameter during invocation
```
$ mvn spring-boot:run '-Dspring-boot.run.arguments=--spring.data.mongodb.host=192.168.0.98'
```

In the next blog we will add authentication & look at streaming support.

References
[Angular](https://angular.io/tutorial/)
[Clartiy](https://vmware.github.io/clarity/)
[Spring Boot](https://spring.io/projects/spring-boot)
[start.spring.io](http://start.spring.io/)
[Spring Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html)
