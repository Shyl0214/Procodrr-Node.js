# Node.js Module System - Beginner's Guide

## Table of Contents
- [What is Module System in Node.js?](#what-is-module-system-in-nodejs)
- [CommonJS Module System](#commonjs-module-system)
- [Module Wrapper Function](#module-wrapper-function)
- [Examples](#examples)
- [Best Practices](#best-practices)

## What is Module System in Node.js?

The **Module System** in Node.js is a way to organize and structure your code by splitting it into separate files called **modules**. Each module can contain functions, variables, classes, or any other code that can be reused across your application.

### Benefits of Using Modules:
- 📁 **Organization**: Keep related code together
- ♻️ **Reusability**: Use the same code in multiple places
- 🔧 **Maintainability**: Easier to debug and update
- 👥 **Collaboration**: Multiple developers can work on different modules
- 🔒 **Encapsulation**: Variables are scoped to their module

### Real-world Analogy:
Think of modules like rooms in a house:
- Kitchen (cooking functions)
- Bedroom (sleeping/rest functions)  
- Living room (entertainment functions)

Each room has a specific purpose, just like each module should have a specific responsibility.

## CommonJS Module System

CommonJS is the module system that Node.js uses by default. It provides two main mechanisms:

### 1. `module.exports` - Exporting Code

This is how you make your code available to other files:

```javascript
// utilities.js

// Method 1: Export an object with multiple functions
function formatName(firstName, lastName) {
    return `${firstName} ${lastName}`;
}

function calculateAge(birthYear) {
    return new Date().getFullYear() - birthYear;
}

module.exports = {
    formatName: formatName,
    calculateAge: calculateAge
};

// Method 2: Export individual items
module.exports.PI = 3.14159;
module.exports.greet = function(name) {
    return `Hello, ${name}!`;
};

// Method 3: Export a single function/class
module.exports = function(message) {
    console.log(`LOG: ${message}`);
};
```

### 2. `require()` - Importing Code

This is how you use code from other modules:

```javascript
// main.js

// Import the entire module
const utils = require('./utilities.js');
console.log(utils.formatName('John', 'Doe')); // John Doe

// Import specific functions using destructuring
const { formatName, calculateAge } = require('./utilities.js');
console.log(formatName('Jane', 'Smith')); // Jane Smith
console.log(calculateAge(1990)); // Current year - 1990

// Import built-in Node.js modules
const fs = require('fs');
const path = require('path');
const http = require('http');
```

### Types of Modules:
1. **Local Modules**: Your own files (use relative paths: `./`, `../`)
2. **Built-in Modules**: Node.js core modules (`fs`, `http`, `path`)
3. **Third-party Modules**: NPM packages (`express`, `lodash`, `axios`)

## Module Wrapper Function

When Node.js loads your module, it automatically wraps your code in a function. This is called the **Module Wrapper Function**.

### What your code looks like:
```javascript
// myModule.js
const name = "John";
exports.greet = function() {
    return `Hello, ${name}!`;
};
```

### What Node.js actually executes:
```javascript
(function(exports, require, module, __filename, __dirname) {
    // Your code is placed here
    const name = "John";
    exports.greet = function() {
        return `Hello, ${name}!`;
    };
});
```

### Wrapper Function Parameters:

| Parameter | Description | Example |
|-----------|-------------|---------|
| `exports` | Shortcut to `module.exports` | `exports.myFunc = () => {}` |
| `require` | Function to import modules | `require('./myModule')` |
| `module` | Current module object | `module.exports = {}` |
| `__filename` | Full path to current file | `/home/user/app.js` |
| `__dirname` | Directory of current file | `/home/user/` |

### Example Usage:
```javascript
// debug.js
console.log('Current file:', __filename);
console.log('Current directory:', __dirname);
console.log('Module info:', module.id);

exports.getModuleInfo = function() {
    return {
        filename: __filename,
        dirname: __dirname,
        id: module.id
    };
};
```

## Examples

### Example 1: Math Utilities Module
```javascript
// math.js
const PI = 3.14159;

function add(a, b) {
    return a + b;
}

function multiply(a, b) {
    return a * b;
}

function circleArea(radius) {
    return PI * radius * radius;
}

module.exports = {
    add,
    multiply,
    circleArea,
    PI
};
```

```javascript
// calculator.js
const math = require('./math.js');

console.log(math.add(5, 3));           // 8
console.log(math.multiply(4, 7));      // 28
console.log(math.circleArea(5));       // 78.53975
console.log(math.PI);                  // 3.14159
```

### Example 2: User Management Module
```javascript
// user.js
class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
        this.id = Date.now();
    }

    getInfo() {
        return `${this.name} (${this.email})`;
    }
}

function createUser(name, email) {
    return new User(name, email);
}

function validateEmail(email) {
    return email.includes('@') && email.includes('.');
}

module.exports = {
    User,
    createUser,
    validateEmail
};
```

```javascript
// app.js
const { createUser, validateEmail } = require('./user.js');

if (validateEmail('john@example.com')) {
    const user = createUser('John Doe', 'john@example.com');
    console.log(user.getInfo()); // John Doe (john@example.com)
}
```

## Best Practices

### 1. Use Clear File Names
```javascript
// ✅ Good
const userController = require('./controllers/userController.js');
const emailService = require('./services/emailService.js');

// ❌ Bad
const stuff = require('./utils.js');
const helper = require('./h.js');
```

### 2. Export Objects for Multiple Functions
```javascript
// ✅ Good
module.exports = {
    createUser,
    deleteUser,
    updateUser
};

// ❌ Harder to manage
module.exports.createUser = createUser;
module.exports.deleteUser = deleteUser;
module.exports.updateUser = updateUser;
```

### 3. Use Destructuring for Clean Imports
```javascript
// ✅ Good
const { readFile, writeFile } = require('fs');
const { join, resolve } = require('path');

// ❌ Less clean
const fs = require('fs');
const path = require('path');
fs.readFile();
path.join();
```

### 4. Organize Your Project Structure
```
project/
├── controllers/
│   ├── userController.js
│   └── productController.js
├── services/
│   ├── emailService.js
│   └── paymentService.js
├── utils/
│   ├── validation.js
│   └── helpers.js
└── app.js
```

### 5. Handle Module Loading Errors
```javascript
try {
    const config = require('./config.js');
} catch (error) {
    console.error('Failed to load config:', error.message);
    process.exit(1);
}
```

## Quick Reference

| Action | Code | Description |
|--------|------|-------------|
| Export single function | `module.exports = myFunction` | Export one thing |
| Export multiple items | `module.exports = { func1, func2 }` | Export object |
| Import entire module | `const mod = require('./module')` | Get everything |
| Import specific items | `const { func1 } = require('./module')` | Destructure import |
| Import built-in module | `const fs = require('fs')` | Core Node.js module |
| Check current file | `console.log(__filename)` | Full file path |
| Check current directory | `console.log(__dirname)` | Directory path |

## Common Gotchas

1. **Forgetting the `./` for local modules**
   ```javascript
   // ❌ Wrong
   require('myModule'); // Looks in node_modules
   
   // ✅ Correct
   require('./myModule'); // Looks in current directory
   ```

2. **Circular dependencies** - Module A requires B, B requires A
   ```javascript
   // Avoid this pattern - redesign your modules
   ```

3. **Modifying require cache**
   ```javascript
   // Modules are cached after first require
   // Subsequent requires return the same instance
   ```

---

*This guide covers the fundamentals of Node.js modules using CommonJS. For modern JavaScript, also explore ES6 modules (`import`/`export`) which are increasingly supported in Node.js.*