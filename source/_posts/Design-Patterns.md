---
title: Design Patterns
date: 2018-07-29 00:00:00
tags: java design patterns
---

We will learn the java design patterns. Creational, Structural & Behavioral design patterns. 

<!-- more -->

<!-- toc -->


The examples use lombok annotations for constructor,setter,getter methods to reduce the noise in the code. Add [lombok](https://mvnrepository.com/artifact/org.projectlombok/lombok) to classpath or pom.xml if you want to run the examples.

## Creational Design Patterns

Provides way to create objects while hiding the creation logic.

### 1. Singleton Pattern

Singleton pattern ensures that only one instance of the class exists in the java virtual machine.

A singleton class has these common features
- private constructor to restrict creation of instance by other classes.
- private static variable of the same class.
- public static method to get instance of class.

We will first look at eager loaded singleton. This is costly as object is created at time of class loading,also no scope for exception handling if instantiation fails.

```java
public class EagerLoadedSingleton {

  public static void main(String[] args) {
    System.out.println(EagerLoadedSingleton.getInstance().hello());
  }

  private static final EagerLoadedSingleton instance = new EagerLoadedSingleton();

  private EagerLoadedSingleton() {
  }

  public static EagerLoadedSingleton getInstance() {
    return instance;
  }

  public String hello() {
    return ("Hello from EagerLoadedSingleton!");
  }
}
```

This can be modified to static block singleton which provides room for handling exception.

```java
public class StaticBlockSingleton {
  
  public static void main(String[] args) {
    System.out.println(StaticBlockSingleton.getInstance().hello());
  }

  private static final StaticBlockSingleton instance;

  private StaticBlockSingleton() {
  }

  static {
    try {
      instance = new StaticBlockSingleton();
    } catch (Exception e) {
      throw new RuntimeException("Exception occured in creatingsingleton instance");
    }
  }

  public static StaticBlockSingleton getInstance() {
    return instance;
  }

  public String hello() {
    return ("Hello from StaticBlockSingleton!");
  }
}
```

The next step is to use lazy initialization singleton as creating singleton at class loading time and not using it will be costly.

```java
public class LazyLoadedSingleton {

  public static void main(String[] args) {
    System.out.println(LazyLoadedSingleton.getInstance().hello());
  }

  private static LazyLoadedSingleton instance;

  private LazyLoadedSingleton() {
  }

  public static LazyLoadedSingleton getInstance() {
    if (instance == null) {
      instance = new LazyLoadedSingleton();
    }
    return instance;
  }

  public String hello() {
    return ("Hello from LazyLoadedSingleton!");
  }
}
```

However this is not thread safe as in multithread environment 2 threads can get 2 different instances of the object. So lets make this thread safe. Notice we introduced synchronized keyword on the getInstance method.

```java
public class ThreadSafeSingleton {

  public static void main(String[] args) {
    System.out.println(ThreadSafeSingleton.getInstance().hello());
  }

  private static ThreadSafeSingleton instance;

  private ThreadSafeSingleton() {
  }

  public static synchronized ThreadSafeSingleton getInstance() {
    if (instance == null) {
      instance = new ThreadSafeSingleton();
    }
    return instance;
  }

  public String hello() {
    return ("Hello from ThreadSafeSingleton!");
  }
}
```
The above program is thread safe but reduces performance as each thread waits to enter the synchronized block. We now fix that by introducing double check locking. Notice that we removed the synchronized keyword on the getInstance method and moved it inside the method. We now perform 2 if checks on the instance.

```java
public class ThreadSafeSingletonDoubleCheckLock {

  public static void main(String[] args) {
    System.out.println(ThreadSafeSingletonDoubleCheckLock.getInstance().hello());
  }

  private static ThreadSafeSingletonDoubleCheckLock instance;

  private ThreadSafeSingletonDoubleCheckLock() {
  }

  public static ThreadSafeSingletonDoubleCheckLock getInstance() {
    if (instance == null) {
      synchronized (ThreadSafeSingletonDoubleCheckLock.class) {
        if (instance == null) {
          instance = new ThreadSafeSingletonDoubleCheckLock();
        }
      }

    }
    return instance;
  }

  public String hello() {
    return ("Hello from ThreadSafeSingleton!");
  }
}
```

Using reflection all previous singleton implementation can be broken

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class BreakSingletonByReflection {

  public static void main(String[] args) {
    new BreakSingletonByReflection().testSingleton();
  }

  public void testSingleton() {

    ThreadSafeSingletonDoubleCheckLock instanceOne = ThreadSafeSingletonDoubleCheckLock.getInstance();
    ThreadSafeSingletonDoubleCheckLock instanceTwo = null;
    try {
      Constructor[] constructors = ThreadSafeSingletonDoubleCheckLock.class.getDeclaredConstructors();
      for (Constructor constructor : constructors) {
        constructor.setAccessible(true);
        instanceTwo = (ThreadSafeSingletonDoubleCheckLock) constructor.newInstance();
        break;
      }
    } catch (InstantiationException | IllegalAccessException | IllegalArgumentException
        | InvocationTargetException ex) {
      ex.printStackTrace();
    }
    if (instanceOne.hashCode() != instanceTwo.hashCode()) {
      System.out.println("Singleton broken!");
    }
  }
}
```

To safegaurd against reflection we will throw RuntimeException in the constructor. We will introduce the volatile keyword to make it even more thread safe.

How volatile works in java?
The volatile keyword in Java is used as an indicator to Java compiler and Thread that do not cache value of this variable and always read it from main memory. Java volatile keyword also guarantees visibility and ordering, write to any volatile variable happens before any read into the volatile variable. It also prevents compiler or JVM from the reordering of code.

If we do not make the instance variable volatile than the Thread which is creating instance of Singleton is not able to communicate to the other thread, that the instance has been created until it comes out of the Singleton block, so if Thread A is creating Singleton instance and just after creation lost the CPU, all other thread will not be able to see value of instance as not null and they will believe its still null. By adding volatile java will not read the variable into thread context local memory and instead read it from the main memory each time.

{% asset_img volatile-memory-model.PNG %}

```java
public class SingletonDefendReflection {

  public static void main(String[] args) {
    System.out.println(SingletonDefendReflection.getInstance().hello());
  }

  private static volatile SingletonDefendReflection instance;

  private SingletonDefendReflection() {
    if(instance != null) {
      throw new RuntimeException("Use get instance to create object!");
    }
  }

  public static SingletonDefendReflection getInstance() {
    if (instance == null) {
      synchronized (SingletonDefendReflection.class) {
        if (instance == null) {
          instance = new SingletonDefendReflection();
        }
      }
    }
    return instance;
  }

  public String hello() {
    return ("Hello from ThreadSafeSingleton!");
  }
}
```

To defend against reflection you can also use Enum based singleton, The disadvantage is you cant do lazy loading, you cant extend the singleton.

```java
public enum EnumSingleton {
  
    INSTANCE;
  
    public static void main(String[] args) {
      System.out.println(EnumSingleton.INSTANCE.hello()); 
    }
    
    public String hello() {
        return ("Hello from EnumSingleTon!");
    }
}
```

There is another approach of writing a singleton called Bill Pugh Singleton implementation which uses static inner helper class instead of using synchronized keyword.

```java
public class BillPughSingleton {

    public static void main(String[] args) {
      BillPughSingleton.getInstance().hello();
    }

    private BillPughSingleton() {
    }

    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }

    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }

    public String hello() {
        return "Hello from BillPughSingleton";
    }
}

