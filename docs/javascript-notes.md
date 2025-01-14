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
### Notes on Advanced JavaScript

---

#### **1. Event Delegation**

**Event Delegation**: A technique where you delegate event handling to a parent element instead of attaching event listeners to individual child elements. This leverages the event bubbling mechanism.

**Benefits**:
- Reduces the number of event listeners, improving performance.
- Works dynamically for elements added after the initial page load.

**Example**:
```javascript
// Without Event Delegation
document.querySelectorAll('.item').forEach(item => {
    item.addEventListener('click', () => {
        console.log('Item clicked');
    });
});

// With Event Delegation
document.querySelector('.container').addEventListener('click', (event) => {
    if (event.target.classList.contains('item')) {
        console.log('Item clicked');
    }
});
```

---

#### **2. Debouncing and Throttling**

**Debouncing**: Delays the execution of a function until a certain amount of time has passed since the last time it was called. Useful for search inputs or resizing events.

**Throttling**: Limits the execution of a function to once every specified time interval. Useful for scroll or mouse move events.

**Example**:
```javascript
// Debouncing
function debounce(func, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

window.addEventListener('resize', debounce(() => {
    console.log('Window resized');
}, 300));

// Throttling
function throttle(func, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            func.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}

window.addEventListener('scroll', throttle(() => {
    console.log('Window scrolled');
}, 300));
```

---

#### **3. Functional Programming**

**Functional Programming (FP)**: A programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing state or mutable data.

**Key Concepts**:
- **Immutability**: Data cannot be changed after creation.
- **Pure Functions**: Functions that always return the same output for the same input and have no side effects.
- **Higher-Order Functions**: Functions that take other functions as arguments or return functions.

**Example**:
```javascript
// Immutability
const arr = [1, 2, 3];
const newArr = [...arr, 4]; // Creates a new array instead of modifying the original

// Pure Function
function add(a, b) {
    return a + b;
}

// Higher-Order Function
function multiplyBy(factor) {
    return function(number) {
        return number * factor;
    };
}
const double = multiplyBy(2);
console.log(double(5)); // Output: 10
```

---

#### **4. Memory Management and Garbage Collection**

**Memory Management**: JavaScript automatically allocates and frees memory, but developers must avoid memory leaks.

**Garbage Collection**: JavaScript engines use garbage collection to reclaim memory occupied by unused objects.

**Common Memory Leaks**:
- Unintended global variables.
- Forgotten timers or callbacks.
- Detached DOM elements.

**Example**:
```javascript
// Memory Leak Example
function createLeak() {
    leakedVar = 'I am a global variable'; // Unintended global variable
}

// Fixing Memory Leaks
function noLeak() {
    let safeVar = 'I am a local variable'; // Local variable
}

// Detached DOM Elements
let button = document.createElement('button');
document.body.appendChild(button);
button = null; // Detached DOM element (memory leak if not handled)
```

---

#### **5. Error Handling**

**Error Handling**: Techniques to catch and handle runtime and asynchronous errors gracefully.

**Strategies**:
- **`try...catch`**: For synchronous code.
- **`.catch()`**: For promises.
- **`async/await` with `try...catch`**: For asynchronous code.

**Example**:
```javascript
// Synchronous Error Handling
try {
    throw new Error('Something went wrong!');
} catch (error) {
    console.error(error.message); // Output: "Something went wrong!"
}

// Asynchronous Error Handling with Promises
fetch('https://api.example.com/data')
    .then(response => response.json())
    .catch(error => console.error('Fetch error:', error));

// Asynchronous Error Handling with async/await
async function fetchData() {
    try {
        const response = await fetch('https://api.example.com/data');
        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error('Fetch error:', error);
    }
}
fetchData();
```

---

### Notes on JavaScript in the Browser

---

#### **1. DOM Manipulation**

**DOM (Document Object Model)**: A tree-like representation of the HTML document. JavaScript can interact with the DOM to dynamically change the content, structure, and style of a webpage.

**Key Operations**:
- **Selecting Elements**: Use methods like `querySelector`, `querySelectorAll`, `getElementById`, etc.
- **Traversing the DOM**: Navigate between parent, child, and sibling nodes.
- **Modifying Elements**: Change attributes, styles, and content.

