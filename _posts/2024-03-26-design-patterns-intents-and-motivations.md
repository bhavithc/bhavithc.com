---
layout: post
title: Design Patterns Intents and Motivations
date: '2024-03-26 20:56:58 +0530'
tags: [c++, cpp, design-pattern]
categories: [design-pattern]
---

In this blog we gonna see about some of the important aspects of the design patterns such as **Intent**, **Also known as**, **Motivation**, **Applicability**, **Structure**, **Participants** and **Related Patterns**

# Creational Patterns
- Creational design patterns abstract the instantiation process
- They help make a system independent of how its objects are created, composed, and represented

Creational patterns are of two kinds 
- Class creational patterns: Uses inheritance to vary the class that's instantiated
- Object creational patterns: Delegate instantiation to another object

There are two recurring themes in these patterns
1. They all encapsulate knowledge about which concrete classes the system uses
2. They hide how instances of these classes are created and put together

Creational patterns give you a lot of flexibility in
- what gets created
- who creates it
- how it gets created
- when it is created

They let you configure a system with "product" objects that vary widely in structure and functionality Configuration can be static (that is, specified at compile-time) or dynamic (atrun-time)

--- 

## Factory Methods

### Intent
Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

### Also Known As
Virtual Constructor

### Motivation
- Consider a framework for an application that can create mutliple documents to the user 
- The two key abstractions in this framework are **Application** class and **Document** class 
  - Example: To create a drawing application we need DrawingApplication and DrawingDocument
- The Application class is resposible for manging documents and will create them as on when required

![alt text](/assets/design-patterns/diagrams/factory_method.png)

- Application subclasses redefine an abstract `CreateDocument` operation on Application to return the appropriate Document subclass
- Once an Application subclass is instantiated, it can then instantiate application-specific Documents without knowing their class

In this case, We call CreateDocument a **factory method** because it's responsible for "manufacturing" an object

### Applicability
Use the Factory Method pattern when
- A class can't anticipate the class of objects it must create
- A class wants its subclasses to specify the objects it creates.
- classes delegate responsibility to one of several helper subclasses, and you want to localize the knowledge of which helper subclass is the delegate

### Structure
![alt text](/assets/design-patterns/diagrams/factory_structure.png)

### Participants 

- **Product** (Document)
  - defines the interface of objects the factory method creates.
- **ConcreteProduct** (MyDocument)
  - implements the Product interface.
- **Creator** (Application)
  - declares the factory method, which returns an object of type Product. Creator may also define a default implementation of the factory method that returns a default ConcreteProduct object.
  - may call the factory method to create a Product object.
- **ConcreteCreator** (MyApplication)
  - overrides the factory method to return an instance of a ConcreteProduct.

### Related Patterns
- **Abstract Factory** is often implemented with factory methods
  - The Motivation example in the Abstract Factory pattern illustrates Factory Method as well
- Factory methods are usually called within **Template Methods**

--- 

## Abstract Factory

### Intent
Provide an interface for creating families of related or dependent objects without specifying their concrete classes.

### Also Known As
Kit 

### Motivation
- Consider a user interface toolkit that supports multiple look-and-feel standards, such as Motif and Presentation Manager (PM)
- Different look-and-feels define different appearances and behaviors for user interface "widgets" like scroll bars, windows, and buttons
- To be portable across look-and-feel standards, an application should not hard-code its widgets for a particular look and feel
- Instantiating look-and-feel-specific classes of widgets throughout the application makes it hard to change the look and feel later
- We can solve this problem by defining an **abstract WidgetFactory** class that declares an interface for creating each basic kind of **widget**.
- Again here's also an abstract class for each kind of **widget**, and concrete subclasses implement widgets for specific look-and-feel standards

![alt text](/assets/design-patterns/diagrams/abstract_factory_motivation.png)

- WidgetFactory's interface has an operation that returns a new widget object for each abstract widget class
- Clients call these operations to obtain widget instances, but clients aren't aware of the concrete
classes they're using. Thus clients stay independent of the prevailing look and feel.

### Applicability
Use the Abstract Factory pattern when
- A system should be independent of how its products are created, composed, and represented.
- A system should be configured with one of multiple families of products.
- A family of related product objects is designed to be used together, and you need to enforce this constraint.
- you want to provide a class library of products, and you want to reveal just their interfaces, not their implementations.

### Structure
![alt text](/assets/design-patterns/diagrams/abstract_facgtory_struct.png)

### Participants
- **AbstractFactory** (WidgetFactory)
  - declares an interface for operations that create abstract product objects
- **ConcreteFactory** (MotifWidgetFactory, PMWidgetFactory)
  - implements the operations to create concrete product objects.
- **AbstractProduct** (Window, ScrollBar)
  - declares an interface for a type of product object.
- **ConcreteProduct** (MotifWindow, MotifScrollBar)
  - defines a product object to be created by the corresponding concrete factory.
  - implements the AbstractProduct interface.
- **Client**
  - uses only interfaces declared by AbstractFactory and AbstractProduct classes.

### Related Patterns

- **AbstractFactory** classes are often implemented with **factory methods**, but they can also be implemented using **Prototype** (lol)
- A concrete factory is often a **singleton**

---

## Singleton

### Intent 
Ensure a class only has one instance, and provide a global point of access to it

### Motivation
- It's important for some classes to have exactly one instance
Examples
  - Although there can be many printers in a system, there should be only one printer spooler
  - There should be only one file system and one window manager
  - A digital filter will have one A/D converter
  - An accounting system will be dedicated to serving one company

**How do we ensure that a class has only one instance and that the instance is easily accessible?**
    A global variable makes an object accessible, but it doesn't keep you from instantiating multiple objects.

- A better solution is to make the class itself responsible for keeping track of its sole instance
- The class can ensure that no other instance can be created (by intercepting requests to create new objects), and it can provide a way to access the instance. **This is the Singleton pattern**

### Applicability

Use the Singleton pattern when
- There must be exactly one instance of a class, and it must be accessible to clients from a well-known access point
- when the sole instance should be extensible by subclassing, and clients should be able to use an extended instance without modifying their code

![alt text](/assets/design-patterns/diagrams/singleton_movitation.png)

### Participants
- **Singleton**
  - defines an Instance operation that lets clients access its unique instance. Instance is a class operation (that is, a class method in Smalltalk and a static member function in C++).
  - may be responsible for creating its own unique instance.

### Related Patterns
- Many patterns can be implemented using the Singleton pattern, See **Abstract Factory**, **Builder**, and **Prototype**

--- 

## Prototype

### Intent
Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype.

### Motivation