```

In a distributed systems a singleton needs to be serialized and restored from store later and care must be taken to ensure that new instance is not created and the same instance that was serialized is restored. Notice the method readResolve if this method is removed then the singleton design breaks during de-serialization.

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInput;
import java.io.ObjectInputStream;
import java.io.ObjectOutput;
import java.io.ObjectOutputStream;
import java.io.Serializable;

public class SerializedSingleton implements Serializable {

  private static final long serialVersionUID = -1L;

  public static void main(String[] args) throws Exception {

    SerializedSingleton instanceOne = SerializedSingleton.getInstance();
    ObjectOutput out = new ObjectOutputStream(new FileOutputStream("filename.ser"));
    out.writeObject(instanceOne);
    out.close();

    ObjectInput in = new ObjectInputStream(new FileInputStream("filename.ser"));
    SerializedSingleton instanceTwo = (SerializedSingleton) in.readObject();
    in.close();
    if (instanceOne.hashCode() != instanceTwo.hashCode()) {
      System.out.println("Singleton broken!");
    }else {
      System.out.println(instanceOne.getInstance().hello());
    }
    
  }

  private SerializedSingleton() {
  }

  private static class SingletonHelper {
    private static final SerializedSingleton instance = new SerializedSingleton();
  }

  public static SerializedSingleton getInstance() {
    return SingletonHelper.instance;
  }

  public String hello() {
    return ("Hello from singleton!");
  }

  protected Object readResolve() {
    return getInstance();
  }

}
```

