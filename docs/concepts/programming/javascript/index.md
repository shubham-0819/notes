# JavaScript Core Concepts

JavaScript concepts and features.

## Execution Context & Scope

- **Lexical Environment** - How variables and functions are scoped based on their physical location in code
- **Closure** - Functions retaining access to their outer scope variables
- **Hoisting** - Variable and function declarations being moved to the top of their scope
- **Temporal Dead Zone (TDZ)** - Period between entering scope and variable declaration where let/const cannot be accessed

## The JavaScript Engine

- **Event Loop** - How JS handles asynchronous operations
- **Call Stack** - LIFO structure tracking execution context
- **Memory Heap** - Where objects and function closures are stored
- **Microtask & Macrotask Queues** - How promises and timers are handled

## Object-Oriented JavaScript 

- **Prototypal Inheritance** - Objects inheriting directly from other objects
- **Constructor Functions** - Traditional way of creating "classes"
- **ES6 Classes** - Syntactic sugar over prototypal inheritance
- **`this` Binding** - How context is determined and methods like `bind`, `call`, `apply`

## Functional Programming Concepts

- **First-class Functions** - Functions as values
- **Higher-order Functions** - Functions that operate on other functions
- **Pure Functions** - Functions without side effects
- **Immutability** - Not modifying data directly
- **Function Composition** - Building complex functions from simple ones

## Modern JavaScript Features

### ES6+ Features
- Destructuring assignment
- Spread/Rest operators
- Arrow functions
- Template literals
- Modules (import/export)
- Iterators and generators
- Map/Set data structures

### Asynchronous Patterns
- **Promises** - Handling async operations
- **Async/Await** - Syntactic sugar over promises
- **Observable Pattern** - Handling streams of data

## Memory Management

- **Garbage Collection** - How JS manages memory
- **Memory Leaks** - Common causes and prevention
- **WeakMap/WeakSet** - Special collections for garbage collection

## Performance Optimization

- **Memoization** - Caching function results
- **Debouncing/Throttling** - Controlling function execution rate
- **Virtual DOM** - Efficient DOM updates (used in frameworks)
- **Web Workers** - Parallel processing capabilities

## Security Considerations

- **Same-Origin Policy** - Browser security mechanism
- **Cross-Site Scripting (XSS)** - Prevention techniques
- **Content Security Policy** - Restricting resource origins
- **Secure coding practices** - Input validation, sanitization

## Design Patterns

- **Module Pattern** - Encapsulation and organization
- **Observer Pattern** - Event handling and reactivity
- **Factory Pattern** - Object creation
- **Singleton Pattern** - Single instance objects

## Testing and Debugging

- **Unit Testing** - Testing individual components
- **Integration Testing** - Testing component interactions
- **Debugging Tools** - Chrome DevTools, Source Maps
- **Error Handling** - Try/Catch, Custom Errors

## Browser APIs and DOM

- **DOM Manipulation** - Efficient updates and event handling
- **Web Storage** - LocalStorage/SessionStorage
- **Service Workers** - Offline capabilities, PWAs
- **Web APIs** - Fetch, WebSocket, WebRTC

## Best Practices

- **Code Organization** - Modular architecture
- **Error Handling** - Proper error management
- **Performance Optimization** - Loading and execution
- **Security Considerations** - Data protection
- **Accessibility** - Making applications accessible

## Modern Development Tools

- **Build Tools** - Webpack, Rollup, Vite
- **Package Managers** - npm, yarn, pnpm
- **Transpilers** - Babel, TypeScript
- **Linters/Formatters** - ESLint, Prettier
