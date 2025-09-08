# 📄 Writing One Lakh Numbers in Node.js

When writing **large amounts of data** to a file in Node.js, the way you write makes a huge difference in **speed** and **memory usage**.

## ❌ Using `fs.appendFileSync()`

### Example Code

```javascript
import fs from "fs";

console.time("appendFile");

// Takes ~22 seconds
for (let i = 0; i <= 100000; i++) {
  fs.appendFileSync("numbers.txt", `${i}, `);
}

console.timeEnd("appendFile");
```

### Why is this slow?

Because `fs.appendFileSync()` is **synchronous** and does **one write per loop iteration** (100,001 times).

1. **Blocks the event loop**
   * Each call is blocking → Node.js stops everything until the write is done.
   * Doing this 100k+ times makes it very slow.

2. **Reopens the file each time**
   * `appendFileSync()` does: open file → move to end → write → close.
   * That's a heavy operation at OS level.

3. **Many tiny writes**
   * Disk writes are much slower than memory writes.
   * Doing 100k small writes is slower than batching them together.

## ✅ Using `fs.createWriteStream()`

### Example Code

```javascript
import fs from "fs";

console.time("writeStream");

const writeStream = fs.createWriteStream("streamedNumber.txt");

for (let i = 1; i <= 100000; i++) {
  writeStream.write(`${i}\n`); // chunk by chunk
}

writeStream.end();

writeStream.on("finish", () => {
  console.timeEnd("writeStream"); // ~370ms
});
```

### Why is this faster?

Because `WriteStream` writes data in a **continuous flow** instead of reopening the file every time.

1. **File opened only once**
   * `fs.createWriteStream()` opens the file once.
   * Keeps it open until `.end()` is called.

2. **Buffered writing**
   * Data is first collected in memory (buffer).
   * Flushed to disk in larger chunks.
   * Fewer disk writes = much faster.

3. **Asynchronous I/O**
   * Writing happens in the background.
   * The loop just queues data into memory.
   * Node.js event loop is not blocked.

## ⚡ Performance Comparison

| Method | Time Taken (approx) |
|--------|-------------------|
| `appendFileSync()` | ~22 seconds |
| `createWriteStream()` | ~370 ms |

## 💡 Key Takeaways

* Avoid writing to files **inside loops** with `appendFileSync()`.
* Use `fs.createWriteStream()` for large data → it's faster, non-blocking, and memory-efficient.
* Streams = best way to handle large amounts of data in Node.js.