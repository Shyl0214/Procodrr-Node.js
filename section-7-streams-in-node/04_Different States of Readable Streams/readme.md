# 🔹 States of Readable Streams in Node.js

A **Readable Stream** in Node.js can be in **different states** depending on whether data is being consumed or not.

## 🏷️ Stream States

### 1. `Readable (Paused Mode)`
* This is the **default state**.
* Data is **not flowing automatically**.
* You must explicitly call `.read()` or attach a `data` event listener to consume data.

📘 Example:

```javascript
import fs from "fs";

const readStream = fs.createReadStream("example.txt");

// Stream is in paused mode
console.log("Stream is paused?", readStream.isPaused()); // true
```

### 2. `Flowing Mode`
* In this state, the stream **automatically reads data** from the source and emits `data` events.
* Once a listener is attached to the `data` event → the stream switches to **flowing mode**.
* Data keeps flowing until you pause it.

📘 Example:

```javascript
const readStream = fs.createReadStream("example.txt");

// Switch to flowing mode when 'data' event is attached
readStream.on("data", (chunk) => {
  console.log("Received chunk:", chunk.toString());
});
```

### 3. `Paused State (Manually)`
* You can manually **pause** a stream using `.pause()`.
* After pausing, data stops flowing until you call `.resume()`.

📘 Example:

```javascript
const readStream = fs.createReadStream("example.txt");

readStream.on("data", (chunk) => {
  console.log("Chunk:", chunk.toString());
  readStream.pause();   // manually pause
  console.log("Stream paused...");

  setTimeout(() => {
    console.log("Resuming stream...");
    readStream.resume();  // resume after 2s
  }, 2000);
});
```

### 4. `Ended / Closed`
* After all data has been read, the stream emits an `end` event.
* At this point, no more data can be consumed.
* The stream may also emit a `close` event, signaling that resources (like file descriptors) are released.

📘 Example:

```javascript
const readStream = fs.createReadStream("example.txt");

readStream.on("end", () => {
  console.log("No more data to read.");
});

readStream.on("close", () => {
  console.log("Stream closed.");
});
```

## 🔁 Summary of Readable Stream States

| State | Description | How to Enter This State |
|-------|-------------|------------------------|
| **Paused** | Default state, data not flowing | No `data` event attached, or `.pause()` called |
| **Flowing** | Data flows automatically and emits `data` events | Attach `data` listener, call `.resume()` |
| **Paused** | Temporarily paused during flowing | Call `.pause()` manually |
| **Ended** | No more data left to read | Stream consumed all data (`end` event fired) |
| **Closed** | Underlying resource released (file/network closed) | After `end` or explicit `.destroy()` call |

✅ **In simple terms**:
* Streams start in **paused mode**.
* When you listen for `data`, they go into **flowing mode**.
* You can **pause** and **resume** as needed.
* Once finished, they emit `end` and may **close**.