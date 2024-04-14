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
Today’s programming is all about costs. Saving is a big issue when it comes to using computer resources, so programmers are doing their best to find ways of improving the performance. When we talk about object creation we can find a better way to have new objects: cloning. To this idea one particular design pattern is related: rather than creation it uses cloning. If the cost of creating a new object is large and creation is resource intensive, we clone the object.

### Applicability
Use the Prototype pattern when a system should be independent of how its products are created, composed, and represented; and

- when the classes to instantiate are specified at run-time, for example, by dynamic loading; or
- to avoid building a class hierarchy of factories that parallels the class hierarchy of products; or
- when instances of a class can have one of only a few different combinations of state. It may be more convenient to install a corresponding number of prototypes and clone them rather than instantiating the class manually, each time with the appropriate state.

### Structure 
![alt text](/assets/design-patterns/diagrams/prototype_structure.png)


### Participants
- Prototype
  - declares an interface for cloning itself.
- ConcretePrototype
  - implements an operation for cloning itself.
- Client
  - creates a new object by asking a prototype to clone itself.

### Collaborations
- A client asks a prototype to clone itself.

### Related Patterns
- Prototype and Abstract Factory are competing patterns in some ways. They can also be used together, however. An Abstract Factory might store a set of prototypes from which to clone and return product objects
- Designs that make heavy use of the Composite and Decorator patterns often can benefit from Prototype as well

---
## Builder 

## Intent
Separate the construction of a complex object from its representation so that the same construction process can create different representations.

## Motivation
A RTF Reader should able to convert to many text formats. The reader might convert RTF documents into plain ASCII text or into a text widget that can be edited interactively. The problem, however, is that the number of possible conversions is open-ended. So it should be easy to add a new conversion without modifying the reader

A solution is to configure the RTFReader class with a TextConverter object that converts RTF to another textual representation. As the RTFReader parses the RTF document, it uses the TextConverter to perform the conversion.Whenever the RTFReader recognizes an RTF token, it issues a request to the TextConverter to convert the token.TextConverter objects are responsible both for performing the data conversion and for representing the token in a particular format.

Subclasses of TextConverter specialize in different conversions and formats. For example,
- ASCIIConverter ignores requests to convert anything except plain text.
- TeXConverter, on the other hand, will implement operations for all requests in order to produce a TeX representation that captures all the stylistic information in the text
- TextWidgetConverter will produce a complex user interface object that lets the user see and edit the text.

![alt text](/assets/design-patterns/diagrams/builder_motivation.png)

The Builder pattern captures all these relationships. 
- Each converter class is called a **builder** in the pattern
- Reader is called the **director**.

Builder pattern separates the algorithm for interpreting a textual format (that is, the parser for RTF documents) from how a converted format gets created and represented. This lets us reuse the RTFReader's parsing algorithm to create different text representations from RTF documents—just configure the RTFReader with different subclasses of TextConverter.

## Applicability

Use the Builder pattern when
- Algorithm for creating a complex object should be independent of the parts that make up the object and how they're assembled.
- Construction process must allow different representations for the object that's constructed

## Structure

![alt text](/assets/design-patterns/diagrams/builder_structure.png)

## Participants

- **Builder** (TextConverter)
  - specifies an abstract interface for creating parts of a Product object.
- **ConcreteBuilder** (ASCIIConverter, TeXConverter, TextWidgetConverter)
  - constructs and assembles parts of the product by implementing the Builder interface.
  - defines and keeps track of the representation it creates.
  - provides an interface for retrieving the product (e.g., GetASCIIText, GetTextWidget).
- **Director** (RTFReader)
  - constructs an object using the Builder interface.
- **Product** (ASCIIText, TeXText, TextWidget)
  - represents the complex object under construction. ConcreteBuilder builds the product's internal representation and defines the process by which it's assembled.
  - includes classes that define the constituent parts, including interfaces for assembling the parts into the final result.

![alt text](/assets/design-patterns/diagrams/builder_sequence.png)

## Related Patterns

- **Abstract Factory** is similar to Builder in that it too may construct complex objects. 
Difference between builder and abstract factory

| Builder | Abstract factory |
|-|-|
|Builder pattern focuses on constructing <br/> a complex object step by step | Abstract Factory's emphasis is on families <br/>of product objects (either simple or complex) |
|Builder returns the product as a final step |Abstract Factory pattern is concerned, <br/> the product gets returned immediately |

- A **Composite** is what the builder often builds.

# Structral Patterns
- Structural patterns are concerned with how classes and objects are composed to form larger structures.
- Structural class patterns use inheritance to compose interfaces or implementations
- Structural object patterns describe ways to compose objects to realize new functionality
- The added flexibility of object composition comes from the ability to change the composition at run-time, which is impossible with static class composition.

