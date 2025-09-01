# 🔒 Closing Writable Streams in Node.js

Writable streams should always be **properly closed** once you're done writing data.

## 🛑 Why Close Streams?

* If a writable stream is **left open**, it will:
   * Keep holding system resources.
   * Continue consuming **RAM** unnecessarily.
* Properly closing ensures the file is saved and resources are freed.

## ✅ How to Close a Writable Stream

Use the `.end()` method:

```javascript
writeStream.end();
```

* Signals that you're done writing.
* After calling `.end()`, you **cannot call** `.write()` anymore.
* Triggers important lifecycle events like `finish` and `close`.

## 📖 Example 1: Without `.end()`

```javascript
import fs from "node:fs";

let readStream = fs.createReadStream("input.mp4", {
  highWaterMark: 1 * 1024 * 1024, // 1MB
});

let writeStream = fs.createWriteStream("output.mp4");

readStream.on("data", (chunk) => {
  let canWrite = writeStream.write(chunk);
  if (!canWrite) {
    readStream.pause();
  }
});

writeStream.on("drain", () => {
  readStream.resume();
});

// Try listening to finish & close
writeStream.on("finish", () => {
  console.log("Finished");
});

writeStream.on("close", () => {
  console.log("Closed");
});
```

### ❌ Output:
The `finish` and `close` events are **never triggered**. Why? → Because we never told the writable stream that writing is done.

## 📖 Example 2: With `.end()`

```javascript
import fs from "node:fs";

let readStream = fs.createReadStream("input.mp4", {
  highWaterMark: 1 * 1024 * 1024, // 1MB
});

let writeStream = fs.createWriteStream("output.mp4");

readStream.on("data", (chunk) => {
  let canWrite = writeStream.write(chunk);
  if (!canWrite) {
    readStream.pause();
  }
});

writeStream.on("drain", () => {
  readStream.resume();
});

// ✅ Close the writable stream after writing
writeStream.end();

writeStream.on("finish", () => {
  console.log("Finished");
});

writeStream.on("close", () => {
  console.log("Closed");
});
```

### ✅ Output:

```
Finished
Closed
```

Now both events fire correctly because the writable stream knows when to stop.

## 📌 Key Events

* `finish` → Emitted when all data has been flushed to the underlying system.
* `close` → Emitted when the stream and its resources have been fully closed.

## ⚡ Key Takeaways

1. Always call `.end()` when you're done writing.
2. Without `.end()`, `finish` and `close` won't trigger.
3. Use `finish` to confirm all data is written.
4. Use `close` to confirm the stream is fully released.