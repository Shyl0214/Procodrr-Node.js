# Piping Using `pipeline()` in Node.js

In the previous section, we learned about the `pipe()` method, which simplifies data transfer between streams and handles **backpressure automatically**.

However, one major drawback of `pipe()` is that it does **not provide built-in error handling**. This is where the `pipeline()` function comes in.

## 1. Why Use `pipeline()`?

* `pipe()` makes data transfer easy but lacks **error handling**.
* With `pipeline()`, you get: 
  ✅ Automatic piping between streams. 
  ✅ Built-in **error handling** via a callback. 
  ✅ Proper cleanup when errors occur.

## 2. Syntax

```javascript
import { pipeline } from "node:stream";

pipeline(
  readableStream, 
  writableStream, 
  (err) => {
    if (err) {
      console.error("Pipeline failed.", err);
    } else {
      console.log("Pipeline succeeded.");
    }
  }
);
```

## 3. Example: Handling Errors with `pipeline()`

```javascript
import fs from "node:fs";
import { pipeline } from "node:stream";

let readStream = fs.createReadStream(
  "C://Users//shail//Downloads//Telegram Desktop//Harkirat Course 0 t o100//[Ad-hoc] 1. Next.js.mp4",
  { highWaterMark: 1 * 1024 * 1024 }
);

let writeStream = fs.createWriteStream("video-mp4", {
  highWaterMark: 1 * 1024 * 1024,
});

pipeline(readStream, writeStream, (err) => {
  console.log("Pipeline callback:", err);
});

// Trigger an error after 1 second
setTimeout(() => {
  readStream.destroy("Custom Error Triggered");
}, 1000);
```

Output:

```
Pipeline callback: Custom Error Triggered
```

✅ `pipeline()` catches the error and reports it in the callback.
✅ No need to attach separate `error` event listeners manually.

## 4. Example with Ongoing Process

You can continue other processes while the pipeline runs.

```javascript
import fs from "node:fs";
import { pipeline } from "node:stream";

let readStream = fs.createReadStream(
  "C://Users//shail//Downloads//Telegram Desktop//Harkirat Course 0 t o100//[Ad-hoc] 1. Next.js.mp4",
  { highWaterMark: 1 * 1024 * 1024 }
);

let writeStream = fs.createWriteStream("video-mp4");

pipeline(readStream, writeStream, (err) => {
  console.log("Pipeline callback:", err);
});

// Force error
setTimeout(() => {
  readStream.destroy("Error occurred during streaming");
}, 1000);

// Meanwhile, keep running another task
setInterval(() => {
  console.log("App is still alive...");
}, 1000);
```

Output:

```
Pipeline callback: Error occurred during streaming
App is still alive...
App is still alive...
...
```

✅ Even after pipeline failure, the rest of your application keeps running.

## 5. Key Takeaways

* Use `pipe()` for **simple piping** when error handling is not a concern.
* Use `pipeline()` when you need:
   * **Error handling**
   * **Automatic cleanup** of streams
   * A more **robust, production-ready** solution

## 6. Common Use Cases of `pipeline()`

* File operations (copying, streaming, backups).
* Network requests (piping HTTP responses).
* Data transformation pipelines (compression, encryption, etc.).
* Any scenario where **errors could break the stream flow**.

👉 With `pipeline()`, you get the best of both worlds: **simplicity of pipe + safety of error handling**.