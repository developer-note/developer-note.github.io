---
layout: default
title: Design Patterns
parent: Best Practices
grand_parent: Java
nav_order: 20
has_children: false
---

{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Design Patterns

- sets of standardized practices used in the software development industry
- various Design patterns are available
- major Categories
    - Creational (focus on Object instantiation)
    - Structual (Focus on Class compositions and object structure)
    - Behavioural (Focus on Object interation)
    - J2EE patterns (Java EE applications)

# Creational Patterns

- some encapsulate the creation logic away from users and handles creation
    - `Factory`
    - `Abstract Factory`
- focus on the process of building the objects themselves
    - `Builder`
- some minimize the cost of creation 
    - `Prototype`
- some control the number of instances on the whole JVM 
    - `Singleton`

> `Simple Factory` (not a design pattern) also belong to Factory Pattern family

## Factory method

- defines a method, which should be used for creating objects instead of direct constructor call
- other names - Virtual Constructor or Factory Template Pattern

> Design principle: **Encapsulate what varies**

### JDK Examples

- `getInstance()` method of `java.util.Calendar`, `NumberFormat`, and `ResourceBundle` uses factory method design pattern. 
- `java.lang.Class.newInstance()`
- `valueOf()` method of all wrapper classes
- `java.nio.charset.Charset.forName()`, `java.lang.Class.forName()`
- `java.sql.DriverManager#getConnection()`, `java.net.URL.openConnection()`

### Diagram

![Factory Method](/img/java/FactoryMethod.png)

### Example

```java
public class FactoryMethodApp {

	public static void main(String[] args) {
		LogFactory logFactory = new LogFactoryImpl();
		logFactory.getLogger("FILE").log();
		logFactory.getLogger("DB").log();
	}

}

//Base Factory
interface LogFactory{
	Logger getLogger(String name);
}

// Concrete Factory
class LogFactoryImpl implements LogFactory{
	@Override
	public Logger getLogger(String name) {
		switch (name) {
		case "FILE":
			return new FileLogger();			
		case "DB":
			return new DatabaseLogger();
		default:
			break;
		}
		
		return null;
	}
}

// ProductBase
interface Logger{
	void log();
}

// Concrete Product 1
class FileLogger implements Logger{
	@Override
	public void log() {
		// File Logging
	}
}

//Concrete Product 2
class DatabaseLogger implements Logger {
	@Override
	public void log() {
		// DB logging
	}
}
```

## Abstract Factory

- builds upon the Factory Pattern and acts as the highest factory in the hierarchy
-  creating a factory of factories

### JDK Examples

- `javax.xml.parsers.DocumentBuilderFactory#newInstance()`
- `javax.xml.transform.TransformerFactory#newInstance()`
- `javax.xml.xpath.XPathFactory#newInstance()`

### Diagram

![Abstract Factory Method](/img/java/AbstractFactory.png)

### Example

```java

public class AbstractFactoryPattern {
	
	// Provider
	static FurnitureFactory getFactory(String type) {
		switch (type) {
		case "wood":
			return new WoodenFurnitureFactory();
		case "plastic":
			return new PlasticFurnitureFactory();
		default:
			return null;
		}
	}
	
	public static void main(String[] args) {
		FurnitureFactory furnitureFactory = getFactory("wood");
		FurnitureClient client = new FurnitureClient(furnitureFactory);
		client.make();
	}
}

// Abstract Product A 
interface Table{
	void make();
}

//Abstract Product B 
interface Chair{
	void make();
}

//Concrete Product A1
class WoodenTable implements Table{
	@Override
	public void make() {
		// make		
	}
}

//Concrete Product A2
class PlasticTable implements Table{
	@Override
	public void make() {
		// make		
	}
}

//Concrete Product B1
class WoodenChair implements Chair{
	@Override
	public void make() {
		// make		
	}
}

//Concrete Product B2
class PlasticChair implements Chair{
	@Override
	public void make() {
		// make		
	}
}

// Abstract Factory
interface FurnitureFactory{
	Table createTable();
	Chair createChair();
}

//Abstract Factory 1
class WoodenFurnitureFactory implements FurnitureFactory{

	@Override
	public Table createTable() {
		return new WoodenTable();
	}

	@Override
	public Chair createChair() {
		return new WoodenChair();
	}
}

//Abstract Factory 2
class PlasticFurnitureFactory implements FurnitureFactory{

	@Override
	public Table createTable() {
		return new PlasticTable();
	}

	@Override
	public Chair createChair() {
		return new PlasticChair();
	}
}

// client
class FurnitureClient{
	Table table;
	Chair chair;
	public FurnitureClient(FurnitureFactory furnitureFactory) {
		this.table = furnitureFactory.createTable();
		this.chair = furnitureFactory.createChair();
	}
	
	public void make() {
		table.make();
		chair.make();
	}
}
```

# Simple Factory Vs Factory Method vs Abstract Factory

## Factory 

- it is fixed no new subclassing

```java
class FruitFactory {

  public Apple makeApple() {
    // Code for creating an Apple here.
  }

  public Orange makeOrange() {
    // Code for creating an orange here.
  }
}
```

## Factory Method

- generally used when you have some generic processing in a class, but want to vary which kind of fruit you actually use.

> an interface for creating an object, but let subclasses decide which class to instantiate

```java
abstract class FruitPicker {

  protected abstract Fruit makeFruit();

  // this method is what make the difference
  public void pickFruit() {
    private final Fruit f = makeFruit(); // The fruit we will work on..

  }
}

class OrangePicker extends FruitPicker {

  @Override
  protected Fruit makeFruit() {
    return new Orange();
  }
}
```

## Abstract Factory

- normally used for things like dependency injection/strategy
- when you want to be able to create a whole family of objects that need to be of "the same kind", and have some common base classes
-  we want to make sure that we don't accidentally use an OrangePicker on an Apple 

> Provide an interface for creating families of related or dependent objects without specifying their concrete classes.

```java
interface PlantFactory {
  
  Plant makePlant();

  Picker makePicker(); 

}

public class AppleFactory implements PlantFactory {
  Plant makePlant() {
    return new Apple();
  }

  Picker makePicker() {
    return new ApplePicker();
  }
}

public class OrangeFactory implements PlantFactory {
  Plant makePlant() {
    return new Orange();
  }

  Picker makePicker() {
    return new OrangePicker();
  }
}
```
