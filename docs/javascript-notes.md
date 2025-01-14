---
title: Javascript topics
---

### Notes on Core JavaScript Topics

---

#### **1. Scope and Closures**

**Scope** defines the accessibility of variables, functions, and objects in your code during runtime. JavaScript has three types of scope:
- **Global Scope**: Variables declared outside any function or block.
- **Function Scope**: Variables declared inside a function.
- **Block Scope**: Variables declared inside a block (`{}`) using `let` or `const`.

**Lexical Scoping**: JavaScript uses lexical scoping, meaning the scope of a variable is determined by its position in the source code.

**`var`, `let`, and `const`**:
- `var`: Function-scoped, hoisted, and can be redeclared.
- `let`: Block-scoped, not hoisted, and cannot be redeclared.
- `const`: Block-scoped, not hoisted, cannot be redeclared, and cannot be reassigned.

**Closures**: A closure is a function that retains access to its lexical scope even when the function is executed outside that scope. Closures are useful for encapsulation and handling asynchronous code.

**Example**:
```javascript
// Lexical Scoping
function outer() {
    let outerVar = 'I am outside!';

    function inner() {
        console.log(outerVar); // Access outerVar due to lexical scoping
    }
    inner();
}
outer(); // Output: "I am outside!"

// Closure
function createCounter() {
    let count = 0;
    return function() {
        count++;
        console.log(count);
    };
}
const counter = createCounter();
counter(); // Output: 1
counter(); // Output: 2
```

---

#### **2. Execution Context and Hoisting**

**Execution Context**: JavaScript code runs inside an execution context, which has two phases:
- **Creation Phase**: Memory is allocated for variables and functions (hoisting).
- **Execution Phase**: Code is executed line by line.

**Hoisting**: JavaScript moves variable and function declarations to the top of their scope during the creation phase.
- **Function Declarations**: Fully hoisted (can be called before declaration).
- **Variable Declarations**: Partially hoisted (`var` is hoisted but initialized as `undefined`, `let` and `const` are hoisted but not initialized).

**Example**:
```javascript
console.log(x); // Output: undefined (var is hoisted)
var x = 10;

console.log(y); // ReferenceError: Cannot access 'y' before initialization (let/const are hoisted but not initialized)
let y = 20;

foo(); // Output: "Hello" (function declaration is fully hoisted)
function foo() {
    console.log("Hello");
}
```

---

#### **3. Event Loop and Asynchronous Programming**

**Event Loop**: JavaScript is single-threaded, but it uses the event loop to handle asynchronous operations. The event loop continuously checks the call stack and the task queue (macrotasks and microtasks).

**Macrotasks vs. Microtasks**:
- **Macrotasks**: `setTimeout`, `setInterval`, I/O operations.
- **Microtasks**: Promises, `queueMicrotask`, `process.nextTick` (Node.js).

**`async/await`**: Syntactic sugar for working with promises, making asynchronous code look synchronous.

**Example**:
```javascript
console.log("Start");

setTimeout(() => console.log("Timeout"), 0); // Macrotask

Promise.resolve().then(() => console.log("Promise")); // Microtask

console.log("End");

// Output:
// Start
// End
// Promise
// Timeout

// async/await Example
async function fetchData() {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    console.log(data);
}
fetchData();
```

---

#### **4. Prototype and Prototypal Inheritance**

**Prototype**: Every JavaScript object has a prototype, which is another object that it inherits properties and methods from.

**Prototypal Inheritance**: Objects can inherit properties and methods from other objects via the prototype chain.

**`Object.create()`**: Creates a new object with the specified prototype.

**Example**:
```javascript
const animal = {
    speak() {
        console.log(`${this.name} makes a noise.`);
    }
};

const dog = Object.create(animal);
dog.name = "Rex";
dog.speak(); // Output: "Rex makes a noise."

// Constructor Function and Prototype
function Person(name) {
    this.name = name;
}
Person.prototype.greet = function() {
    console.log(`Hello, my name is ${this.name}`);
};

const john = new Person("John");
john.greet(); // Output: "Hello, my name is John"
```

---

#### **5. Strict Mode**

**Strict Mode**: Enforces stricter parsing and error handling in your JavaScript code. It prevents common mistakes and "unsafe" actions.

**Benefits**:
- Throws errors for common coding mistakes.
- Prevents the use of undeclared variables.
- Disallows duplicate parameter names.
- Makes `this` undefined in global functions.

**Example**:
```javascript
"use strict";

x = 10; // ReferenceError: x is not defined

function duplicateParam(a, a) { // SyntaxError: Duplicate parameter name not allowed
    console.log(a);
}

function globalThis() {
    console.log(this); // Output: undefined (in strict mode)
}
globalThis();
```