A singleton example within java sdk is the Runtime class for garbage collection.

```java
public class RuntimeSingleton {
  public static void main(String[] args) {
    Runtime singleton1 = Runtime.getRuntime();
    singleton1.gc();
    Runtime singleton2 = Runtime.getRuntime();
    if(singleton1 == singleton2) {
      System.out.println("Singleton!");
    }else {
      System.out.println("Not Singleton!");
    }
  }
}
```

Why not use a static class instead of writing a singleton class?
Because static class doesnt guarantee thread safety.

Can i have parameters in a singleton?
A singleton constructor cant take parameters that violates the rule of singleton. If there are parameters then it classifies as a factory pattern.

If singleton is unique instance per JVM instance how does it work in a tomcat server which can have 2 instances of same web application deployed on it. Since the applications still run on single JVM will they share the singleton?
In this case both web applications will get their own instance of singleton because of class loader visibility.Tomcat uses individual class loaders for webapps. However if both application request a JRE or Tomcat singleton eg: Runtime then both get the same singleton.


### 2. Factory Pattern

Factory design pattern is used when we have a super class with multiple sub-classes and based on input, we need to return one of the sub-class. The main method doesnt know the details of instantiating a object its deferred to the factory subclass. Factory calls the new operator.

```java
public class Main {

  public static void main(String[] args) {
    Animal animal = Factory.getAnimal(AnimalType.CAT);
    System.out.println(animal.sound());
  }
}

interface Animal {
  public String sound();
}

enum AnimalType{
  DOG,DUCK,CAT;
}

class Duck implements Animal {

  @Override
  public String sound() {
    return "Quak!";
  }
}

class Dog implements Animal {

  @Override
  public String sound() {
    return "Bark!";
  }
}

class Cat implements Animal {

  @Override
  public String sound() {
    return "Meow!";
  }
}

class Factory {
  public static Animal getAnimal(AnimalType type) {
    switch (type) {
    case DOG:
      return new Dog();
    case CAT:
      return new Cat();
    case DUCK:
      return new Duck();
    default:
      return null;
    }
  }
}
```

### 3. Abstract Factory Pattern

Abstract factory pattern is similar to Factory pattern and it’s factory of factories. In factory pattern we used switch statement to decide which object to return in abstract factory we remove the if-else/switch block and have a factory class for each sub-class.

```java

public class Main {

  public static void main(String[] args) {
    Animal animal = AbstractFactory.getAnimal(new DogFactory());
    System.out.println(animal.sound());
  }
}

interface Animal {
  public String sound();
}

class Duck implements Animal {
  @Override
  public String sound() {
    return "Quak!";
  }
}

class Dog implements Animal {
  @Override
  public String sound() {
    return "Bark!";
  }
}

class Cat implements Animal {
  @Override
  public String sound() {
    return "Meow!";
  }
}

interface BaseFactory {
  public Animal createAnimal();
}

class AbstractFactory {
  public static Animal getAnimal(BaseFactory bf) {
    return bf.createAnimal();
  }
}

class DuckFactory implements BaseFactory {
  @Override
  public Animal createAnimal() {
    return new Duck();
  }
}

class DogFactory implements BaseFactory {
  @Override
  public Animal createAnimal() {
    return new Dog();
  }
}

class CatFactory implements BaseFactory {
  @Override
  public Animal createAnimal() {
    return new Cat();
  }
}
```

