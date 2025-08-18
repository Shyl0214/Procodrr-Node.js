# 📖 In-Depth: Readable Streams in Node.js

## 🔹 What is a Readable Stream?
A **Readable Stream** in Node.js is a type of stream used to **read data from a source** (like files, network requests, or processes) in **small chunks**, instead of loading the whole content into memory at once.

👉 This makes it **memory efficient** and ideal for handling **large files or continuous input**.

## 🔹 Common Use Cases
1. Reading data from a **file**
2. Reading data from a **network request**
3. Reading data from a **process** (e.g., `process.stdin`)

## 🔹 Key Notes
* To read files, we use `fs.createReadStream()`.
* Data is received in **chunks** (each chunk is a **Buffer** object by default).
* Important **events**:
   * `data` → Fired whenever a new chunk is available.
   * `end` → Fired when no more data is left to read.
   * `error` → Fired if something goes wrong.

## 🔹 Basic Example

```javascript
import fs from "fs";

// Create a stream to read from a text file
const readStream = fs.createReadStream("example.txt");

// 'data' event → reads data in chunks
readStream.on("data", (chunk) => {
  console.log("Received chunk:", chunk);
});

// 'end' event → called when file reading is complete
readStream.on("end", () => {
  console.log("Finished reading the file.");
});

// 'error' event → in case something goes wrong
readStream.on("error", (err) => {
  console.log("Error while reading:", err.message);
});
```

## 🔹 Advanced Example: Reading Large Files
By default, Node.js uses a **buffer size (**`highWaterMark`) of 64 KB per chunk. You can customize this to change how much data is read in one go.

### 📝 Example 1: Default `highWaterMark` (64 KB)

```javascript
import fs from "fs";

const path =
  "C://Users//shail//Downloads//Telegram Desktop//Namaste Node.js//Season - 1//14-Episode-13 . Creating a database & mongodb.mp4";

// Default highWaterMark = 64 KB
const readStream = fs.createReadStream(path);

console.time();

readStream.on("data", (chunk) => {
  console.log("Received chunk:", chunk.byteLength);
});

readStream.on("end", () => {
  console.log("Finished reading the stream");
  console.timeEnd();
});

readStream.on("error", (err) => {
  console.log("Read Stream Error:", err.message);
});
```

📌 **Output**:
* Chunks of ~64 KB are read each time.
* Total time taken: ~ **1.494s**

### 📝 Example 2: Increased `highWaterMark` (10 MB)

```javascript
import fs from "fs";

const path =
  "C://Users//shail//Downloads//Telegram Desktop//Namaste Node.js//Season - 1//14-Episode-13 . Creating a database & mongodb.mp4";

// Increase buffer size to 10 MB
const readStream = fs.createReadStream(path, {
  highWaterMark: 10 * 1024 * 1024,
});

console.time();

readStream.on("data", (chunk) => {
  console.log("Received chunk:", chunk.byteLength);
});

readStream.on("end", () => {
  console.log("Finished reading the stream");
  console.timeEnd();
});

readStream.on("error", (err) => {
  console.log("Read Stream Error:", err.message);
});
```

📌 **Output**:
* Chunks of ~10 MB are read each time.
* Total time taken: ~ **242 ms** (faster because fewer chunks = fewer event cycles).

⚠️ **Important Note**: Increasing `highWaterMark` improves performance but also increases **memory usage**. Be careful with very large values.

## 🔹 Summary
* **Readable Stream** → Reads data chunk by chunk.
* Uses **events** (`data`, `end`, `error`) to handle data flow.
* `highWaterMark` controls chunk size:
   * Small value → less memory, slower performance
   * Large value → more memory, faster performance
* Perfect for **large files** and **continuous input sources**.