**Example**:
```javascript
// Selecting Elements
const header = document.querySelector('h1');
const allParagraphs = document.querySelectorAll('p');

// Traversing the DOM
const parent = header.parentElement;
const firstChild = parent.firstElementChild;

// Modifying Elements
header.textContent = 'New Header';
header.style.color = 'blue';

// Creating and Appending Elements
const newParagraph = document.createElement('p');
newParagraph.textContent = 'This is a new paragraph.';
document.body.appendChild(newParagraph);
```

---

#### **2. Browser APIs**

**Browser APIs**: Built-in functionalities provided by the browser to interact with the browser and the user's environment.

**Common APIs**:
- **Fetch API**: For making network requests.
- **Web Storage (`localStorage` and `sessionStorage`)**: For storing data on the client side.
- **Geolocation API**: For accessing the user's location.
- **Canvas API**: For drawing graphics and animations.

**Example**:
```javascript
// Fetch API
fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Error:', error));

// Web Storage
localStorage.setItem('name', 'Alice');
console.log(localStorage.getItem('name')); // Output: "Alice"

// Geolocation API
navigator.geolocation.getCurrentPosition(position => {
    console.log(`Latitude: ${position.coords.latitude}, Longitude: ${position.coords.longitude}`);
});

// Canvas API
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');
ctx.fillStyle = 'green';
ctx.fillRect(10, 10, 100, 100);
```

---

#### **3. Event Listeners**

**Event Listeners**: Functions that are called in response to specific events (e.g., clicks, keypresses).

**Event Phases**:
- **Capturing Phase**: Event travels from the root to the target.
- **Bubbling Phase**: Event travels from the target back to the root.

**Example**:
```javascript
// Adding Event Listeners
const button = document.querySelector('button');
button.addEventListener('click', () => {
    console.log('Button clicked!');
});

// Event Bubbling and Capturing
document.querySelector('.outer').addEventListener('click', () => {
    console.log('Outer div clicked');
}, true); // Capturing phase

document.querySelector('.inner').addEventListener('click', () => {
    console.log('Inner div clicked');
}); // Bubbling phase

// Event Delegation
document.querySelector('ul').addEventListener('click', (event) => {
    if (event.target.tagName === 'LI') {
        console.log('List item clicked:', event.target.textContent);
    }
});
```

---

#### **4. Performance Optimization**

**Performance Optimization**: Techniques to improve the speed and efficiency of your web application.

**Key Techniques**:
- **Lazy Loading**: Load resources (e.g., images, scripts) only when needed.
- **Code Splitting**: Split your JavaScript code into smaller chunks to load only what's necessary.
- **Minimizing Reflows/Repaints**: Reduce the number of times the browser recalculates layout and repaints the screen.

**Example**:
```javascript
// Lazy Loading Images
<img data-src="image.jpg" alt="Lazy-loaded image" class="lazy">
<script>
    document.addEventListener('DOMContentLoaded', () => {
        const lazyImages = document.querySelectorAll('.lazy');
        lazyImages.forEach(img => {
            img.src = img.dataset.src;
        });
    });
</script>

// Code Splitting with Dynamic Imports
button.addEventListener('click', async () => {
    const module = await import('./module.js');
    module.someFunction();
});

// Minimizing Reflows/Repaints
const element = document.getElementById('myElement');
element.style.display = 'none'; // Hide element to avoid reflows
// Perform multiple DOM changes
element.style.width = '100px';
element.style.height = '100px';
element.style.display = 'block'; // Show element after changes
```

---
### Notes on JavaScript on the Server (Node.js)

---

#### **1. Core Modules**

Node.js provides built-in **core modules** that enable server-side functionality. Some of the most commonly used modules include:

- **`fs` (File System)**: Interact with the file system (read/write files).
- **`path`**: Handle and transform file paths.
- **`http`**: Create HTTP servers and clients.
- **`stream`**: Handle streaming data (e.g., reading/writing large files).

**Example**:
```javascript
// fs Module
const fs = require('fs');

// Read a file
fs.readFile('example.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});

// Write to a file
fs.writeFile('example.txt', 'Hello, Node.js!', (err) => {
    if (err) throw err;
    console.log('File written successfully.');
});

// path Module
const path = require('path');
const filePath = path.join(__dirname, 'example.txt');
console.log(filePath); // Output: Absolute path to example.txt

// http Module
const http = require('http');
const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello, World!');
});
server.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});

// stream Module
const readStream = fs.createReadStream('example.txt');
const writeStream = fs.createWriteStream('output.txt');
readStream.pipe(writeStream); // Pipes data from readStream to writeStream
```