### 4. Builder Pattern

Builder pattern is used to build a complex object with lot of attributes. It becomes difficult to pass the correct type in correct order to a constructor when there are many attributes. If some of the attributes are optional then there is overhead of having to pass null each time to the constructor or having to write multiple constructors(telescoping). Notice that in the example below builder pattern returns <b>immutable object</b> hence no setter methods exist. Notice the <b>static inner class</b> you can write an external class as well if you choose not to modify an existing class. Notice the private constructor of the Dog class as the only way to create an instance is via Builder. The name of dog and breed are the only mandatory fields this defines a contract that a dog object atleast needs these 2 attributes.

```java
import lombok.Getter;
import lombok.ToString;

public class Main {

  public static void main(String[] args) {
    Dog dog1 = new Dog.DogBuilder("rocky", "German Sheperd").setColor("Grey").setAge(6).setWeight(40.5).build();
    System.out.println(dog1);
    Dog dog2 = new Dog.DogBuilder("rocky", "German Sheperd").build();
    System.out.println(dog2);
  }

}

@Getter
@ToString
class Dog {

  String name;
  String breed;
  String color;
  int age;
  double weight;

  private Dog(DogBuilder builder) {
    this.name = builder.name;
    this.breed = builder.breed;
    this.color = builder.color;
    this.age = builder.age;
    this.weight = builder.weight;
  }

  @Getter
  public static class DogBuilder {

    String name;
    String breed;
    String color;
    int age;
    double weight;

    public DogBuilder(String name, String breed) {
      this.name = name;
      this.breed = breed;
    }

    public Dog build() {
      return new Dog(this);
    }

    public DogBuilder setColor(String color) {
      this.color = color;
      return this;
    }

    public DogBuilder setAge(int age) {
      this.age = age;
      return this;
    }

    public DogBuilder setWeight(double weight) {
      this.weight = weight;
      return this;
    }
  }
}
```

Output:

```bash
Dog(name=rocky, breed=German Sheperd, color=Grey, age=6, weight=40.5)
Dog(name=rocky, breed=German Sheperd, color=null, age=0, weight=0.0)
```

Using lombok @Builder annotation you can reduce the code further

```java
import lombok.Builder;
import lombok.Getter;
import lombok.ToString;

public class Main {
  public static void main(String[] args) {
    Dog dog1 = Dog.builder().name("Rocky").breed("German Sheperd").build();
    System.out.println(dog1);
  }
}

@Builder
@Getter
@ToString
class Dog {

  String name;
  String breed;
  String color;
  int age;
  @Builder.Default
  double weight = 30.0;
}
```

Output:

```bash
Dog(name=Rocky, breed=German Sheperd, color=null, age=0, weight=30.0)
```

An example in the java SDK is the StringBuilder class.

### 5. Prototype Pattern

Prototype pattern is used when the object creation is expensive. Instead of creating a new object you can copy the original object using clone and then modify it according to your needs. Prototype design pattern mandates that the object which you are copying should provide the copying feature, it should not be done by any other class. Decision to use shallow or deep copy of the object attributes is a design decision a shallow copy just copies immediate property and deep copy copies all object references as well. Notice we dont use new to create prototype objects after the first instance is created. Prototype avoid subclassing.

