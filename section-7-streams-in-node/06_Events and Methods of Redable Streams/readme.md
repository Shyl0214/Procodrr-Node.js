# Events and Methods of Readable Streams in Node.js

Readable streams in Node.js provide a powerful way to handle data that is read chunk-by-chunk from a source (like files, HTTP requests, or sockets). To effectively use them, you must understand the events they emit and the methods they expose.

## 🔹 Events of Readable Streams

Readable streams are event emitters. These events notify you about different stages of the stream's lifecycle.

### 1. `data` Event

- Triggered when a chunk of data is available to read.
- Automatically switches the stream into flowing mode.

```javascript
const fs = require('fs');
const stream = fs.createReadStream('example.txt');

stream.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes:`, chunk.toString());
});
```

### 2. `end` Event

- Emitted when no more data is available to read (i.e., stream reaches EOF).

```javascript
stream.on('end', () => {
  console.log('No more data.');
});
```

### 3. `error` Event

- Emitted if an error occurs during reading (e.g., file not found).
- **Always handle this event** to avoid crashes.

```javascript
stream.on('error', (err) => {
  console.error('Error:', err.message);
});
```

### 4. `close` Event

- Fired when the stream and its underlying resource (like file descriptor) are closed.
- Indicates that cleanup has happened.

```javascript
stream.on('close', () => {
  console.log('Stream closed.');
});
```

### 5. `readable` Event

- Emitted when data is available to read from the internal buffer, but not automatically consumed.
- Lets you use the `.read()` method for manual control.

```javascript
stream.on('readable', () => {
  let chunk;
  while ((chunk = stream.read()) !== null) {
    console.log(`Read ${chunk.length} bytes`);
  }
});
```

### ✅ Summary of Events

| Event | When it Happens | Use Case |
|-------|----------------|----------|
| `data` | A new chunk is available | Auto-consume chunks in flowing mode |
| `end` | No more data left in the stream | Clean up after reading |
| `error` | An error occurred while reading | Handle file/network errors |
| `close` | Stream closed and resource released | Confirm resource cleanup |
| `readable` | Data available in buffer, needs manual `.read()` call | Fine-grained reading control |

## 🔹 Methods of Readable Streams

Node.js provides several methods on Readable streams to manage how data is consumed.

### 1. `read([size])`

- Pulls data from the internal buffer.
- Optional `size` specifies the number of bytes/objects to read.

```javascript
const chunk = stream.read(10); // read 10 bytes
console.log(chunk);
```

### 2. `pipe(destination)`

- Pipes data directly to a writable stream.
- Handles backpressure automatically.

```javascript
const fs = require('fs');
const read = fs.createReadStream('input.txt');
const write = fs.createWriteStream('output.txt');

read.pipe(write); // Copies data
```

### 3. `unpipe([destination])`

- Stops piping to the given destination stream.

```javascript
read.unpipe(write);
```

### 4. `pause()`

- Pauses the flow of data events (useful for applying backpressure manually).

```javascript
stream.on('data', (chunk) => {
  console.log('Received:', chunk.toString());
  stream.pause(); // pause temporarily

  setTimeout(() => stream.resume(), 1000); // resume after 1s
});
```

### 5. `resume()`

- Resumes emitting data events after a `pause()`.

### 6. `isPaused()`

- Returns `true` if the stream is currently paused.

```javascript
console.log(stream.isPaused()); // true or false
```

### 7. `destroy([error])`

- Destroys the stream and optionally emits an error.
- Releases resources immediately.

```javascript
stream.destroy(new Error('Stream destroyed manually'));
```

### ✅ Summary of Methods

| Method | Purpose |
|--------|---------|
| `read([size])` | Read specific data from buffer |
| `pipe()` | Connects to a writable stream |
| `unpipe()` | Stops piping to writable stream |
| `pause()` | Pause data flow |
| `resume()` | Resume data flow |
| `isPaused()` | Check pause state |
| `destroy()` | Destroy stream & free resources |

## 🔹 Putting it All Together (Example)

Here's a comprehensive example demonstrating how to use events and methods together:

```javascript
const fs = require('fs');
const stream = fs.createReadStream('example.txt', { highWaterMark: 16 });

stream.on('readable', () => {
  let chunk;
  while ((chunk = stream.read()) !== null) {
    console.log(`Read chunk: ${chunk.toString()}`);
  }
});

stream.on('end', () => {
  console.log('Finished reading file');
});

stream.on('error', (err) => {
  console.error('Error:', err.message);
});
```

## 🎯 Key Takeaways

- **Events** (`data`, `end`, `error`, `close`, `readable`) let you react to the stream lifecycle.
- **Methods** (`read`, `pipe`, `pause`, `resume`, etc.) give you control over how data flows.
- Use `pipe()` for most cases → it simplifies handling backpressure.
- Use `readable` + `read()` if you want fine-grained manual control.

## Stream Modes

### Flowing Mode
- Data is read automatically and emitted via `data` events
- Triggered by: listening to `data` event, calling `pipe()`, or calling `resume()`

### Paused Mode (Non-flowing)
- Data must be explicitly read using `read()` method
- Default mode when stream is created
- Triggered by: listening to `readable` event or calling `pause()`

## Best Practices

1. **Always handle the `error` event** to prevent crashes
2. **Use `pipe()` for simple data transfer** - it handles backpressure automatically
3. **Use `readable` + `read()` for fine-grained control** over data consumption
4. **Don't mix flowing and non-flowing modes** in the same stream
5. **Clean up resources** by listening to the `close` event when needed

## Additional Resources

- [Node.js Streams Documentation](https://nodejs.org/api/stream.html)
- [Stream API Reference](https://nodejs.org/api/stream.html#stream_readable_streams)
- [Understanding Stream Events](https://nodejs.org/api/stream.html#stream_events)