---

#### **2. Asynchronous Patterns**

Node.js is designed to be non-blocking and asynchronous. Key patterns for handling asynchronous operations include:

- **Callbacks**: Functions passed as arguments to other functions and executed after an operation completes.
- **Promises**: Objects representing the eventual completion (or failure) of an asynchronous operation.
- **`async/await`**: Syntactic sugar for working with promises, making asynchronous code look synchronous.

**Example**:
```javascript
// Callbacks
fs.readFile('example.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});

// Promises
const readFilePromise = (file) => {
    return new Promise((resolve, reject) => {
        fs.readFile(file, 'utf8', (err, data) => {
            if (err) reject(err);
            else resolve(data);
        });
    });
};
readFilePromise('example.txt')
    .then(data => console.log(data))
    .catch(err => console.error(err));

// async/await
async function readFileAsync() {
    try {
        const data = await readFilePromise('example.txt');
        console.log(data);
    } catch (err) {
        console.error(err);
    }
}
readFileAsync();
```

---

#### **3. Event Emitter**

**Event Emitter**: A core Node.js module (`events`) that allows you to create, fire, and listen for custom events. It’s the foundation of many Node.js features like streams and HTTP servers.

**Example**:
```javascript
const EventEmitter = require('events');

// Create a custom event emitter
class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

// Listen for an event
myEmitter.on('greet', (name) => {
    console.log(`Hello, ${name}!`);
});

// Emit the event
myEmitter.emit('greet', 'Alice'); // Output: "Hello, Alice!"
```

---

#### **4. RESTful APIs**

**RESTful APIs**: APIs that follow REST (Representational State Transfer) principles. They use HTTP methods (GET, POST, PUT, DELETE) to perform CRUD (Create, Read, Update, Delete) operations.

**Example**:
```javascript
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
    if (req.method === 'GET' && req.url === '/data') {
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify({ message: 'Hello, World!' }));
    } else if (req.method === 'POST' && req.url === '/data') {
        let body = '';
        req.on('data', chunk => body += chunk);
        req.on('end', () => {
            res.writeHead(201, { 'Content-Type': 'application/json' });
            res.end(JSON.stringify({ received: JSON.parse(body) }));
        });
    } else {
        res.writeHead(404, { 'Content-Type': 'text/plain' });
        res.end('Not Found');
    }
});

server.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

---

#### **5. Package Management (npm/yarn)**

**Package Management**: Tools like `npm` (Node Package Manager) and `yarn` help manage dependencies and scripts for Node.js projects.

**Key Commands**:
- **`npm init`**: Initialize a new Node.js project.
- **`npm install <package>`**: Install a package.
- **`npm run <script>`**: Run a script defined in `package.json`.

**Example**:
```bash
# Initialize a new project
npm init -y

# Install a package (e.g., Express)
npm install express

# Define a script in package.json
{
  "scripts": {
    "start": "node app.js"
  }
}

# Run the script
npm start
```

**Using Yarn**:
```bash
# Initialize a new project
yarn init -y

# Install a package
yarn add express

# Run a script
yarn start
```

---

### Notes on Testing and Debugging

---

#### **1. Unit Testing**

**Unit Testing**: Testing individual units or components of your code in isolation to ensure they work as expected. Popular frameworks include **Jest** and **Mocha**.

**Jest**: A powerful testing framework with built-in assertions, mocking, and code coverage.

**Mocha**: A flexible testing framework often paired with assertion libraries like **Chai**.

**Example with Jest**:
```javascript
// math.js
function add(a, b) {
    return a + b;
}

// math.test.js
const { add } = require('./math');

test('adds 1 + 2 to equal 3', () => {
    expect(add(1, 2)).toBe(3);
});

test('adds -1 + 1 to equal 0', () => {
    expect(add(-1, 1)).toBe(0);
});
```

**Example with Mocha + Chai**:
```javascript
// math.js
function add(a, b) {
    return a + b;
}

