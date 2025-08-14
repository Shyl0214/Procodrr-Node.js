# Buffer Methods and Properties in Node.js

In Node.js, Buffer objects are used for working with binary data.
They come with a variety of properties and methods to read, write, transform, and compare data.

## 📌 Properties

### `buffer.length`
Returns the size of the buffer in bytes (fixed at creation).

```javascript
const buf = Buffer.alloc(10);
console.log(buf.length); // 10
```

### `buffer.byteLength`
Returns the actual byte length of the data in the buffer (useful when partially filled).

```javascript
const buf = Buffer.from('Hi');
console.log(buf.byteLength); // 2
```

## 🛠 Commonly Used Methods

### 1. `buffer.toString([encoding], [start], [end])`
Converts buffer data into a string using a specified encoding.
Default encoding is "utf8".

```javascript
const buf = Buffer.from('Hello');
console.log(buf.toString()); // "Hello"
```

### 2. `buffer.write(string, [offset], [length], [encoding])`
Writes a string into the buffer.

```javascript
const buf = Buffer.alloc(10);
buf.write('Hi');
console.log(buf.toString()); // "Hi"
```

### 3. `buffer.slice([start], [end])`
Creates a new Buffer view referencing the same memory.

```javascript
const buf = Buffer.from('Hello');
const part = buf.slice(0, 2);
console.log(part.toString()); // "He"
```

### 4. `buffer.copy(targetBuffer, [targetStart], [sourceStart], [sourceEnd])`
Copies data from one buffer to another.

```javascript
const src = Buffer.from('Hello');
const dest = Buffer.alloc(5);
src.copy(dest, 0, 0, 5);
console.log(dest.toString()); // "Hello"
```

### 5. `buffer.equals(otherBuffer)`
Compares two buffers.

```javascript
const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('ABC');
console.log(buf1.equals(buf2)); // true
```

### 6. `buffer.fill(value, [offset], [end], [encoding])`
Fills buffer with a value.

```javascript
const buf = Buffer.alloc(5);
buf.fill('A');
console.log(buf.toString()); // "AAAAA"
```

### 7. `buffer.readUInt8(offset)` / `buffer.writeUInt8(value, offset)`
Reads/Writes an unsigned 8-bit integer.

```javascript
const buf = Buffer.alloc(1);
buf.writeUInt8(255, 0);
console.log(buf.readUInt8(0)); // 255
```

### 8. `buffer.readInt16LE(offset)` / `buffer.writeInt16LE(value, offset)`
Reads/Writes a signed 16-bit integer in little-endian format.

```javascript
const buf = Buffer.alloc(2);
buf.writeInt16LE(-123, 0);
console.log(buf.readInt16LE(0)); // -123
```

### 9. `buffer.subarray([start], [end])`
Returns a view (like slice) without copying data.

```javascript
const buf = Buffer.from('Hello');
const sub = buf.subarray(1, 4);
console.log(sub.toString()); // "ell"
```

### 10. `buffer.includes(value, [byteOffset], [encoding])`
Checks if buffer contains a value.

```javascript
const buf = Buffer.from('Hello');
console.log(buf.includes('ell')); // true
```

## 📜 Summary Table

| Property / Method                           | Purpose                               |
|---------------------------------------------|---------------------------------------|
| `length`                                    | Size in bytes                         |
| `byteLength`                                | Actual byte length of data            |
| `toString()`                                | Convert to string                     |
| `write()`                                   | Write string into buffer              |
| `slice()`                                   | Get part of buffer (same memory)      |
| `copy()`                                    | Copy data to another buffer           |
| `equals()`                                  | Compare buffers                       |
| `fill()`                                    | Fill with a value                     |
| `readUInt8()` / `writeUInt8()`              | Read/Write unsigned 8-bit             |
| `readInt16LE()` / `writeInt16LE()`          | Read/Write signed 16-bit LE           |
| `subarray()`                                | View without copying                  |
| `includes()`                                | Search in buffer                      |