## Adapter

## Intent
Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces

## Also Known As
Wrapper 

## Motivation
Sometimes a toolkit class that's designed for reuse isn't reusable only because its interface doesn't match the domain-specific interface an application requires.

The adapter pattern is adapting between classes and objects. Like any adapter in the real world it is used to be an interface, a bridge between two objects. 

## Applicability
Use the Adapter pattern when
- you want to use an existing class, and its interface does not match the one you need.
- you want to create a reusable class that cooperates with unrelated or unforeseen classes, that is, classes that don't necessarily have compatible interfaces.
- (object adapter only) you need to use several existing subclasses, but it's impractical to adapt their interface by subclassing every one. An object adapter can adapt the interface of its parent class.

## Structure

**Type1:** A class adapter uses multiple inheritance to adapt one interface to another:
![alt text](/assets/design-patterns/diagrams/adapter_structure_type1.png)

**Type2:** An object adapter relies on object composition:
![alt text](/assets/design-patterns/diagrams/adapter_structure_type2.png)

## Participants
- **Target**
  - defines the domain-specific interface that Client uses.
- **Client**
  - collaborates with objects conforming to the Target interface.
- **Adaptee**
  - defines an existing interface that needs adapting.
- **Adapter**
  - adapts the interface of Adaptee to the Target interface.

> Note: Sometimes Adpater and Adaptee name makes lot of confusion, so better avoid it and use which better suits for the situation 

## Collaborations
- Clients call operations on an Adapter instance. In turn, the adapter calls Adaptee operations that carry out the request.

## Known Uses
- Avoids tight coupling with third-party code
- Often used as a protection layer between application and third-party components

## Related Patterns
- Bridge has a structure similar to an object adapter, but Bridge has a different intent: It is meant to separate an interface from its implementation so that they can be varied easily and independently. An adapter is meant to change the interface of an existing object.
- Decorator enhances another object without changing its interface. A decorator is thus more transparent to the application than an adapter is. As a consequence, Decorator supports recursive composition, which isn't possible with pure adapters.
- Proxy defines a representative or surrogate for another object and does not change its interface.

## Bridge

## Intent 
Decouple an abstraction from its implementation so that the two can vary independently.

## Also Known As
Handle/Body

## Motivation
- When an abstraction can have one of several possible implementations, the usual way to accommodate them is to use inheritance.
- An abstract class defines the interface to the abstraction, and concrete subclasses implement it in different ways. But this approach isn't always flexible enough. Inheritance binds an implementation to the abstraction permanently, which makes it difficult to modify, extend, and reuse abstractions and implementations independently.

Example: 
Consider the implementation of a portable Window abstraction in a user interface toolkit. This abstraction should enable us to write applications that work on both the X Window System and IBM's Presentation Manager (PM), for example. Using inheritance, we could define an abstract class Window and subclasses XWindow and
PMWindow that implement the Window interface for the different platforms. But this approach has two drawbacks:

1. It's inconvenient to extend the Window abstraction to cover different kinds of windows or new platforms. Imagine an IconWindow subclass of Window that specializes the Window abstraction for icons. To support IconWindows for both platforms, we have to implement two new classes, XIconWindow and
PMIconWindow. Worse, we'll have to define two classes for every kind of window. Supporting a third platform requires yet another new Window subclass for every kind of window.
![alt text](/assets/design-patterns/diagrams/bridge_bad_way.png)

2. It makes **client code platform-dependent**. Whenever a client creates a window, it instantiates a concrete class that has a specific implementation. For example, creating an XWindow object binds the Window abstraction to the X Window implementation, which makes the client code dependent on the X Window implementation. This, in turn, makes it harder to port the client code to other platforms.

**Expectation:** Clients should be able to create a window without committing to a concrete implementation. Only the window implementation should depend on the platform on which the application runs. Therefore client code should instantiate windows without mentioning specific platforms.

**Solution**
- The Bridge pattern addresses these problems by putting the Window abstraction and its implementation in separate class hierarchies. 
- There is one class hierarchy for window interfaces (Window, IconWindow, TransientWindow) and a separate
hierarchy for platform-specific window implementations, with WindowImp as its root. The XWindowImp subclass, for example, provides an implementation based on the X Window System.

![alt text](/assets/design-patterns/diagrams/bridge_structure.png)

- All operations on Window subclasses are implemented in terms of abstract operations from the WindowImp interface. This decouples the window abstractions from the various platform-specific implementations
- We refer to the relationship between Window and WindowImp as a **bridge**, because it bridges the abstraction and its implementation, letting them vary independently.

In simple instead of creating an interfaces and do inheritance, make it as seperate classes for interfaces and implementations 

