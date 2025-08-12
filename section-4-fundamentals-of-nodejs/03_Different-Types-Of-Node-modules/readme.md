# 🧩 Understanding Different Types of Modules in Node.js

In Node.js, **modules** are like separate "files" or "chunks" of code that you can reuse anywhere in your app. Think of them like **Lego blocks** — you build small pieces and connect them to make something bigger.

Node.js has **three main types** of modules:

## 1️⃣ Built-in Modules (Core Modules)

These come **pre-installed** with Node.js — no need to install them separately.

**Examples:** `fs` (file system), `path`, `http`, `os`, `url`, etc.

**Example Code:**

```js
// Importing a built-in module
const fs = require("fs");

// Using fs to read a file
fs.readFile("example.txt", "utf8", (err, data) => {
  if (err) {
    console.error("Error reading file:", err);
    return;
  }
  console.log("File content:", data);
});
```

💡 **Key point:** Just `require()` them — no `npm install` needed.

## 2️⃣ Local Modules

These are **your own files** or **custom code** you create.

**Example Structure:**

```
project/
├─ math.js
└─ app.js
```

**math.js**

```js
// Our custom local module
function add(a, b) {
  return a + b;
}

module.exports = add; // Exporting the function
```

**app.js**

```js
const add = require("./math"); // './' means local file

console.log(add(5, 3)); // Output: 8
```

💡 **Key point:** Use `./` to import from your own files.

## 3️⃣ Third-party Modules

These are **created by other developers** and shared on **npm** (Node Package Manager).

**Steps:**

1. Install the package:

```bash
npm install lodash
```

2. Use it in your code:

```js
const _ = require("lodash");

let numbers = [1, 2, 2, 3, 4, 4];
let uniqueNumbers = _.uniq(numbers);
console.log(uniqueNumbers); // Output: [1, 2, 3, 4]
```

💡 **Key point:** You need to install them before using.

## 📦 Summary Table

| Type | Source | Example Module | How to Use |
|------|--------|---------------|------------|
| Built-in | Comes with Node.js | `fs`, `path` | `require("fs")` |
| Local | Your own files | `math.js` | `require("./math")` |
| Third-party (npm) | Download from npm registry | `lodash` | Install → `require("lodash")` |

## 📝 Quick Tips

- `require` is used in CommonJS module system (default in Node.js before ES modules).
- Use `module.exports` to share your code from a file.
- Use **relative paths** (`./`) for local modules.
- Always `npm install` before using third-party modules.

## Getting Started

1. Make sure you have Node.js installed on your system
2. Create a new project directory
3. Initialize your project with `npm init -y` (optional, but recommended for third-party modules)
4. Start creating your modules and importing them as needed!

## Additional Resources

- [Node.js Official Documentation](https://nodejs.org/en/docs/)
- [npm Registry](https://www.npmjs.com/)
- [CommonJS vs ES Modules](https://nodejs.org/api/esm.html)