---
layout: default
title: Design Patterns
parent: Java
nav_order: 1
has_children: false
---

# Design Patterns

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

## Factory method

- defines a method, which should be used for creating objects instead of direct constructor call
- other names - Virtual Constructor or Factory Template Pattern

### JDK examples

- `getInstance()` method of `java.util.Calendar`, `NumberFormat`, and `ResourceBundle` uses factory method design pattern. 
- `java.lang.Class.newInstance()`
- `valueOf()` method of all wrapper classes
- `java.nio.charset.Charset.forName()`, `java.lang.Class.forName()`
- `java.sql.DriverManager#getConnection()`, `java.net.URL.openConnection()`

## Diagram

![Factory Method](/img/java/FactoryMethod.png)

## Example

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