// math.test.js
const { add } = require('./math');
const chai = require('chai');
const expect = chai.expect;

describe('add function', () => {
    it('should return 3 for 1 + 2', () => {
        expect(add(1, 2)).to.equal(3);
    });

    it('should return 0 for -1 + 1', () => {
        expect(add(-1, 1)).to.equal(0);
    });
});
```

---

#### **2. Debugging Tools**

**Debugging Tools**: Tools to identify and fix issues in your code.

- **Browser DevTools**: Debug JavaScript running in the browser (e.g., Chrome DevTools).
- **Node.js Debugger**: Debug Node.js applications using the built-in debugger or tools like **VS Code**.

**Example with Chrome DevTools**:
1. Open Chrome DevTools (`F12` or `Ctrl+Shift+I`).
2. Go to the **Sources** tab.
3. Set breakpoints in your JavaScript code.
4. Reload the page and inspect variables, call stack, and step through code.

**Example with Node.js Debugger**:
```bash
# Start Node.js in debug mode
node inspect app.js
```

**Example with VS Code**:
1. Add a `launch.json` file in the `.vscode` folder:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Debug Node.js",
            "skipFiles": ["<node_internals>/**"],
            "program": "${workspaceFolder}/app.js"
        }
    ]
}
```
2. Set breakpoints in your code.
3. Press `F5` to start debugging.

---

#### **3. Code Coverage**

**Code Coverage**: Measures how much of your code is tested. Tools like **Istanbul** (used by Jest) help generate coverage reports.

**Example with Jest**:
```bash
# Run tests with coverage
npx jest --coverage
```

This generates a coverage report in the `coverage` folder, showing:
- **Statement Coverage**: Percentage of statements executed.
- **Branch Coverage**: Percentage of branches (e.g., `if` statements) executed.
- **Function Coverage**: Percentage of functions called.
- **Line Coverage**: Percentage of lines executed.

**Example Output**:
```
PASS  ./math.test.js
✓ adds 1 + 2 to equal 3 (2 ms)
✓ adds -1 + 1 to equal 0

----------------|---------|----------|---------|---------|-------------------
File            | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
----------------|---------|----------|---------|---------|-------------------
All files       |     100 |      100 |     100 |     100 |
 math.js        |     100 |      100 |     100 |     100 |
----------------|---------|----------|---------|---------|-------------------
```

---

#### **4. Mocking and Spying**

**Mocking**: Replacing real dependencies with fake implementations to isolate the unit under test.

**Spying**: Tracking function calls, arguments, and return values without modifying their behavior.

**Example with Jest**:
```javascript
// userService.js
class UserService {
    constructor(userRepository) {
        this.userRepository = userRepository;
    }

    getUser(id) {
        return this.userRepository.getUser(id);
    }
}

// userService.test.js
const UserService = require('./userService');
const UserRepository = require('./userRepository');

jest.mock('./userRepository'); // Mock the UserRepository

test('getUser should call repository', () => {
    const mockUserRepository = new UserRepository();
    const userService = new UserService(mockUserRepository);

    // Mock the getUser method
    mockUserRepository.getUser.mockReturnValue({ id: 1, name: 'Alice' });

    const user = userService.getUser(1);

    expect(mockUserRepository.getUser).toHaveBeenCalledWith(1);
    expect(user).toEqual({ id: 1, name: 'Alice' });
});
```

**Example with Sinon (for Mocha)**:
```javascript
// userService.js
class UserService {
    constructor(userRepository) {
        this.userRepository = userRepository;
    }

    getUser(id) {
        return this.userRepository.getUser(id);
    }
}

// userService.test.js
const sinon = require('sinon');
const UserService = require('./userService');
const UserRepository = require('./userRepository');

describe('UserService', () => {
    it('getUser should call repository', () => {
        const mockUserRepository = new UserRepository();
        const getUserStub = sinon.stub(mockUserRepository, 'getUser').returns({ id: 1, name: 'Alice' });
        const userService = new UserService(mockUserRepository);

        const user = userService.getUser(1);

        sinon.assert.calledWith(getUserStub, 1);
        expect(user).to.deep.equal({ id: 1, name: 'Alice' });
    });
});
```

---
### Notes on Performance and Optimization

---

#### **1. Code Splitting**