## Applicability
Use the Bridge pattern when
- you want to avoid a permanent binding between an abstraction and its implementation. This might be the case, for example, when the implementation must be **selected or switched at run-time**
- both the abstractions and their implementations should be extensible by subclassing. In this case, the Bridge pattern lets you combine the different abstractions and implementations and extend them independently.
- changes in the implementation of an abstraction should have no impact on clients; that is, **their code should not have to be recompiled.** - Client should not be recompiled
- (C++) you want to hide the implementation of an abstraction completely from clients. ***In C++ the representation of a class is visible in the class interface.*** -> here we can use pimpl idiom to avoid this as well 

## Structure
![alt text](/assets/design-patterns/diagrams/bridge_structure_2.png)

## Participants
- Abstraction (Window)
  - defines the abstraction's interface.
  - maintains a reference to an object of type Implementor.
- RefinedAbstraction (IconWindow)
  - Extends the interface defined by Abstraction.
- Implementor (WindowImp)
  - defines the interface for implementation classes. This interface **doesn't** have to correspond **exactly** to Abstraction's interface; in fact the two interfaces **can be quite different**. Typically the Implementor interface provides only **primitive operations**, and Abstraction defines higher-level operations based on these primitives.
- ConcreteImplementor (XWindowImp, PMWindowImp)
  - implements the Implementor interface and defines its concrete implementation.

## Collaborations
- Abstraction forwards client requests to its Implementor object.

## Related Patterns
- An Abstract Factory can create and configure a particular Bridge.
- The Adapter pattern is geared toward making unrelated classes work together. It is usually applied to systems after they're designed.Bridge, on the other hand, is used up-front in a design to let abstractions and implementations vary independently.


## Composite

## Intent

Compose objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.

## Motivation
Graphics applications like drawing editors and CAD systems let users build complex diagrams out of simple components. The user can group components to form larger components, which in turn can be grouped to form still larger components.
Example: Group in PPT where user can group many small items and make a new component

A simple implementation could define classes for graphical primitives such as Text and Lines plus other classes that act as containers for these primitives. That is we can have a class which can hold Text, Lines and other grpups and acts as a containers. But there's a problem with this approach: **Code that uses these classes must treat primitive and container objects differently**, even if most of the time the user treats them identically. Having to distinguish these objects makes the application more complex.

The Composite pattern describes how to use recursive composition so that clients don't have to make this distinction.

![alt text](/assets/design-patterns/diagrams/composite_struct.png)

- The key to the Composite pattern is an abstract class that represents both primitives and their containers. For the graphics system, this class is Graphic. Graphic declares operations like Draw that are specific to graphical objects. It also declares operations that all composite objects share, such as operations for accessing and managing its children.

The subclasses Line, Rectangle, and Text (see preceding class diagram) define primitive graphical objects. These classes implement Draw to draw lines, rectangles, and text, respectively. Since primitive graphics have no child graphics, none of these subclasses implements child-related operations. 

The Picture class defines an aggregate of Graphic objects. Picture implements Draw to call Draw on its children, and it implements child-related operations accordingly. Because the Picture interface conforms to the Graphic interface, Picture objects can compose other Pictures recursively.

The following diagram shows a typical composite object structure of recursively composed Graphic objects:

![alt text](/assets/design-patterns/diagrams/composite_herarchy.png)

## Applicability

Use the Composite pattern when

- you want to represent part-whole hierarchies of objects.
- you want clients to be able to ignore the difference between compositions of objects and individual objects. Clients will treat all objects in the composite structure uniformly.

## Structure

![alt text](/assets/design-patterns/diagrams/composite_strct.png)

## Participants
- Component (Graphic)
  - declares the interface for objects in the composition.
  - implements default behavior for the interface common to all classes, as appropriate.
  - declares an interface for accessing and managing its child components.
  - (optional) defines an interface for accessing a component's parent in the recursive structure, and implements it if that's appropriate.
- Leaf (Rectangle, Line, Text, etc.)
  - represents leaf objects in the composition. A leaf has no children.
  - defines behavior for primitive objects in the composition.
- Composite (Picture)
  - defines behavior for components having children.
  - stores child components.
  - implements child-related operations in the Component interface.
- Client
  - manipulates objects in the composition through the Component interface.

## Related Patterns
- Often the component-parent link is used for a Chain of Responsibility
- Decorator is often used with Composite. When decorators and composites are used together, they will usually have a common parent class. So decorators will have to support the Component interface with operations like Add, Remove, and GetChild.
- Flyweight lets you share components, but they can no longer refer to their parents.
- Iterator can be used to traverse composites.
- Visitor localizes operations and behavior that would otherwise be distributed across Composite and Leaf classes.

## Decorator

## Intent

Attach additional responsibilities to an object dynamically. Decorators provide a flexible **alternative** to subclassing for extending functionality.


