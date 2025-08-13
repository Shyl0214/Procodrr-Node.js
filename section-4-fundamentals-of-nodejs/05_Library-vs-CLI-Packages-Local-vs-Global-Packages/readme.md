# 📦 Library vs CLI Packages & Local vs Global Packages — Beginner's Guide

When you work with **Node.js** or **JavaScript projects**, you'll often install different kinds of packages. Some are used **inside your code**, while others are run **from the terminal**. Let's break them down.

## 1️⃣ Library Packages vs CLI (Command Line Interface) Packages

### 1. Library Packages

- **Purpose:** Meant to be **used in your code**.
- You install them, then **import** or **require** them in your JavaScript/TypeScript files.
- **They do not** come with a terminal command to run directly.

**Example:**

```bash
npm install axios
```

Usage in code:

```javascript
import axios from "axios";

axios.get("https://api.example.com/data")
  .then(res => console.log(res.data));
```

Here, **Axios** is a library package — you use it **inside** your code, not directly from the terminal.

### 2. CLI Packages

- **Purpose:** Meant to be **run from the terminal** to perform some task.
- When installed, they often have a `bin` entry that tells Node.js how to run them from the command line.
- Example tasks: running a dev server, building a project, formatting code, etc.

**Example:**

```bash
npm install vite
```

Running it locally:

```bash
npx vite
```

Here, **Vite** is a CLI tool — you **execute it** from the terminal to start your dev server.

💡 **Rule of thumb:**
- Library packages → Used inside your JavaScript/TypeScript code.
- CLI packages → Run from the terminal.

## 2️⃣ Installing CLI Packages the Right Way

When you're installing tools like **Vite**, **ESLint**, or **Prettier** (which are only needed while developing), you should install them as **dev dependencies** so they don't end up in your production build.

```bash
npm install vite --save-dev
# or shorthand
npm i vite -D
```

## 3️⃣ Running CLI Tools

After installing a CLI tool locally, you can run it without installing globally by using:

```bash
npx <tool-name>
```

Example:

```bash
npx vite
```

## 4️⃣ Local vs Global Packages

### Local Packages

- Installed **inside your project** in the `node_modules` folder.
- Only available for that project.
- Added to your `package.json` dependencies.

**Command to install locally:**

```bash
npm install <package-name>
```

Example:

```bash
npm install axios # Only available in this project
```

### Global Packages

- Installed **system-wide**.
- Can be used from **any** project or directory.
- Often used for tools you run frequently without needing them per-project.

**Command to install globally:**

```bash
npm install <package-name> -g
```

Example:

```bash
npm install vite -g
vite # Can run from anywhere
```

## ✅ Quick Summary

| Feature | Library Package | CLI Package |
|---------|----------------|-------------|
| **Used in code?** | ✅ Yes | ❌ No |
| **Run in terminal?** | ❌ No | ✅ Yes |
| **Example** | axios | vite |
| **Installed as** | `dependencies` | usually `devDependencies` |

**Local Packages** → Installed in the project folder, only available there.  
**Global Packages** → Installed system-wide, available anywhere.

## 🏗️ Real-world Analogy

- **Library package** = A toolbox **inside** your workshop (your code uses it).
- **CLI package** = A worker you call to do a specific task (you run it from the terminal).
- **Local install** = Tools kept in *this* workshop only.
- **Global install** = Tools you keep in your *truck*, so you can take them anywhere.

## 📋 Common Examples

### Popular Library Packages
- `axios` - HTTP client for making API requests
- `lodash` - Utility functions for JavaScript
- `moment` - Date manipulation library
- `express` - Web framework for Node.js

### Popular CLI Packages
- `vite` - Build tool and dev server
- `eslint` - Code linting tool
- `prettier` - Code formatter
- `nodemon` - Auto-restart Node.js apps during development

### Popular Global CLI Tools
- `npm` - Node package manager (comes with Node.js)
- `yarn` - Alternative package manager
- `create-react-app` - React app generator
- `typescript` - TypeScript compiler

## 🎯 Best Practices

1. **Install CLI dev tools locally** when possible to ensure version consistency across your team
2. **Use `npx`** to run locally installed CLI tools
3. **Only install globally** if you need the tool across multiple projects
4. **Use `--save-dev` (-D)** for development-only tools
5. **Check your `package.json`** to see what's installed locally

## 🚀 Quick Commands Reference

| Action | Command |
|--------|---------|
| Install library locally | `npm install package-name` |
| Install CLI tool locally (dev) | `npm install package-name --save-dev` |
| Install globally | `npm install package-name -g` |
| Run local CLI tool | `npx tool-name` |
| List local packages | `npm list` |
| List global packages | `npm list -g --depth=0` |