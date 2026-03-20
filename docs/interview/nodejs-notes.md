# Node.js Interview Notes

> **TODO:** This document is a stub. Expand with deeper coverage of streams, clustering, performance tuning, and common interview questions with answers.

## Core Concepts

### Event Loop

Node.js is single-threaded and non-blocking. The event loop processes tasks in phases:

1. **Timers** — `setTimeout`, `setInterval`
2. **Pending Callbacks** — I/O callbacks deferred from previous cycle
3. **Idle, Prepare** — Internal use
4. **Poll** — Retrieve new I/O events
5. **Check** — `setImmediate` callbacks
6. **Close Callbacks** — `socket.on('close', ...)`

Microtasks (`Promise.then`, `process.nextTick`) run between each phase.

### Streams

```js
const fs = require('fs');

// Readable stream
const readable = fs.createReadStream('input.txt');
const writable = fs.createWriteStream('output.txt');

// Pipe data from readable to writable
readable.pipe(writable);
```

**Types:** Readable, Writable, Duplex, Transform

### Modules (CommonJS vs ESM)

```js
// CommonJS
const express = require('express');
module.exports = { myFunc };

// ESM
import express from 'express';
export { myFunc };
```

### Error Handling

```js
// Async/await pattern
async function getData() {
  try {
    const data = await fetchSomething();
    return data;
  } catch (err) {
    console.error('Error:', err.message);
    throw err;
  }
}

// Unhandled rejections
process.on('unhandledRejection', (reason) => {
  console.error('Unhandled:', reason);
});
```

## Common Interview Topics

| Topic | Key Points |
|-------|-----------|
| **Event Loop** | Non-blocking I/O, phases, microtasks vs macrotasks |
| **Streams** | Memory-efficient data processing, backpressure |
| **Clustering** | `cluster` module, spawn workers per CPU core |
| **child_process** | `exec`, `spawn`, `fork` for running subprocesses |
| **Buffer** | Binary data handling |
| **Global objects** | `__dirname`, `__filename`, `process`, `global` |
| **npm** | `package.json`, `node_modules`, `package-lock.json` |

## Performance Tips

- Use streaming for large files instead of loading them into memory
- Use `cluster` or worker threads for CPU-bound tasks
- Avoid synchronous operations (`fs.readFileSync`) in production
- Use connection pooling for databases

## References

- [Node.js Official Docs](https://nodejs.org/docs/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
