# Design Patterns

Design patterns are reusable solutions to common problems in software design. Based on the book "Design Patterns: Elements of Reusable Object-Oriented Software", these patterns are categorized into three main types:

## 1. Creational Patterns

Patterns that deal with object creation mechanisms.

### Singleton
- **Purpose**: Ensures a class has only one instance
- **Example**: Database connection pool, Logger
- **Code Example**: Configuration manager that maintains application settings

### Factory Method
- **Purpose**: Defines interface for creating objects, lets subclasses decide which class to instantiate
- **Example**: Document creator that handles different file formats
- **Code Example**: Payment processor factory creating different payment methods (Credit Card, PayPal)

### Abstract Factory
- **Purpose**: Creates families of related objects
- **Example**: UI toolkit that creates consistent GUI elements
- **Code Example**: Cross-platform UI components factory

### Builder
- **Purpose**: Separates complex object construction from its representation
- **Example**: Custom computer configuration system
- **Code Example**: SQL query builder

### Prototype
- **Purpose**: Creates new objects by cloning an existing object
- **Example**: Creating game objects from templates
- **Code Example**: Document template system

## 2. Structural Patterns

Patterns that deal with object composition and relationships.

### Adapter
- **Purpose**: Converts interface of a class into another interface clients expect
- **Example**: Power outlet adapters for different countries
- **Code Example**: Legacy system integration adapter

### Bridge
- **Purpose**: Separates abstraction from implementation
- **Example**: Device-remote control relationship
- **Code Example**: Cross-platform drawing API

### Composite
- **Purpose**: Treats individual objects and compositions uniformly
- **Example**: File system structure (files and directories)
- **Code Example**: GUI component hierarchy

### Decorator
- **Purpose**: Adds responsibilities to objects dynamically
- **Example**: Coffee ordering system with add-ons
- **Code Example**: Input/Output stream decorators

### Facade
- **Purpose**: Provides unified interface to a set of interfaces
- **Example**: Home theater system control
- **Code Example**: Library API simplification

### Flyweight
- **Purpose**: Shares common parts of state between multiple objects
- **Example**: Character formatting in text editor
- **Code Example**: Game object pool

### Proxy
- **Purpose**: Provides surrogate for another object to control access
- **Example**: Credit card as a proxy for bank account
- **Code Example**: Lazy loading of heavy resources

## 3. Behavioral Patterns

Patterns that deal with communication between objects.

### Chain of Responsibility
- **Purpose**: Passes request along chain of handlers
- **Example**: Support ticket escalation system
- **Code Example**: Logging framework with different levels

### Command
- **Purpose**: Encapsulates request as an object
- **Example**: Remote control with programmable buttons
- **Code Example**: Undo/Redo functionality

### Interpreter
- **Purpose**: Defines grammar for simple language
- **Example**: Mathematical expression parser
- **Code Example**: SQL query interpreter

### Iterator
- **Purpose**: Accesses elements sequentially without exposing structure
- **Example**: Browsing through playlist
- **Code Example**: Custom collection traversal

### Mediator
- **Purpose**: Defines how objects interact
- **Example**: Air traffic control system
- **Code Example**: Chat room server

### Memento
- **Purpose**: Captures and restores object's internal state
- **Example**: Game save system
- **Code Example**: Text editor's undo mechanism

### Observer
- **Purpose**: Notifies dependents when object changes
- **Example**: Newsletter subscription system
- **Code Example**: Event handling in UI

### State
- **Purpose**: Alters object's behavior when state changes
- **Example**: Vending machine states
- **Code Example**: Document editing modes

### Strategy
- **Purpose**: Defines family of algorithms, makes them interchangeable
- **Example**: Different payment methods
- **Code Example**: Sorting algorithms selection

### Template Method
- **Purpose**: Defines skeleton of algorithm, lets subclasses override steps
- **Example**: Document generation process
- **Code Example**: Data mining operations

### Visitor
- **Purpose**: Adds operations to class without changing it
- **Example**: Document export in different formats
- **Code Example**: AST operations in compiler

## Best Practices

1. **Choose Patterns Wisely**: Don't force patterns where they're not needed
2. **Understand the Context**: Consider your specific use case and requirements
3. **Keep It Simple**: Start with simpler solutions before applying complex patterns
4. **Document Usage**: Clearly document where and why patterns are used
5. **Consider Maintenance**: Think about long-term maintenance implications

## Anti-Patterns to Avoid

1. **Pattern Overuse**: Using patterns where simple code would suffice
2. **Golden Hammer**: Trying to solve every problem with favorite patterns
3. **Abstract Everything**: Creating unnecessary abstractions
4. **Premature Optimization**: Implementing patterns before they're needed

