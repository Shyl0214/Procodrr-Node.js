# 📖 Reading a File with File Descriptor

After opening a file and getting its **File Descriptor (FD)**, you can read the file's content using `fs.read()` in Node.js.

Think of it like this: 👉 **Open file → Get FD → Use FD to read data into a buffer.**

## 🟢 Basic Usage

```javascript
import fs from "fs";

const fd = fs.openSync("sample.txt", "r");

fs.read(fd, (err, bytesRead, bufferData) => {
  console.log("Error:", err);
  console.log("Bytes Read:", bytesRead);
  console.log("Buffer Data:", bufferData.toString());
  console.log("Buffer Size:", bufferData.byteLength); // Default = 16 KB
});
```

### 🔍 What happens here?

* If no buffer is passed, Node.js **creates a default buffer of 16 KB**.
* `bytesRead` → How many bytes were actually read.
* `bufferData` → A **Buffer object** that holds the file content.

## 🟢 Using a Custom Buffer

You can control **how much data gets read** by creating your own buffer.

```javascript
import fs from "fs";

const fd = fs.openSync("sample.txt", "r");

fs.read(fd, { buffer: Buffer.alloc(10) }, (err, bytesRead, bufferData) => {
  console.log("Bytes Read:", bytesRead); // ≤ 10
  console.log("Data:", bufferData.toString());
});
```

* `Buffer.alloc(10)` → Creates a 10-byte buffer.
* Reads **up to 10 bytes** at a time (instead of 16 KB).

👉 This is useful if you want to read files in **small chunks**.

## 🟢 Reading from a Specific Position

You can also read data **from a specific place in the file**.

```javascript
fs.read(fd, {
  buffer: Buffer.alloc(5),
  offset: 0,        // Start writing in buffer from index 0
  length: 5,        // Number of bytes to read
  position: 10      // Start reading from the 10th byte in the file
}, (err, bytesRead, bufferData) => {
  console.log("Bytes Read:", bytesRead);
  console.log("Data:", bufferData.toString());
});
```

### ⚡ Explanation

* `offset` → Where to begin writing inside the buffer.
* `length` → How many bytes to read.
* `position` → Where to start reading from in the file.

## 🧾 Key Parameters in `fs.read()`

| Parameter | Purpose |
|-----------|---------|
| `fd` | File descriptor (from `fs.open` / `fs.openSync`). |
| `buffer` | Buffer that stores the read data. |
| `offset` | Start writing in the buffer at this index. |
| `length` | Number of bytes to read. |
| `position` | File position to start reading (if `null`, uses current position). |

## 🎯 Quick Summary

* `fs.read()` lets you **read data into a buffer** using a File Descriptor.
* By default, Node uses a **16 KB buffer**.
* You can control **size** (using custom buffer) and **location** (using `position`).
* **Useful for**:
   * **Reading large files in chunks**.
   * **Random access** (jumping to specific parts of a file).

---

> **Don't forget**: Always close the file descriptor with `fs.close(fd)` or `fs.closeSync(fd)` when you're done reading!