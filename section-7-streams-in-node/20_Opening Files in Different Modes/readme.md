# 📂 Opening Files in Different Modes

In Node.js, when you open a file using `fs.open()` or `fs.openSync()`, the **second argument** (`mode`) tells Node.js *how the file should be accessed*.

```javascript
const fd = fs.openSync(path, mode);
```

* `path` → The file's location.
* `mode` → Defines whether you want to read, write, or append.

## 🟢 Default Behavior

* If you don't pass a mode, Node.js will use `"r"` (read-only) by default.
* This means the file must already exist, otherwise you'll get an error.

```javascript
// These are equivalent:
const fd1 = fs.openSync("file.txt");
const fd2 = fs.openSync("file.txt", "r");
```

## 🟢 Example: Write Mode

```javascript
import fs from "fs";

const fd = fs.openSync("example.txt", "w"); // "w" = write mode
fs.writeSync(fd, "Hello World!");
fs.closeSync(fd);
```

👉 This creates a file named **example.txt** (if it doesn't already exist) and writes **"Hello World!"** into it.

👉 If the file already exists, it **erases all old content** before writing.

## 🟢 File Open Modes

| Mode | Purpose | Notes |
|------|---------|-------|
| `"r"` | **Read only** | File **must exist**, otherwise error. |
| `"w"` | **Write only** | Creates file if not exists. **Clears existing content**. |
| `"a"` | **Append only** | Creates file if not exists. Adds data at the **end**. |
| `"r+"` | **Read + Write** | File must exist. Can read & overwrite from start. |
| `"w+"` | **Read + Write** | Creates file if not exists. **Clears content first**. |
| `"a+"` | **Read + Append** | Creates file if not exists. Can read, but writing always happens at the **end**. |

## 🟢 Practical Examples

### Reading a File (`"r"`)
```javascript
import fs from "fs";

try {
  const fd = fs.openSync("data.txt", "r");
  const buffer = Buffer.alloc(1024);
  const bytesRead = fs.readSync(fd, buffer);
  console.log("Content:", buffer.toString('utf8', 0, bytesRead));
  fs.closeSync(fd);
} catch (error) {
  console.log("Error: File doesn't exist or can't be read");
}
```

### Writing to a File (`"w"`)
```javascript
import fs from "fs";

const fd = fs.openSync("output.txt", "w");
fs.writeSync(fd, "This will replace all existing content!\n");
fs.writeSync(fd, "Line 2");
fs.closeSync(fd);
```

### Appending to a File (`"a"`)
```javascript
import fs from "fs";

const fd = fs.openSync("log.txt", "a");
fs.writeSync(fd, `${new Date().toISOString()}: New log entry\n`);
fs.closeSync(fd);
```

### Read and Write (`"r+"`)
```javascript
import fs from "fs";

const fd = fs.openSync("config.txt", "r+");
// Read existing content
const buffer = Buffer.alloc(100);
const bytesRead = fs.readSync(fd, buffer, 0, 100, 0);
console.log("Existing:", buffer.toString('utf8', 0, bytesRead));

// Overwrite from the beginning
fs.writeSync(fd, "Updated content", 0);
fs.closeSync(fd);
```

## 🧠 Quick Trick to Remember

* **Single letter** → main action only:
   * **r = read**
   * **w = write**
   * **a = append**
* Adding a **"+"** → allows **both read & write**.

## ⚠️ Important Warnings

* **`"w"` and `"w+"` are destructive!** They immediately clear the file content.
* Always use **`"a"` or `"a+"`** if you want to preserve existing data.
* **`"r+"` requires the file to exist** - use `"w+"` if you want to create it.

## 🛠️ Best Practices

1. **Always close file descriptors** using `fs.closeSync(fd)` or `fs.close(fd)`
2. **Use try-catch blocks** when opening files that might not exist
3. **Choose the right mode** based on your needs:
   - Logging → `"a"` (append)
   - Configuration updates → `"r+"` (read + write)
   - Creating new files → `"w"` (write)
   - Reading data → `"r"` (read)

## 🎯 Summary

* `"r"` → Only read. File must exist.
* `"w"` → Only write. Creates/clears file.
* `"a"` → Only append. Creates file if missing.
* Add `"+"` → Mix of read + write.

---

> **Pro tip**: When in doubt, start with `"r"` for reading and `"a"` for writing to avoid accidentally losing data!