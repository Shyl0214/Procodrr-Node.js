# Piping Streams in Node.js

Streams in Node.js allow us to read and write data efficiently, piece by piece, without loading the entire content into memory. When working with **file transfers** or **large data flows**, streams are the preferred method.

## 1. Writing Data Manually (Traditional Way)

Before introducing the `pipe()` method, let's look at the **manual approach** of transferring data from a readable stream to a writable stream.

Example:

```javascript
import fs from "node:fs";

let readStream = fs.createReadStream(
  "C://Users//shail//Downloads//Telegram Desktop//Harkirat Course 0 t o100//[Ad-hoc] 1. Next.js.mp4",
  { highWaterMark: 1 * 1024 * 1024 } // buffer size = 1 MB
);

let writeStream = fs.createWriteStream("video-mp4");

// Read and write manually
readStream.on("data", (chunk) => {
  let canWrite = writeStream.write(chunk);
  if (!canWrite) {
    readStream.pause(); // handle backpressure
  }
});

writeStream.on("drain", () => {
  readStream.resume();
});

readStream.on("end", () => {
  writeStream.end();
});

writeStream.on("finish", () => {
  console.log("Finished");
});

writeStream.on("close", () => {
  console.log("Closed");
});
```

✅ **Explanation:**
* `pause()` and `resume()` handle **backpressure** (when the writable stream can't keep up).
* Requires **manual management** of events.

## 2. Using the `pipe()` Method

The `pipe()` method provides a **simpler and more efficient** way to connect readable and writable streams.
It **automatically handles backpressure**, so we don't need to manually pause/resume streams.

Example:

```javascript
import fs from "node:fs";

let readStream = fs.createReadStream(
  "C://Users//shail//Downloads//Telegram Desktop//Harkirat Course 0 t o100//[Ad-hoc] 1. Next.js.mp4",
  { highWaterMark: 1 * 1024 * 1024 }
);

let writeStream = fs.createWriteStream("video-mp4");

// Pipe the readable stream into writable
readStream.pipe(writeStream);

console.log("Piping started...");

writeStream.on("finish", () => {
  console.log("Finished");
});

writeStream.on("close", () => {
  console.log("Closed");
});
```

✅ **Key Points:**
* No need to manually manage `pause()` and `resume()`.
* Best practice for **stream-to-stream transfer**.
* Great for **file copy, network streaming, zipping, compression** tasks.

## 3. Stopping Data Flow with `unpipe()`

Sometimes, you may want to **stop piping midway** (e.g., user cancels a download). In such cases, the `unpipe()` method can be used.

Example:

```javascript
import fs from "node:fs";

let readStream = fs.createReadStream(
  "C://Users//shail//Downloads//Telegram Desktop//Harkirat Course 0 t o100//[Ad-hoc] 1. Next.js.mp4",
  { highWaterMark: 1 * 1024 * 1024 }
);

let writeStream = fs.createWriteStream("video-mp4", {
  highWaterMark: 1 * 1024 * 1024,
});

// Start piping
readStream.pipe(writeStream);
console.log("Piping Started...");

// Stop after 1 second
setTimeout(() => {
  readStream.unpipe(writeStream);
  console.log("Piping stopped using unpipe()");
}, 1000);

writeStream.on("finish", () => {
  console.log("Finished");
});

writeStream.on("close", () => {
  console.log("Closed");
});
```

✅ **Effect:**
* Only part of the data is written.
* Useful when **canceling a transfer** or **stream interruption**.

## 4. Important Notes

* `pipe()` **simplifies backpressure handling** automatically.
* `unpipe()` lets you stop data flow manually.
* However, **error handling is not built-in with **`pipe()`.
   * You should always listen for `error` events on both readable and writable streams.

## 5. Use Cases of `pipe()`

* File copy (large files).
* Streaming video/audio.
* Sending HTTP responses (`res` is a writable stream in Node.js).
* Data transformation pipelines (e.g., `gzip`, compression).

👉 With this, you now understand how to:
* Manually handle streams.
* Use `pipe()` for automatic backpressure handling.
* Stop the flow using `unpipe()`.