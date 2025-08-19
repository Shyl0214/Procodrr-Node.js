# Internal Buffer of Readable Streams in Node.js

When working with **Readable Streams** in Node.js, one of the most important concepts to understand is the **internal buffer**. Streams don't simply pass data directly from the source to the consumer; instead, they rely on an internal buffer to manage data flow efficiently.

## 🔹 What is the Internal Buffer?

- The **internal buffer** is a temporary storage area used by a `Readable` stream to hold chunks of data that have been fetched from the underlying resource but not yet consumed by the application.
- It helps balance the **speed mismatch** between the data producer (source) and the data consumer (your code).

Think of it like a **queue**:
- Data enters the queue when the source pushes it.
- Data leaves the queue when your application reads it.

## 🔹 Why Do We Need an Internal Buffer?

Without a buffer, one of two problems can occur:

1. If the **producer is faster** than the consumer → Data could be lost.
2. If the **consumer is faster** than the producer → The consumer would stall waiting for new data.

The internal buffer ensures a smooth flow of data, allowing **backpressure handling** and controlled consumption.

## 🔹 Buffer Size and HighWaterMark

- The maximum size of the internal buffer is controlled by the `highWaterMark` option when creating a stream.
- For example:

```javascript
const fs = require('fs');

// Read stream with a buffer size of 16 KB (default for binary streams)
const readStream = fs.createReadStream('example.txt', { highWaterMark: 16 * 1024 });
```

### Defaults:
- **16 KB** for binary streams (Buffer).
- **16** (characters) for objectMode streams.

If the buffer size is exceeded, Node.js will **pause reading** from the source until space becomes available.

## 🔹 States of the Internal Buffer

The buffer state affects the behavior of the stream. It can be:

1. **Empty** → No data available to read.
2. **Partially Filled** → Some data available, waiting to be consumed.
3. **Full (≥ highWaterMark)** → Stream temporarily stops pulling data until consumer processes it.

## 🔹 Accessing the Internal Buffer

Node.js exposes the internal buffer via:
- `readable.readableLength` → Shows how much data (in bytes or objects) is currently stored.
- `readable.readableHighWaterMark` → Shows the configured maximum buffer size.

### Example:

```javascript
const fs = require('fs');
const stream = fs.createReadStream('example.txt', { highWaterMark: 10 });

stream.on('data', (chunk) => {
  console.log(`Chunk: ${chunk}`);
  console.log(`Buffered Length: ${stream.readableLength}`);
  console.log(`HighWaterMark: ${stream.readableHighWaterMark}`);
});
```

## 🔹 How Data Flows Through the Internal Buffer

1. The source pushes data into the buffer.
2. If buffer < `highWaterMark`, it continues fetching more data.
3. If buffer ≥ `highWaterMark`, the stream signals **backpressure** by stopping data reads.
4. The consumer reads data (via `.on('data')`, `.read()`, or async iteration).
5. Once space frees up, the producer resumes pushing data.

## 🔹 Example: Observing Backpressure

```javascript
const { Readable } = require('stream');

const customStream = new Readable({
  read(size) {
    // Push some data continuously
    this.push('x'.repeat(size));
    this.push(null); // end
  }
});

customStream.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes`);
  console.log(`Buffered length: ${customStream.readableLength}`);
});
```

Here you can see how much data is buffered internally before being consumed.

## 🔹 Key Points to Remember

- The **internal buffer** is crucial for efficient stream handling.
- Controlled by `highWaterMark`.
- `readableLength` shows how much is currently stored.
- Backpressure prevents overloading consumers.
- Helps smooth data flow between **fast producers** and **slow consumers**.

## ✅ Summary

**In short:** The internal buffer acts like a shock absorber in streams—it ensures that no matter how fast or slow either side is, data flow stays efficient and manageable.

## Additional Resources

- [Node.js Streams Documentation](https://nodejs.org/api/stream.html)
- [Understanding Backpressure in Node.js Streams](https://nodejs.org/en/docs/guides/backpressuring-in-streams/)
- [Stream API Reference](https://nodejs.org/api/stream.html#stream_readable_streams)