---

### Notes on Modern JavaScript (ES6+)

---

#### **1. Arrow Functions and Lexical `this`**

**Arrow Functions**: A concise syntax for writing functions. They are anonymous and do not have their own `this`, `arguments`, `super`, or `new.target`.

**Lexical `this`**: Arrow functions inherit `this` from the parent scope, making them ideal for callbacks and methods where you want to preserve the context.

**Example**:
```javascript
// Traditional Function
function add(a, b) {
    return a + b;
}

// Arrow Function
const add = (a, b) => a + b;

// Lexical `this`
const obj = {
    name: "Alice",
    greet: function() {
        setTimeout(() => {
            console.log(`Hello, ${this.name}`); // `this` refers to `obj`
        }, 1000);
    }
};
obj.greet(); // Output: "Hello, Alice"
```

---

#### **2. Destructuring and Spread/Rest Operators**

**Destructuring**: Extract values from arrays or objects into distinct variables.

**Spread Operator (`...`)**: Expands an iterable (array, object, string) into individual elements.

**Rest Operator (`...`)**: Collects multiple elements into an array or object.

**Example**:
```javascript
// Array Destructuring
const [a, b, ...rest] = [1, 2, 3, 4];
console.log(a); // Output: 1
console.log(rest); // Output: [3, 4]

// Object Destructuring
const person = { name: "John", age: 30 };
const { name, age } = person;
console.log(name); // Output: "John"

// Spread Operator
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];
console.log(arr2); // Output: [1, 2, 3, 4, 5]

// Rest Operator
function sum(...numbers) {
    return numbers.reduce((acc, num) => acc + num, 0);
}
console.log(sum(1, 2, 3)); // Output: 6
```

---

#### **3. Modules (import/export)**

**Modules**: ES6 introduced a native module system to organize code into reusable and maintainable pieces.

- **`export`**: Exports variables, functions, or classes from a module.
- **`import`**: Imports exported members from another module.

**Example**:
```javascript
// math.js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// app.js
import { add, subtract } from './math.js';
console.log(add(2, 3)); // Output: 5
console.log(subtract(5, 2)); // Output: 3

// Default Export
// math.js
export default function multiply(a, b) {
    return a * b;
}

// app.js
import multiply from './math.js';
console.log(multiply(2, 3)); // Output: 6
```

---

#### **4. Template Literals**

**Template Literals**: Use backticks (`` ` ``) to create strings that can span multiple lines and embed expressions using `${}`.

**Example**:
```javascript
const name = "Alice";
const age = 25;

// Multi-line String
const message = `
  Hello, my name is ${name}.
  I am ${age} years old.
`;
console.log(message);

// Expression Embedding
const sum = (a, b) => a + b;
console.log(`The sum of 2 and 3 is ${sum(2, 3)}.`); // Output: "The sum of 2 and 3 is 5."
```

---

#### **5. Promises and Async/Await**

**Promises**: Represent the eventual completion (or failure) of an asynchronous operation and its resulting value.

**`async/await`**: Syntactic sugar for working with promises, making asynchronous code look synchronous.

**Example**:
```javascript
// Promises
const fetchData = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("Data fetched!");
        }, 2000);
    });
};

fetchData()
    .then(data => console.log(data)) // Output: "Data fetched!"
    .catch(error => console.error(error));

// Async/Await
async function fetchDataAsync() {
    try {
        const data = await fetchData();
        console.log(data); // Output: "Data fetched!"
    } catch (error) {
        console.error(error);
    }
}
fetchDataAsync();
```

---

#### **6. Iterators and Generators**

**Iterators**: Objects that implement the `next()` method, returning an object with `value` and `done` properties.

**Generators**: Functions that can be paused and resumed, using `function*` and `yield`.

**Example**:
```javascript
// Iterator
const arrayIterator = (arr) => {
    let index = 0;
    return {
        next: () => {
            return index < arr.length ?
                { value: arr[index++], done: false } :
                { done: true };
        }
    };
};

const iterator = arrayIterator([1, 2, 3]);
console.log(iterator.next()); // Output: { value: 1, done: false }
console.log(iterator.next()); // Output: { value: 2, done: false }
console.log(iterator.next()); // Output: { value: 3, done: false }
console.log(iterator.next()); // Output: { done: true }

// Generator
function* numberGenerator() {
    yield 1;
    yield 2;
    yield 3;
}

const gen = numberGenerator();
console.log(gen.next().value); // Output: 1
console.log(gen.next().value); // Output: 2
console.log(gen.next().value); // Output: 3
```

---