**Code Splitting**: A technique to split your code into smaller bundles that can be loaded on demand. This reduces the initial load time of your application.

**Tools**:
- **Webpack**: Built-in support for code splitting using `import()` or `require.ensure`.
- **Rollup**: Supports code splitting with plugins like `@rollup/plugin-commonjs`.

**Example with Webpack**:
```javascript
// webpack.config.js
module.exports = {
    entry: './src/index.js',
    output: {
        filename: '[name].bundle.js',
        path: __dirname + '/dist',
    },
    optimization: {
        splitChunks: {
            chunks: 'all',
        },
    },
};

// Lazy load a module
import(/* webpackChunkName: "lazyModule" */ './lazyModule').then(module => {
    module.default();
});
```

**Example with Rollup**:
```javascript
// rollup.config.js
import commonjs from '@rollup/plugin-commonjs';
import resolve from '@rollup/plugin-node-resolve';

export default {
    input: 'src/index.js',
    output: {
        dir: 'dist',
        format: 'esm',
        chunkFileNames: '[name].js',
    },
    plugins: [resolve(), commonjs()],
};
```

---

#### **2. Lazy Loading and Preloading**

**Lazy Loading**: Load resources (e.g., JavaScript, images) only when they are needed, reducing the initial load time.

**Preloading**: Load critical resources in advance to improve performance.

**Example with Lazy Loading**:
```javascript
// Lazy load an image
const img = document.createElement('img');
img.src = 'placeholder.jpg';
img.dataset.src = 'actual-image.jpg';
document.body.appendChild(img);

const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            const lazyImage = entry.target;
            lazyImage.src = lazyImage.dataset.src;
            observer.unobserve(lazyImage);
        }
    });
});

observer.observe(img);

// Lazy load a JavaScript module
const button = document.querySelector('button');
button.addEventListener('click', () => {
    import('./lazyModule').then(module => {
        module.default();
    });
});
```

**Example with Preloading**:
```html
<!-- Preload critical resources -->
<link rel="preload" href="critical.css" as="style">
<link rel="preload" href="critical.js" as="script">
```

---

#### **3. Minification**

**Minification**: The process of removing unnecessary characters (e.g., whitespace, comments) from code to reduce file size.

**Tools**:
- **Webpack**: Uses `TerserPlugin` for JavaScript minification.
- **CSS Minifiers**: Tools like `cssnano`.
- **HTML Minifiers**: Tools like `html-minifier`.

**Example with Webpack**:
```javascript
// webpack.config.js
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
    optimization: {
        minimize: true,
        minimizer: [new TerserPlugin()],
    },
};
```

**Example with CSS Minification**:
```bash
# Install cssnano
npm install cssnano --save-dev

# Use cssnano in a build process
const cssnano = require('cssnano');
const postcss = require('postcss');

postcss([cssnano])
    .process(cssContent)
    .then(result => {
        console.log(result.css);
    });
```

---

#### **4. Benchmarking**

**Benchmarking**: Measuring the performance of your code to identify bottlenecks.

**Tools**:
- **`console.time` and `console.timeEnd`**: Simple built-in timing functions.
- **Third-party Libraries**: Libraries like `Benchmark.js` for more advanced benchmarking.

**Example with `console.time`**:
```javascript
console.time('myFunction');
myFunction(); // Function to benchmark
console.timeEnd('myFunction'); // Output: "myFunction: 123.456ms"
```

**Example with `Benchmark.js`**:
```bash
# Install Benchmark.js
npm install benchmark --save-dev
```

```javascript
const Benchmark = require('benchmark');
const suite = new Benchmark.Suite;

// Add tests
suite
    .add('Function A', () => {
        functionA();
    })
    .add('Function B', () => {
        functionB();
    })
    .on('cycle', (event) => {
        console.log(String(event.target));
    })
    .on('complete', function () {
        console.log('Fastest is ' + this.filter('fastest').map('name'));
    })
    .run({ async: true });
```

---

### Notes on Security

---

#### **1. Cross-Site Scripting (XSS)**

**Cross-Site Scripting (XSS)**: A vulnerability where attackers inject malicious scripts into web pages viewed by other users.

**Prevention**:
- **Sanitize Input**: Always sanitize user input to remove or escape harmful content.
- **Use Secure Libraries**: Libraries like **DOMPurify** or **sanitize-html** can help sanitize HTML.
- **Content Security Policy (CSP)**: Use CSP headers to restrict the sources of executable scripts.

