# 🚀 Backpressure & Internal Buffer in Writable Streams (Node.js)

When you write data to a **writable stream** in Node.js, two important things come into play:
* 🛢 **Internal Buffer** → A temporary storage space for data before it's written.
* ⏸ **Backpressure** → A mechanism to prevent memory overload when the buffer is full.

## 🛢 Internal Buffer

* Every writable stream has an **internal buffer**.
* The size of this buffer is defined by the `highWaterMark`.
* Default values:
   * **Writable stream** → `16 KB` (16,384 bytes)
   * **Readable stream** → `64 KB`

👉 You can check these values like this:

```javascript
console.log(writeStream.writableHighWaterMark); // Default = 16384
console.log(writeStream.writableLength); // Current amount of data in buffer
```

## ⏸ What is Backpressure?

* **Backpressure happens** when the **buffer is full** and cannot accept more data.
* In such cases:
   * `write(chunk)` returns `false` → "Stop sending data, I'm full."
   * The writable stream emits a `drain` event once it's ready again.

💡 This prevents your app from **overloading memory** when the source (readable stream) is faster than the destination (writable stream).

## 📝 Behavior of `write()` Method

* `write(chunk)` returns:
   * ✅ `true` → Buffer has space → Safe to keep writing.
   * ❌ `false` → Buffer is full → Pause the readable stream until `'drain'`.

## 📖 Example: Manual Backpressure Handling

```javascript
import fs from "node:fs";

// 1MB chunks read from source video
let readStream = fs.createReadStream("input.mp4", {
  highWaterMark: 1 * 1024 * 1024,
});

// Writable stream to save data
let writeStream = fs.createWriteStream("output.mp4");

// Read data in chunks
readStream.on("data", (chunk) => {
  let canWrite = writeStream.write(chunk);

  if (!canWrite) {
    // If buffer is full → pause reading
    readStream.pause();
  }
});

// Resume reading when buffer drains
writeStream.on("drain", () => {
  readStream.resume();
});

// End of reading
readStream.on("end", () => {
  console.log("✅ Finished writing file");
});
```

## 📄 Code Walkthrough

1. **📥 Read Stream** → Reads `input.mp4` in **1MB chunks**.
2. **💾 Write Stream** → Writes data into `output.mp4`.
3. **🔁 Writing** → Each chunk is written with `write(chunk)`.
4. **⚠️ Backpressure** → If `write()` returns `false`, pause the `readStream`.
5. **🔄 Resume** → When `'drain'` fires, resume reading again.
6. **🏁 End** → When the source file is finished, log a completion message.

## ⚡ Key Takeaways

* `highWaterMark` sets the internal buffer size.
* **Backpressure** ensures data flows smoothly without crashing memory.
* Always **pause** when `write()` returns false, and **resume** on `'drain'`.