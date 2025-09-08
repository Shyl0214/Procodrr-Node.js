# 🔄 Piping and Redirection of Data Streams

In **Node.js** (and also in terminals), we can connect **data streams** between processes or files using **piping** and **redirection**.

## 🌊 1. Piping Streams

### 📌 What is Piping?

* **Piping** means sending the **output (stdout)** of one process directly into the **input (stdin)** of another process.
* This allows data to flow **directly** between processes, without creating temporary files.
* Commonly used in **Linux/Unix shells**.
* On Windows, you can try **WSL** or **Git Bash** for similar behavior.

### 🛠️ Syntax

```bash
command1 | command2
```

Here, the pipe symbol (`|`) connects `stdout` of the first command to `stdin` of the second.

### 🔹 Example 1: Echo into Node

```bash
echo hii | node index.js
```

1. `echo hii` produces `"hii"` as stdout.
2. That stdout is passed as stdin to `index.js`.

### 🔹 Example 2: Node to Node

```bash
node script.js | node index.js
```

1. `script.js` sends its output to stdout.
2. That output is piped into the stdin of `index.js`.

### 💡 **Remember:** Pipes connect only:

```
stdout → stdin
```

They do **not** connect stderr unless redirected separately.

## 📝 2. Redirection of Streams

### 📌 What is Redirection?

* **Redirection** sends a stream to a **file** or reads from a file.
* Instead of showing data in the terminal, it can be written to a file, or input can come from a file.

### 🔹 Redirect stdout → File

```bash
node script.js > output.txt
```

* `>` overwrites the file.
* Stdout of `script.js` goes into `output.txt`.

### 🔹 Redirect stderr → File

```bash
node script.js 2> errors.txt
```

* `2>` means redirect **stderr** (file descriptor 2) into `errors.txt`.

### 🔹 Redirect both stdout & stderr

```bash
node script.js > output.txt 2>> output.txt
```

* `>` → stdout goes to `output.txt` (overwrites).
* `2>>` → stderr goes to the same file but **appends**.

### 🔹 Input Redirection (File → stdin)

```bash
node index.js < input.txt
```

* `<` sends the contents of `input.txt` into stdin of `index.js`.

## 📌 3. Summary Table

| Symbol | Meaning | Example |
|--------|---------|---------|
| `\|` | Pipe stdout → stdin | `node app.js \| node other.js` |
| `>` | Redirect stdout (overwrite) | `node app.js > out.txt` |
| `>>` | Redirect stdout (append) | `node app.js >> out.txt` |
| `2>` | Redirect stderr (overwrite) | `node app.js 2> err.txt` |
| `2>>` | Redirect stderr (append) | `node app.js 2>> err.txt` |
| `<` | File → stdin | `node app.js < input.txt` |

## 💡 Key Takeaways

* **Piping** → connects process to process (**stream-to-stream**).
* **Redirection** → connects process to file (or file to process).
* File Descriptors:
   * `stdout` = **1**
   * `stderr` = **2**
* Pipe (`|`) only connects **stdout → stdin** by default.