**Example**:
```javascript
// Sanitize user input using DOMPurify
const DOMPurify = require('dompurify');
const userInput = '<script>alert("XSS Attack!")</script>';
const sanitizedInput = DOMPurify.sanitize(userInput);
console.log(sanitizedInput); // Output: ""

// Set Content Security Policy (CSP) in HTTP headers
app.use((req, res, next) => {
    res.setHeader(
        'Content-Security-Policy',
        "default-src 'self'; script-src 'self' https://trusted.cdn.com;"
    );
    next();
});
```

---

#### **2. Cross-Origin Resource Sharing (CORS)**

**Cross-Origin Resource Sharing (CORS)**: A mechanism that allows restricted resources on a web page to be requested from another domain.

**Secure Configuration**:
- **Allow Specific Origins**: Only allow trusted domains to access your API.
- **Use Middleware**: Libraries like **cors** in Node.js simplify CORS configuration.

**Example**:
```javascript
const express = require('express');
const cors = require('cors');
const app = express();

// Allow only specific origins
const corsOptions = {
    origin: 'https://trusted-website.com',
    optionsSuccessStatus: 200,
};
app.use(cors(corsOptions));

app.get('/data', (req, res) => {
    res.json({ message: 'This is secure data!' });
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

---

#### **3. OAuth and JWT**

**OAuth**: An open standard for access delegation, commonly used for authentication and authorization.

**JWT (JSON Web Tokens)**: A compact, URL-safe means of representing claims to be transferred between two parties.

**Example with OAuth and JWT**:
```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const app = express();

// Middleware to verify JWT
function authenticateToken(req, res, next) {
    const authHeader = req.headers['authorization'];
    const token = authHeader && authHeader.split(' ')[1];
    if (!token) return res.sendStatus(401);

    jwt.verify(token, 'your-secret-key', (err, user) => {
        if (err) return res.sendStatus(403);
        req.user = user;
        next();
    });
}

// Login route to generate JWT
app.post('/login', (req, res) => {
    const user = { id: 1, username: 'alice' };
    const accessToken = jwt.sign(user, 'your-secret-key', { expiresIn: '1h' });
    res.json({ accessToken });
});