```java
import java.util.ArrayList;
import java.util.List;
import lombok.AllArgsConstructor;
import lombok.Data;

public class Main {

  public static void main(String[] args) throws CloneNotSupportedException {
    Employees emps = new Employees(new ArrayList<>());
    emps.seedData();
    Employees dataSet1 = (Employees) emps.clone();
    Employees dataSet2 = (Employees) emps.clone();

    System.out.println("dataSet1 size: " + dataSet1.getEmpList().size());
    System.out.println("dataSet2 size: " + dataSet2.getEmpList().size());

    dataSet2.getEmpList().add("jhon");
    System.out.println("dataSet1 size: " + dataSet1.getEmpList().size());
    System.out.println("dataSet2 size: " + dataSet2.getEmpList().size());
  }

}

@AllArgsConstructor
@Data
class Employees implements Cloneable {

  private List<String> empList;

  public void seedData() {
    for (int i = 0; i < 100; i++) {
      empList.add("employee_" + i);
    }
  }

  @Override
  public Object clone() throws CloneNotSupportedException {
    List<String> temp = new ArrayList<>();
    for (String s : this.empList) {
      temp.add(s);
    }
    return new Employees(temp);
  }
}
```

Output:

```bash
dataSet1 size: 100
dataSet2 size: 100
dataSet1 size: 100
dataSet2 size: 101
```

You can also create a registry to stored newly created objects when there are different types of objects and lookup against the registry when you want to clone objects.

## Structural Design Patterns

Deal with class and object composition. Provide different ways to create a class structure, using inheritance and composition to create a large object from small objects

### 1. Adapter Pattern

Adapter pattern is used when two unrelated interfaces need to work together. There is a AlienCraft which has different type of fire & scan api that takes additional parameter compared to the human readable ship interface. However by writing the adapter we map the appropriate functions for fire and scan. 

{% asset_img adapter-pattern-visual.PNG %}

```java
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {
    SpaceShipAdapter shipAdapter = new SpaceShipAdapter(new AlienCraft());
    shipAdapter.scan();
    shipAdapter.fire();
  }
}

class AlienCraft {
  public void dracarys(String sector) {
    System.out.println("Firing weapon at sector " + sector);
  }

  public void zorg(String sector) {
    System.out.println("Scanning enemy in sector " + sector);
  }
}

interface Ship {
  public void scan();

  public void fire();
}

class EnterpriseShip implements Ship {

  @Override
  public void scan() {
    System.out.println("Scanning enemy in front of ship!");
  }

  @Override
  public void fire() {
    System.out.println("Firing weapon!");
  }

}

@AllArgsConstructor
class SpaceShipAdapter implements Ship {
  AlienCraft ship;

  @Override
  public void scan() {
    ship.zorg("NORTH");
  }

  @Override
  public void fire() {
    ship.dracarys("NORTH");
  }

}
```

Output:

```bash
Scanning enemy in sector NORTH
Firing weapon at sector NORTH
```

UML Diagram Adapter design pattern.

{% asset_img adapter.PNG %}

### 2. Composite Pattern

Composite pattern is used when we have to represent a part-whole hierarchy.A group of objects should behave in a similar way,tree like structure. Here we have a playlist which can contain songs or other playlist and those playlist can have songs of their own.

{% asset_img composite-pattern-visual.PNG %}

```java
import java.util.ArrayList;
import java.util.List;
import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;

public class Main {

    public static void main(String[] args) {
      SongComponent playlist1  = new PlayList("playlist_1");
      SongComponent playlist2  = new PlayList("playlist_2");
      SongComponent playlist3  = new PlayList("playlist_3");
      SongComponent myplaylist  = new PlayList("myplaylist");
      myplaylist.add(playlist1);
      myplaylist.add(playlist2);
      playlist1.add(new Song("Song1"));
      playlist1.add(new Song("Song2"));
      playlist1.add(playlist3);
      playlist3.add(new Song("Song3"));
      
      myplaylist.displaySongInfo();
    }
}
abstract class SongComponent{
  
  public void add(SongComponent c) {
    throw new UnsupportedOperationException();
  }
  
  public String getSong() {
    throw new UnsupportedOperationException();
  }
  
  public void displaySongInfo() {
    throw new UnsupportedOperationException();
  }
}

@RequiredArgsConstructor
class PlayList extends SongComponent{
  
  List<SongComponent> componentLst = new ArrayList<>();
  final String playListName;

  @Override
  public void add(SongComponent c) {
    componentLst.add(c);
  }

  @Override
  public void displaySongInfo() {
    System.out.println("Playlist Name: " + playListName);
    for(SongComponent s: componentLst) {
      s.displaySongInfo();
    }
  }
}

@AllArgsConstructor
class Song extends SongComponent{
  String songName;
  
  @Override
  public String getSong() {
    return songName;
  }
  
  @Override
  public void displaySongInfo() {
    System.out.println("Song: "+ songName);
  }
}
```

