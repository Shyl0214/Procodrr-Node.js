# Writable Streams in Node.js

A **writable stream** in Node.js is used to write data to a specific destination in chunks. Instead of loading the entire data into memory at once, streams let you process data piece by piece, making them efficient for large files.

## 📌 Key Points About Writable Streams

1. **Chunk-based writing** → Data is written in smaller pieces (chunks) instead of all at once.
2. **HighWaterMark** →
   * Default buffer size for **writable streams** is **16 KB**.
   * Default buffer size for **readable streams** is **64 KB**.
3. **File creation** → If the file doesn't exist, a writable stream will **create the file automatically**.
4. **Overwrite behavior** → If the file already exists, it will **overwrite the existing content** with the new data.

## 📖 Example: Copying a Video Using Streams

```javascript
import fs from "node:fs";

// Create a readable stream from video.mp4
let readStream = fs.createReadStream("video.mp4", {
  highWaterMark: 1 * 1024 * 1024, // read in 1MB chunks
});

// Create a writable stream for video-2.mp4
let writeStream = fs.createWriteStream("video-2.mp4");

// Read data in chunks and write it to new file
readStream.on("data", (chunk) => {
  writeStream.write(chunk);
});
```

## 🔎 What happens here?

* The **read stream** takes chunks of `video.mp4` (1MB each because of `highWaterMark`).
* Each chunk is immediately **written** to `video-2.mp4` via the **writable stream**.
* If `video-2.mp4` doesn't exist, Node.js will create it. If it does exist, its contents will be replaced.

## ✅ Output

After running the program, you'll see that a new file `video-2.mp4` is created in your project directory. This new file is an exact copy of the original `video.mp4`.

## ⚡ Summary

**Writable streams** in Node.js allow efficient writing of large amounts of data without consuming too much memory. They are commonly used in tasks like **file writing, HTTP responses, and piping data between streams**.