// Protected route
app.get('/protected', authenticateToken, (req, res) => {
    res.json({ message: 'This is a protected route', user: req.user });
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

---

#### **4. Secure Data Handling**

**Secure Data Handling**: Protect sensitive data by using HTTPS, environment variables, and encryption.

**Best Practices**:
- **Use HTTPS**: Encrypt data in transit using SSL/TLS.
- **Environment Variables**: Store sensitive data (e.g., API keys, secrets) in environment variables.
- **Encryption**: Encrypt sensitive data at rest using libraries like **crypto**.

**Example**:
```javascript
// Use HTTPS in Express
const https = require('https');
const fs = require('fs');
const express = require('express');
const app = express();

const options = {
    key: fs.readFileSync('server.key'),
    cert: fs.readFileSync('server.cert'),
};

https.createServer(options, app).listen(3000, () => {
    console.log('HTTPS server running on https://localhost:3000');
});

// Use environment variables
require('dotenv').config();
const apiKey = process.env.API_KEY;

// Encrypt sensitive data
const crypto = require('crypto');
const algorithm = 'aes-256-cbc';
const key = crypto.randomBytes(32);
const iv = crypto.randomBytes(16);

function encrypt(text) {
    let cipher = crypto.createCipheriv(algorithm, Buffer.from(key), iv);
    let encrypted = cipher.update(text);
    encrypted = Buffer.concat([encrypted, cipher.final()]);
    return { iv: iv.toString('hex'), encryptedData: encrypted.toString('hex') };
}

function decrypt(text) {
    let iv = Buffer.from(text.iv, 'hex');
    let encryptedText = Buffer.from(text.encryptedData, 'hex');
    let decipher = crypto.createDecipheriv(algorithm, Buffer.from(key), iv);
    let decrypted = decipher.update(encryptedText);
    decrypted = Buffer.concat([decrypted, decipher.final()]);
    return decrypted.toString();
}

const encrypted = encrypt('Sensitive Data');
console.log(encrypted);
const decrypted = decrypt(encrypted);
console.log(decrypted); // Output: "Sensitive Data"
```

---

### Notes on Soft Skills for a Senior Developer

---

#### **1. Code Reviews**

**Code Reviews**: A process where team members review each other's code to ensure quality, share knowledge, and maintain consistency.

**Best Practices**:
- **Be Constructive**: Focus on the code, not the person. Provide actionable feedback.
- **Be Specific**: Point out exact issues and suggest improvements.
- **Be Respectful**: Use a positive tone and acknowledge good practices.
- **Prioritize**: Focus on critical issues like bugs, security vulnerabilities, and performance bottlenecks.

**Example**:
```javascript
// Code Review Example
// Before
function add(a, b) {
    return a + b;
}

// After (with feedback)
/**
 * Adds two numbers.
 * @param {number} a - The first number.
 * @param {number} b - The second number.
 * @returns {number} The sum of a and b.
 */
function add(a, b) {
    if (typeof a !== 'number' || typeof b !== 'number') {
        throw new TypeError('Both arguments must be numbers');
    }
    return a + b;
}
```

**Feedback**:
- "Great job on the function! However, it would be beneficial to add input validation to ensure the function handles non-number inputs gracefully."
- "Consider adding JSDoc comments to improve code documentation."

---

#### **2. Documentation**

**Documentation**: Writing clear and comprehensive documentation to help others understand and use your code.

**Best Practices**:
- **Be Clear and Concise**: Use simple language and avoid jargon.
- **Include Examples**: Provide code examples to illustrate usage.
- **Keep It Updated**: Ensure documentation stays in sync with the code.
- **Use Tools**: Leverage tools like **JSDoc**, **Markdown**, or **Swagger** for API documentation.

**Example**:
```javascript
/**
 * Calculates the area of a rectangle.
 * @param {number} length - The length of the rectangle.
 * @param {number} width - The width of the rectangle.
 * @returns {number} The area of the rectangle.
 * @example
 * // Returns 20
 * calculateArea(4, 5);
 */
function calculateArea(length, width) {
    return length * width;
}
```

**Markdown Example**:
```markdown
# API Documentation

## `GET /users`

Returns a list of users.

### Example Request

```bash
curl -X GET https://api.example.com/users
```

### Example Response

```json
[
    { "id": 1, "name": "Alice" },
    { "id": 2, "name": "Bob" }
]
```
```

---

#### **3. Mentoring**

**Mentoring**: Helping junior developers grow by sharing knowledge, providing guidance, and fostering a supportive environment.

**Best Practices**:
- **Be Patient**: Understand that everyone learns at their own pace.
- **Encourage Questions**: Create a safe space for asking questions.
- **Provide Resources**: Share books, articles, and tutorials.
- **Lead by Example**: Demonstrate best practices in your own work.

**Example**:
- **Pair Programming**: Work together on a task to teach problem-solving and coding techniques.
- **Code Reviews**: Use code reviews as an opportunity to explain best practices and suggest improvements.
- **Regular Check-ins**: Schedule regular meetings to discuss progress, challenges, and goals.

---

#### **4. System Design**

**System Design**: Contributing to the design of scalable, maintainable, and efficient systems.

**Key Considerations**:
- **Scalability**: Ensure the system can handle growth in users, data, and traffic.
- **Maintainability**: Design for ease of maintenance and future updates.
- **Performance**: Optimize for speed and efficiency.
- **Reliability**: Ensure the system is robust and can handle failures gracefully.

**Example**:
```plaintext
### System Design for a URL Shortener

#### Requirements
- Shorten long URLs.
- Redirect users to the original URL when they visit the shortened URL.
- Handle high traffic and large volumes of data.

#### Components
1. **Web Server**: Handles incoming requests and redirects.
2. **Database**: Stores mappings between short and long URLs.
3. **Cache**: Improves performance by caching frequently accessed URLs.
4. **URL Generator**: Generates unique short URLs.

#### Scalability
- Use a distributed database to handle large volumes of data.
- Implement caching to reduce database load.
- Use load balancers to distribute traffic across multiple servers.

#### Example API Endpoints
- `POST /shorten`: Create a short URL.
- `GET /{shortUrl}`: Redirect to the original URL.
```

---