Output:

```bash
Playlist Name: myplaylist
Playlist Name: playlist_1
Song: Song1
Song: Song2
Playlist Name: playlist_3
Song: Song3
Playlist Name: playlist_2
```

### 3. Proxy Pattern

Proxy pattern is used when we want to provide controlled access of a functionality. A real world example would be when a lawyer restricts the questions police would ask a mob boss. You can add only one proxy per class.

{% asset_img proxy-pattern-visual.PNG %}

```java
package com.tutor.designpattern.proxy;

public class Main {

    public static void main(String[] args) {
        Proxy proxy = new Proxy();
        proxy.runCommand("rm");
        proxy.runCommand("dir");
    }
}
interface Command {
  public void runCommand(String cmd);
}

class CommandImpl implements Command {

  @Override
  public void runCommand(String cmd) {
      System.out.println("Running : " + cmd);
  }
}

class Proxy implements Command {

  Command cmdObj;

  @Override
  public void runCommand(String cmd) {
      if (cmd.contains("rm")) {
          System.out.println("Cant run rm");
      } else {
          cmdObj.runCommand(cmd);
      }
  }

  public Proxy() {
      this.cmdObj = new CommandImpl();
  }

}
```

Output:

```bash
Cant run rm
Running : dir
```

A much more generic way to doing this using default java class InvocationHandler is shown below.

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Main {

  public static void main(String[] args) {
    Command cmd = (Command) CommandProxy.newInstance(new CommandImpl());
    cmd.runCommand("ls");
    cmd.runCommand("rm");
  }

}

interface Command {
  public void runCommand(String cmd);
}

class CommandImpl implements Command {

  @Override
  public void runCommand(String cmd) {
    System.out.println("Running : " + cmd);
  }
}

class CommandProxy implements InvocationHandler {
  private Object obj;

  private CommandProxy(Object obj) {
    this.obj = obj;
  }

  public static Object newInstance(Object obj) {
    return java.lang.reflect.Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(),
        new CommandProxy(obj));
  }

  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    Object result;
    try {
      if (args[0].equals("rm")) {
        throw new IllegalAccessException("rm command not allowed");
      } else {
        result = method.invoke(obj, args);
      }
      return result;
    } catch (InvocationTargetException ex) {
      throw ex.getTargetException();
    } catch (Exception ex) {
      throw new RuntimeException("invocation exception " + ex.getMessage());
    }
  }

}
```

### 4. Flyweight Pattern

Flyweight pattern is used when we need to create a lot of Objects of a class eg 100,000 objects. Reduce cost of storage for large objects by sharing. When we share objects we need to determine what is intrinsic and extrinsic attributes. Here beeType is an intrinsic state and will be shared by all bees. The (x,y) coordinates are the extrinsic properties which will vary for each object. Notice that a factory pattern is also seen in the flyweight example below.

{% asset_img flyweight-pattern-visual.PNG %}

```java
import java.util.HashMap;
import java.util.Random;
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {
    for (int i = 0; i < 100000; i++) {
      Bee b = FlyweightBeeFactory.getBeeType(BeeType.getRandom(new Random().nextInt(4)));
      b.carryOutMission(new Random().nextInt(100), new Random().nextInt(100));
    }

    System.out.println("Total Bee objects created:" + FlyweightBeeFactory.bees.size());
  }
}

