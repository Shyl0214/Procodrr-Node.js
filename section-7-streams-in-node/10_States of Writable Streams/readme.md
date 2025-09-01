# 🔄 States of Writable Streams in Node.js

Writable streams go through different **states** during their lifecycle. These states help you understand whether you can still write data, whether the buffer is flushed, and whether the stream is fully finished.

## 1️⃣ Writable State (`writable`)

* Indicates **whether the stream is open for writing**.
* Returns `true` → You can still write data.
* Returns `false` → Stream has ended, you cannot write anymore.

```javascript
import fs from "node:fs";

let writeStream = fs.createWriteStream("file.txt");

writeStream.write("hello world");

console.log(writeStream.writable); // true ✅ still writable

writeStream.end();

console.log(writeStream.writable); // false ❌ can't write anymore
```

## 2️⃣ Corked State (`cork()` / `uncork()`)

* `cork()` → Temporarily holds all writes in the buffer (doesn't flush them immediately).
* `uncork()` → Flushes the buffered data to the underlying resource (disk).
* Use case: Batch multiple small writes together to improve performance.

```javascript
import fs from "node:fs";

let writeStream = fs.createWriteStream("file.txt");

writeStream.cork(); // Buffering begins

writeStream.write("1");
writeStream.write("2");
writeStream.write("3");
writeStream.write("4");

console.log(writeStream.writableCorked); // 1 (corked)
```

➡️ At this point, nothing is written to the file because it's still corked.

Now, let's **uncork**:

```javascript
writeStream.uncork();
console.log(writeStream.writableCorked); // 0 (uncorked)
```

➡️ Now all buffered writes (`1 2 3 4`) are flushed into the file.

## 3️⃣ Ended State (`writableEnded`)

* Indicates whether `.end()` has been called.
* **Important**: Ended ≠ Flushed. Data may still be in the buffer.

```javascript
let writeStream = fs.createWriteStream("file.txt");

writeStream.write("1");
writeStream.write("2");
writeStream.write("3");
writeStream.write("4");

writeStream.end();

console.log(writeStream.writableEnded); // true ✅ (end called)
```

➡️ Even though `writableEnded` is `true`, there might still be data waiting in the buffer to be written to disk.

## 4️⃣ Finished State (`writableFinished`)

* Means **all data has been flushed from the buffer** and the stream is fully done.
* Triggered after the `finish` event.

```javascript
let writeStream = fs.createWriteStream("file.txt");

writeStream.write("1");
writeStream.write("2");
writeStream.write("3");
writeStream.write("4");

writeStream.end();

console.log(writeStream.writableEnded); // true (end called)

writeStream.on("finish", () => {
  console.log("Finished event fired ✅");
});

setTimeout(() => {
  console.log(writeStream.writableFinished); // true (fully flushed)
}, 10);
```

➡️ We use `setTimeout` because flushing to disk takes a little time.

## 📝 Summary of Writable States

| State/Property | Meaning |
|---|---|
| `writable` | `true` if you can still write data; `false` after `.end()` |
| `writableCorked` | `>0` if corked; `0` if uncorked |
| `writableEnded` | `true` after `.end()` has been called (but buffer may still have data) |
| `writableFinished` | `true` once all data has been flushed and stream is fully done |

## ✅ Conclusion

With these states, you can always check **where your writable stream stands** in its lifecycle.