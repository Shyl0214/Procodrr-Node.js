# 📂 What is a File Descriptor?

A **File Descriptor (FD)** is a **number (0 or greater)** given by the **Operating System** to identify an **open file** or any **I/O resource** (like keyboard, console, or network sockets).

👉 Think of it as an **address tag** that the OS uses to know *which file or resource you are working with* during **read** and **write** operations.

## 🔢 Standard File Descriptors

When you run a program, three file descriptors are created by default:

| FD | Stream | Purpose |
|----|--------|---------|
| 0 | **stdin** | Input (keyboard) |
| 1 | **stdout** | Output (console) |
| 2 | **stderr** | Errors (console) |

📌 **Note**: In Node.js, any **newly opened file** usually gets a descriptor number **3 or higher**.

## 🛠️ Accessing File Descriptors in Node.js

Node.js provides two ways to open files and see their **File Descriptors**.

### ✅ Asynchronous Way

```javascript
import fs from "fs";

fs.open("file.txt", "r", (err, fd) => {
  if (err) throw err;
  console.log(fd); // e.g., 3 or greater
});
```

* Uses a **callback** function.
* Non-blocking → Node.js continues running other code while opening the file.

### ✅ Synchronous Way

```javascript
import { openSync } from "fs";

const fd = openSync("file.txt", "r");
console.log(fd); // e.g., 3 or greater
```

* **Blocks execution** until the file is opened.
* Simpler, but not recommended for performance-heavy applications.

## 🧠 Why Are File Descriptors Important?

* Every open **file/stream** in Node.js has a **File Descriptor**.
* They are used internally for **low-level I/O operations**.
* **Benefits**:
   * Fine control over **reading/writing files**.
   * Using low-level methods like `fs.read`, `fs.write`, and `fs.close`.
   * Helpful in **debugging resource leaks** (when files are not closed properly).

## 🎯 Quick Summary

* **File Descriptor = ID for open files or resources.**
* **0 → stdin, 1 → stdout, 2 → stderr.**
* **Node.js files** get FDs starting from **3**.
* Can be accessed with **fs.open (async)** or **fs.openSync (sync)**.
* Crucial for **low-level control & debugging**.

---

> **Remember**: Always close file descriptors when you're done with them using `fs.close()` or `fs.closeSync()` to prevent resource leaks!