interface Bee {
  public void carryOutMission(int x, int y);
}

@AllArgsConstructor
class WorkerBee implements Bee {

  BeeType beeType;

  @Override
  public void carryOutMission(int x, int y) {
    System.out.println(beeType + ", Depositing honey at (" + x + "," + y + ") quadrant!");
  }

}

@AllArgsConstructor
class AttackBee implements Bee {

  BeeType beeType;

  @Override
  public void carryOutMission(int x, int y) {
    System.out.println(beeType + ", Defending (" + x + "," + y + ") quadrant!");
  }

}

class FlyweightBeeFactory {

  public static final HashMap<BeeType, Bee> bees = new HashMap<>();

  public static Bee getBeeType(BeeType beeType) {
    Bee bee = bees.get(beeType);
    if (bee == null) {
      if (beeType.equals(BeeType.WORKER)) {
        bee = new WorkerBee(BeeType.WORKER);
      } else if (beeType.equals(BeeType.WORKER_LEADER)) {
        bee = new WorkerBee(BeeType.WORKER_LEADER);
      } else if (beeType.equals(BeeType.ATTACKER_LEADER)) {
        bee = new WorkerBee(BeeType.ATTACKER_LEADER);
      } else {
        bee = new WorkerBee(BeeType.ATTACKER);
      }
      bees.put(beeType, bee);
    }
    return bee;
  }

}

enum BeeType {
  WORKER, WORKER_LEADER, ATTACKER, ATTACKER_LEADER;

  public static BeeType getRandom(int val) {
    return BeeType.values()[val];
  }
}
```

Output:

```bash
...
...
WORKER_LEADER, Depositing honey at (9,50) quadrant!
ATTACKER, Depositing honey at (75,68) quadrant!
WORKER_LEADER, Depositing honey at (25,78) quadrant!
Total Bee objects created:4
```

Now lets look at how the bad design would have looked, Here we end up creating large number of objects there by wasting memory. In the solution above we have moved out the extrinsic properties from the Bee class so that we can share the objects.


<span style="color:red;font-size: large;font-weight: bold;">Bad Design Alert!</span>

```java
package com.tutor.designpattern.flyweight.bad;

import java.util.Random;
import lombok.AllArgsConstructor;

public class Main {

  public static void main(String[] args) {
    int i = 0;
    for (; i < 100000; i++) {
      BeeType beeType = BeeType.getRandom(new Random().nextInt(4));
      if (beeType.equals(BeeType.WORKER)) {
        new WorkerBee(BeeType.WORKER, new Random().nextInt(100), new Random().nextInt(100)).carryOutMission();
      } else if (beeType.equals(BeeType.WORKER_LEADER)) {
        new WorkerBee(BeeType.WORKER_LEADER, new Random().nextInt(100), new Random().nextInt(100)).carryOutMission();
      } else if (beeType.equals(BeeType.ATTACKER_LEADER)) {
        new WorkerBee(BeeType.ATTACKER_LEADER, new Random().nextInt(100), new Random().nextInt(100)).carryOutMission();
      } else {
        new WorkerBee(BeeType.ATTACKER, new Random().nextInt(100), new Random().nextInt(100)).carryOutMission();
      }
    }
    System.out.println("Total Bee objects created:" + i);
  }
}

interface Bee {
  public void carryOutMission();
}

@AllArgsConstructor
class WorkerBee implements Bee {

  BeeType beeType;
  int x;
  int y;

  @Override
  public void carryOutMission() {
    System.out.println(beeType + ", Depositing honey at (" + x + "," + y + ") quadrant!");
  }

}

@AllArgsConstructor
class AttackBee implements Bee {

  BeeType beeType;
  int x;
  int y;

  @Override
  public void carryOutMission() {
    System.out.println(beeType + ", Defending (" + x + "," + y + ") quadrant!");
  }

}

enum BeeType {
  WORKER, WORKER_LEADER, ATTACKER, ATTACKER_LEADER;

