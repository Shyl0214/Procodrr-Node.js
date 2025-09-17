# 📝 Writing to a File with File Descriptor

In Node.js, low-level file writing follows **3 steps**:

1. **Open the file** → Get its File Descriptor (FD).
2. **Write data** → Using async (`fs.write`) or sync (`fs.writeSync`).
3. **Close the file** → To release resources and avoid issues.

## 🔹 1️⃣ Open the File in Write Mode

```javascript
import fs from "fs";

// "w" = Write mode (creates file if missing, clears content if it exists)
const fd = fs.openSync("file.txt", "w");
```

✨ Mode `"w"` behavior:
* ✅ Creates file if it **doesn't exist**.
* ✅ Clears (truncates) the file if it **already exists**.

## 🔹 2️⃣ Write to the File

✅ Asynchronous Version

```javascript
fs.write(fd, "Hello World", (err, bytesWritten, dataWritten) => {
    console.log("Error:", err);
    console.log("Bytes Written:", bytesWritten);
    console.log("String Written:", dataWritten);
});
```

* `bytesWritten` → How many bytes were actually written.
* `dataWritten` → The string that was written to the file.
* Runs **non-blocking** (other code can execute while writing).

✅ Synchronous Version

```javascript
const bytes = fs.writeSync(fd, "Hello Sync World");
console.log("Bytes Written (Sync):", bytes);
```

* Returns the number of bytes written.
* Runs **blocking** (waits until writing is done).

## 🔹 3️⃣ Close the File

```javascript
fs.closeSync(fd); // Always close after writing
```

⚡ Why close the file?
* Frees up system resources.
* Prevents accidental data corruption.
* Avoids **File Descriptor leaks** (when too many files stay open).

## 📌 Quick Summary Table

| Step | Function | Async | Sync |
|------|----------|-------|------|
| Open | `fs.open()` / `fs.openSync()` | ✅ | ✅ |
| Write | `fs.write()` | ✅ | ❌ |
| Write (sync) | `fs.writeSync()` | ❌ | ✅ |
| Close | `fs.close()` / `fs.closeSync()` | ✅ | ✅ |

## 🎯 Key Takeaways

* Always **open → write → close** in sequence.
* `"w"` mode creates or clears the file.
* Use **async** for non-blocking apps, **sync** for simple scripts.
* Closing files = good habit ✅.