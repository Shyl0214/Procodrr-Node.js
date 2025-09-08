# 📘 Data Streams in Processes

## 1. What are `stdin`, `stdout`, and `stderr`?

Every process in an operating system works with **three main data streams**:

1. `stdin` (Standard Input) → Input data given by the user or another program.
2. `stdout` (Standard Output) → Normal output shown by the program (usually on the terminal).
3. `stderr` (Standard Error) → Error messages shown by the program.

👉 The prefix `std` means **standard**.

## 2. Example in a Bash Terminal

* If you type `ls`, this is taken as **stdin** (input from the keyboard).
* The list of files you see in the terminal is **stdout**.
* If you type something invalid like `asd`, the message `command not found` is **stderr**.

## 3. Using Streams in Node.js

In Node.js, these three streams are linked to the `process` object. We can use them to read/write data.

### Example 1: Writing to a File

```javascript
import fs from "node:fs";

const writeStream = fs.createWriteStream("output.txt");

// writing data using a writable stream
process.stdin.on("data", (chunk) => {
  writeStream.write(chunk);
});
```

✅ This creates a file `output.txt`. Anything you type in the terminal will be saved into this file.

### Example 2: Using `pipe()`

```javascript
import fs from "node:fs";

const writeStream = fs.createWriteStream("output.txt");

// writing data using pipe
process.stdin.pipe(writeStream);
```

✅ The `pipe()` method does the same job as above, but in a shorter way.

## 4. File Descriptors (FD)

Each stream also has a **File Descriptor (FD)**, which is just a number ID:

```javascript
console.log(process.stdin.fd);  // 0
console.log(process.stdout.fd); // 1
console.log(process.stderr.fd); // 2
```

* `stdin` → 0
* `stdout` → 1
* `stderr` → 2

## 📌 In short:

* `stdin` = input
* `stdout` = normal output
* `stderr` = error output
* All three are part of the process in Node.js and can also be connected to files.