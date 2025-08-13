# 📂 FS (File System) Module in Node.js — Beginner's Guide

The fs module in Node.js allows you to work with files — reading, writing, deleting, updating, etc. It has both synchronous and asynchronous methods.

## 1️⃣ Reading Files in Node.js

Node.js provides three main ways to read files:

1. `readFileSync()` → Synchronous (Blocking)
2. `readFile()` with a call-back → Asynchronous (Non-blocking)
3. `readFile()` with Promises → Asynchronous (Non-blocking, modern way)

### A. readFileSync() — Synchronous (Blocking)

- Blocks the main thread until the file is read completely.
- Returns a Buffer by default (binary data).
- Not recommended for performance-critical applications.

**Example:**

```javascript
import fs from "node:fs";

const fileContent = fs.readFileSync("./index.html");
console.log(fileContent); // Shows raw Buffer (0s and 1s)
```

#### Converting Buffer to Readable Text

You can make the content human-readable by:

**1. Using .toString()**

```javascript
const fileContent = fs.readFileSync("./index.html");
console.log(fileContent.toString());
```

**2. Passing encoding directly**

```javascript
const fileContent = fs.readFileSync("./index.html", "utf-8");
console.log(fileContent);
```

### B. readFile() with a Call-back — Asynchronous (Non-blocking)

- Runs in the background.
- Doesn't block the main thread.
- Uses a call-back function to handle results.
- Can lead to call-back hell in complex scenarios.

**Example:**

```javascript
import fs from "node:fs";

fs.readFile("./index.html", "utf-8", function (err, data) {
    if (err) {
        console.error("Error reading file:", err);
        return;
    }
    console.log(data);
});

console.log("This runs before file content is printed!");
```

### C. readFile() with Promises — Modern Asynchronous Way

- Uses `fs/promises` module.
- Works seamlessly with async/await.
- Preferred in modern Node.js projects.

**Example with await:**

```javascript
import fs from "node:fs/promises";

const fileContent = await fs.readFile("./index.html", "utf-8");
console.log(fileContent);
```

## 2️⃣ Synchronous vs Asynchronous — Example Comparison

### Synchronous (readFileSync)

```javascript
import fs from "node:fs";

console.log("1. Starting sync read");
const data = fs.readFileSync("./index.html", "utf-8"); // Blocks here
console.log("2. File content:", data);
console.log("3. This runs AFTER file is read");
```

**Output:**

```
1. Starting sync read
2. File content: <contents of index.html>
3. This runs AFTER file is read
```

### Asynchronous (readFile with Promises)

```javascript
import fs from "node:fs/promises";

console.log("1. Starting async read");

fs.readFile("./index.html", "utf-8").then(data => {
    console.log("2. File content:", data);
});

console.log("3. This runs immediately after starting read");
```

**Output:**

```
1. Starting async read
3. This runs immediately after starting read
2. File content: <contents of index.html>
```

💡 **Notice:** Step 3 appears before Step 2, because file reading happens in the background.

## 3️⃣ Understanding Async Behavior with an Example

```javascript
import fs from "node:fs/promises";

console.log("1. Start reading file...");

fs.readFile("./index.html", "utf-8").then((data) => {
    console.log("3. File content received:");
    console.log(data);
});

console.log("2. Doing other stuff while file is being read...");

let count = 0;
const interval = setInterval(() => {
    count++;
    console.log(`Tick ${count}`);
    if (count === 5) clearInterval(interval);
}, 300);
```

**Possible Output:**

```
1. Start reading file...
2. Doing other stuff while file is being read...
Tick 1
Tick 2
3. File content received:
Hello, world!
Tick 3
Tick 4
Tick 5
```

### 🔍 What's Happening Here?

1. Line 1 logs immediately.
2. `readFile` starts reading the file in the background.
3. Line 2 logs right away — it doesn't wait for the file.
4. The counter starts ticking every 300ms.
5. Once file reading finishes, Step 3 logs the content.
6. Counter continues ticking from where it left off.

## 4️⃣ Key Takeaways

- `readFileSync` → Blocks main thread, returns a Buffer by default.
- `readFile` (call-back) → Non-blocking, uses call-backs, can get messy for big apps.
- `readFile` (Promises) → Non-blocking, works great with async/await, preferred way.
- Always prefer asynchronous methods for better performance.

## 📋 Method Comparison Table

| Method | Type | Blocks Thread | Error Handling | Modern Usage |
|--------|------|---------------|----------------|--------------|
| `readFileSync()` | Synchronous | ✅ Yes | try/catch | ❌ Not recommended |
| `readFile()` + call-back | Asynchronous | ❌ No | Call-back parameter | ⚠️ Legacy |
| `readFile()` + Promise | Asynchronous | ❌ No | try/catch or .catch() | ✅ Recommended |

## 🚀 Best Practices

1. **Use async methods** - Always prefer asynchronous file operations
2. **Handle errors properly** - Always check for errors in call-backs or use try/catch with promises
3. **Specify encoding** - Use "utf-8" for text files to avoid Buffer conversion
4. **Use fs/promises** - Modern Promise-based API is cleaner than call-backs
5. **Avoid sync in production** - Synchronous methods can block your entire application

## 🔧 Common File Operations

### Writing Files

```javascript
import fs from "node:fs/promises";

// Write to file
await fs.writeFile("output.txt", "Hello, World!", "utf-8");

// Append to file
await fs.appendFile("output.txt", "\nNew line added!", "utf-8");
```

### Checking if File Exists

```javascript
import fs from "node:fs/promises";

try {
    await fs.access("file.txt");
    console.log("File exists");
} catch {
    console.log("File does not exist");
}
```

### Getting File Stats

```javascript
import fs from "node:fs/promises";

const stats = await fs.stat("file.txt");
console.log("File size:", stats.size);
console.log("Is directory:", stats.isDirectory());
console.log("Is file:", stats.isFile());
```

## 🛠️ Error Handling Examples

### With Promises (Recommended)

```javascript
import fs from "node:fs/promises";

try {
    const data = await fs.readFile("nonexistent.txt", "utf-8");
    console.log(data);
} catch (error) {
    console.error("Error reading file:", error.message);
}
```

### With Call-backs

```javascript
import fs from "node:fs";

fs.readFile("nonexistent.txt", "utf-8", (err, data) => {
    if (err) {
        console.error("Error reading file:", err.message);
        return;
    }
    console.log(data);
});
```

Remember: The fs module is your gateway to file system operations in Node.js. Master these patterns and you'll be able to handle any file-related task efficiently!