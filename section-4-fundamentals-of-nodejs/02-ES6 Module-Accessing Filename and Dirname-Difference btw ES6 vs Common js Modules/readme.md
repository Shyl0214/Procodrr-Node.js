# ES6 Modules - Complete Guide

## Table of Contents
- [What are ES6 Modules?](#what-are-es6-modules)
- [ES6 Module Syntax](#es6-module-syntax)
- [Accessing Filename & Dirname in ES6 Modules](#accessing-filename--dirname-in-es6-modules)
- [Difference between CommonJS and ES6 Modules](#difference-between-commonjs-and-es6-modules)
- [Examples](#examples)
- [Best Practices](#best-practices)

## What are ES6 Modules?

**ES6 Modules** (also called ECMAScript Modules or ESM) are the official standard module system for JavaScript, introduced in ES2015 (ES6). They provide a more modern, cleaner syntax for importing and exporting code between files.

### Key Features:
- 🎯 **Static Analysis**: Imports/exports are determined at compile time
- 🚀 **Tree Shaking**: Unused code can be eliminated during bundling
- 📦 **Standard**: Official JavaScript specification
- 🔒 **Strict Mode**: Always runs in strict mode
- ⚡ **Asynchronous**: Module loading is asynchronous by default

### Real-world Analogy:
If CommonJS modules are like **phone calls** (you call and wait for a response), ES6 modules are like **text messages** (you send what you need, and it arrives when ready).

## ES6 Module Syntax

### 1. Exporting Code

#### Named Exports
```javascript
// utilities.js

// Method 1: Export while declaring
export function add(a, b) {
    return a + b;
}

export const PI = 3.14159;

export class Calculator {
    multiply(a, b) {
        return a * b;
    }
}

// Method 2: Export after declaring
function subtract(a, b) {
    return a - b;
}

const E = 2.71828;

export { subtract, E };

// Method 3: Export with different names
function divide(a, b) {
    return a / b;
}

export { divide as div };
```

#### Default Exports
```javascript
// logger.js

// Method 1: Export default function
export default function log(message) {
    console.log(`[LOG]: ${message}`);
}

// Method 2: Export default class
export default class User {
    constructor(name) {
        this.name = name;
    }
}

// Method 3: Export default value
const config = {
    apiUrl: 'https://api.example.com',
    timeout: 5000
};

export default config;

// Method 4: Mixed exports (default + named)
export const version = '1.0.0';
export default config;
```

### 2. Importing Code

#### Named Imports
```javascript
// main.js

// Import specific functions
import { add, PI, Calculator } from './utilities.js';

console.log(add(5, 3)); // 8
console.log(PI); // 3.14159

// Import with different names
import { subtract as minus, E } from './utilities.js';
console.log(minus(10, 4)); // 6

// Import with alias for renamed exports
import { div } from './utilities.js';
console.log(div(10, 2)); // 5

// Import all named exports
import * as utils from './utilities.js';
console.log(utils.add(2, 3)); // 5
console.log(utils.PI); // 3.14159
```

#### Default Imports
```javascript
// app.js

// Import default export (can use any name)
import logger from './logger.js';
import User from './user.js';
import config from './config.js';

logger('Application started');
const user = new User('John');
console.log(config.apiUrl);

// Mixed imports (default + named)
import config, { version } from './config.js';
console.log(config.apiUrl);
console.log(version); // 1.0.0
```

#### Dynamic Imports
```javascript
// Dynamic imports (returns a Promise)
async function loadModule() {
    const { add, PI } = await import('./utilities.js');
    console.log(add(5, 3)); // 8
}

// Or with .then()
import('./utilities.js')
    .then(({ add, PI }) => {
        console.log(add(2, 3)); // 5
    });
```

## Accessing Filename & Dirname in ES6 Modules

Unlike CommonJS, ES6 modules **don't have** `__filename` and `__dirname` variables automatically available. Here's how to get them:

### Method 1: Using `import.meta.url`
```javascript
// fileInfo.js
import { fileURLToPath } from 'url';
import { dirname, basename } from 'path';

// Get current file URL
console.log('File URL:', import.meta.url);
// Output: file:///path/to/your/fileInfo.js

// Convert URL to file path (equivalent to __filename)
const __filename = fileURLToPath(import.meta.url);
console.log('Filename:', __filename);
// Output: /path/to/your/fileInfo.js

// Get directory path (equivalent to __dirname)
const __dirname = dirname(__filename);
console.log('Directory:', __dirname);
// Output: /path/to/your

// Get just the filename
const filename = basename(__filename);
console.log('Just filename:', filename);
// Output: fileInfo.js
```

### Method 2: Creating Utility Functions
```javascript
// pathUtils.js
import { fileURLToPath } from 'url';
import { dirname } from 'path';

export function getCurrentFilename(metaUrl) {
    return fileURLToPath(metaUrl);
}

export function getCurrentDirname(metaUrl) {
    return dirname(fileURLToPath(metaUrl));
}

// Usage in other files
// someModule.js
import { getCurrentFilename, getCurrentDirname } from './pathUtils.js';

const __filename = getCurrentFilename(import.meta.url);
const __dirname = getCurrentDirname(import.meta.url);

console.log('Current file:', __filename);
console.log('Current directory:', __dirname);
```

### Method 3: One-liner Helper
```javascript
// helpers.js
import { fileURLToPath } from 'url';
import { dirname } from 'path';

export const getDirname = (metaUrl) => dirname(fileURLToPath(metaUrl));
export const getFilename = (metaUrl) => fileURLToPath(metaUrl);

// Usage
// app.js
import { getDirname, getFilename } from './helpers.js';

const __dirname = getDirname(import.meta.url);
const __filename = getFilename(import.meta.url);
```

### Practical Example:
```javascript
// config.js
import { fileURLToPath } from 'url';
import { dirname, join } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

// Read a file in the same directory
export const configPath = join(__dirname, 'config.json');
export const publicPath = join(__dirname, '..', 'public');

console.log('Config path:', configPath);
console.log('Public path:', publicPath);
```

## Difference between CommonJS and ES6 Modules

| Feature | CommonJS | ES6 Modules |
|---------|----------|-------------|
| **Syntax** | `require()` / `module.exports` | `import` / `export` |
| **Loading** | Synchronous | Asynchronous |
| **When resolved** | Runtime | Compile time |
| **File extension** | `.js` (default) | `.mjs` or `.js` with `"type": "module"` |
| **Strict mode** | Optional | Always |
| **Top-level await** | ❌ No | ✅ Yes |
| **Tree shaking** | ❌ Limited | ✅ Full support |
| **Circular dependencies** | ✅ Handled | ⚠️ Limited support |
| **Dynamic imports** | ✅ `require()` | ✅ `import()` |
| **`__filename`/`__dirname`** | ✅ Available | ❌ Use `import.meta.url` |
| **Browser support** | ❌ No (needs bundler) | ✅ Native |

### Syntax Comparison

#### CommonJS
```javascript
// math.js (CommonJS)
function add(a, b) {
    return a + b;
}

function multiply(a, b) {
    return a * b;
}

module.exports = { add, multiply };

// app.js (CommonJS)
const { add, multiply } = require('./math.js');
console.log(add(2, 3)); // 5
```

#### ES6 Modules
```javascript
// math.js (ES6)
export function add(a, b) {
    return a + b;
}

export function multiply(a, b) {
    return a * b;
}

// app.js (ES6)
import { add, multiply } from './math.js';
console.log(add(2, 3)); // 5
```

### Loading Behavior Comparison

#### CommonJS - Synchronous
```javascript
console.log('Before require');
const math = require('./math.js'); // Blocks here until loaded
console.log('After require');
console.log(math.add(2, 3));
```

#### ES6 - Asynchronous
```javascript
console.log('Before import');
import { add } from './math.js'; // Doesn't block
console.log('After import');
// All imports are loaded before any code runs
console.log(add(2, 3));
```

### File Extensions and Package.json

#### For ES6 Modules:

**Option 1: Use `.mjs` extension**
```javascript
// math.mjs
export const add = (a, b) => a + b;

// app.mjs
import { add } from './math.mjs';
```

**Option 2: Set type in package.json**
```json
{
    "type": "module",
    "name": "my-app"
}
```

**Option 3: Mixed approach**
```json
{
    "type": "module",
    "exports": {
        "./commonjs": "./lib/commonjs/index.js",
        "./esm": "./lib/esm/index.mjs"
    }
}
```

### Browser vs Node.js

#### Browser (ES6 Modules)
```html
<!DOCTYPE html>
<html>
<head>
    <script type="module">
        import { greet } from './greet.js';
        greet('World');
    </script>
</head>
</html>
```

#### Node.js (Both supported)
```javascript
// CommonJS (traditional)
const fs = require('fs');

// ES6 Modules (modern)
import fs from 'fs';
```

## Best Practices

### 1. Use Descriptive Import Names
```javascript
// ✅ Good
import { validateEmail, formatDate } from './utils.js';
import UserService from './services/UserService.js';

// ❌ Bad
import { v, f } from './utils.js';
import S from './services/UserService.js';
```

### 2. Group Imports Logically
```javascript
// ✅ Good - Group by type
// Built-in modules
import fs from 'fs';
import path from 'path';

// Third-party modules
import express from 'express';
import lodash from 'lodash';

// Local modules
import { config } from './config.js';
import UserModel from './models/User.js';
```

### 3. Use Default Exports for Single-Purpose Modules
```javascript
// ✅ Good for single class/function
// Logger.js
export default class Logger {
    log(message) {
        console.log(message);
    }
}

// ✅ Good for multiple related functions
// utils.js
export function validateEmail(email) { /* */ }
export function formatDate(date) { /* */ }
export function sanitizeInput(input) { /* */ }
```

### 4. Create Path Utilities for __dirname/__filename
```javascript
// pathUtils.js
import { fileURLToPath } from 'url';
import { dirname } from 'path';

export const createPathUtils = (metaUrl) => ({
    __filename: fileURLToPath(metaUrl),
    __dirname: dirname(fileURLToPath(metaUrl))
});

// Usage in any module
import { createPathUtils } from './pathUtils.js';
const { __filename, __dirname } = createPathUtils(import.meta.url);
```

### 5. Use Dynamic Imports for Code Splitting
```javascript
// ✅ Good for large modules or conditional loading
async function handleUserAction(action) {
    if (action === 'generateReport') {
        // Only load heavy reporting module when needed
        const { generateReport } = await import('./reporting.js');
        return generateReport();
    }
}
```

## Migration from CommonJS to ES6

### Step-by-Step Migration:

1. **Update package.json**
   ```json
   {
       "type": "module"
   }
   ```

2. **Convert exports**
   ```javascript
   // Before (CommonJS)
   module.exports = { add, subtract };
   
   // After (ES6)
   export { add, subtract };
   ```

3. **Convert imports**
   ```javascript
   // Before (CommonJS)
   const { add } = require('./math.js');
   
   // After (ES6)
   import { add } from './math.js';
   ```

4. **Handle __filename/__dirname**
   ```javascript
   // Before (CommonJS)
   console.log(__filename);
   console.log(__dirname);
   
   // After (ES6)
   import { fileURLToPath } from 'url';
   import { dirname } from 'path';
   
   const __filename = fileURLToPath(import.meta.url);
   const __dirname = dirname(__filename);
   ```

### Common Migration Issues:

| Issue | Solution |
|-------|----------|
| `require is not defined` | Change to `import` statements |
| `__dirname is not defined` | Use `dirname(fileURLToPath(import.meta.url))` |
| File extension required | Add `.js` to import paths |
| JSON imports | Use `import config from './config.json' assert { type: 'json' }` |

## Quick Reference

### Export Patterns
```javascript
// Named exports
export const value = 42;
export function func() {}
export { func1, func2 };

// Default exports
export default function() {}
export default class MyClass {}
export default value;

// Re-exports
export { func } from './other.js';
export * from './other.js';
```

### Import Patterns
```javascript
// Named imports
import { name } from './module.js';
import { name as alias } from './module.js';
import * as all from './module.js';

// Default imports
import defaultExport from './module.js';
import defaultExport, { named } from './module.js';

// Dynamic imports
const module = await import('./module.js');
```

### Path Utilities
```javascript
import { fileURLToPath } from 'url';
import { dirname, join, basename } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const configPath = join(__dirname, 'config.json');
```

---