  public static BeeType getRandom(int val) {
    return BeeType.values()[val];
  }
}
```

Output:

```bash
...
...
WORKER_LEADER, Depositing honey at (77,41) quadrant!
ATTACKER_LEADER, Depositing honey at (54,35) quadrant!
WORKER, Depositing honey at (7,17) quadrant!
Total Bee objects created:100000
```

### 5. Facade Pattern

Facade pattern is used to give unified interface to a set of interfaces in a subsystem.

```java
package com.tutor.designpattern.facade;

public class Main {
  public static void main(String[] args) {
    HelperFacade.generateReport(DbType.ORACLE);
    HelperFacade.generateReport(DbType.MYSQL);
  }
}

enum DbType {
  ORACLE, MYSQL;
}

class MysqlHelper {

  public void mysqlReport() {
    System.out.println("Generating report in mysql");
  }
}

class OracleHelper {

  public void oracleReport() {
    System.out.println("Generating report in oracle");
  }

}

class HelperFacade {

  public static void generateReport(DbType db) {

    switch (db) {
    case ORACLE:
      OracleHelper ohelper = new OracleHelper();
      ohelper.oracleReport();
      break;
    case MYSQL:
      MysqlHelper mhelper = new MysqlHelper();
      mhelper.mysqlReport();
      break;
    }

  }
}
```

Output:

```bash
Generating report in oracle
Generating report in mysql

```

### 6. Bridge Pattern

Bridge Pattern is used to decouple the interfaces from implementation. Prefer Composition over inheritance. There are interface hierarchies in both interfaces as well a implementations.

{% asset_img bridge-pattern-visual.PNG %}

By decoupling the switch & electric device from each other each can vary independently. You can add new switches, you can add new electric devices independently without increasing complexity.

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

UML of Bridge Pattern. There is a bridge between Switch class and ElectricDevice class.

{% asset_img bridge.PNG %}

<span style="color:red;font-size: large;font-weight: bold;">Bad Design Alert!</span>

Lets look at how a problematic code looks like and its eligibility for bridge pattern. In the below code trying to add a new Electric Device + Switch combination is a pain which is solved by the bridge pattern mentioned above.

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

### 7. Decorator Pattern

Decorator design pattern is used to add the functionality by wrapping another class around the core class without modifying the core class. Disadvantage of decorator pattern is that it uses a lot of similar kind of objects.

{% asset_img decorator-pattern-visual.PNG %}

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
    this.car.assemble();
    System.out.println("Default features added as no specific feature requested!");
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

## Behavioral Design Patterns

Behavioral patterns help design classes with better interaction between objects and provide lose coupling.

### 1. Template Pattern

Template Pattern used to create a method stub and deferring some of the steps of implementation to the subclasses. Template method defines the steps to execute an algorithm and it can provide default implementation that might be common for all or some of the subclasses.

{% asset_img template-pattern-visual.PNG %}

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

Chain of responsibility pattern is used when a request from client is passed to a chain of objects to process them.

{% asset_img chainofresponsibility-pattern-visual.PNG %}

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

{% asset_img observer-pattern-visual.PNG %}

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

{% asset_img strategy-pattern-visual.PNG %}

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
Bridge pattern is built upfront you break things at design time to make changes so that functionality can be added without tight coupling, adapter pattern works after code is already designed like legacy code.

#### 2. Difference between mediator pattern and observer pattern
In observer, many objects are interested in the state change of one object. They are not interested in each other. So the relation is one to many. In mediator, many objects are interested to communicate many other objects. Here the relation is many to many.


#### 3. Difference between chain of responsibility and command pattern
In chain of responsibility pattern, the request is passed to potential receivers, whereas the command pattern uses a command object that encapsulates a request.

#### 4. Difference between adapter pattern and decorator pattern
Adapter pattern only adapts functionality, decorator adds more functionality.

#### 4. Difference between adapter pattern and facade pattern
Adapter pattern just links two incompatible interfaces. A facade is used when one wants an easier or simpler interface to work with.