# DataView and Typed Arrays — Reading & Writing in ArrayBuffers

## Table of Contents
- [Why We Need Views](#why-we-need-views)
- [Understanding DataView](#understanding-dataview)
- [Creating a DataView](#creating-a-dataview)
- [Writing Data with DataView](#writing-data-with-dataview)
- [Reading Data with DataView](#reading-data-with-dataview)
- [Multiple Views on the Same Buffer](#multiple-views-on-the-same-buffer)
- [Typed Arrays (Quick Introduction)](#typed-arrays-quick-introduction)
- [Summary](#summary)
- [Code Examples](#code-examples)
- [What's Next](#whats-next)

## Why We Need Views

In the previous topic, we learned that an **ArrayBuffer** is just a *box of raw binary data* — and we can't directly read or write into it. To work with the data inside, we need something called a **view**.

### The Analogy
An **ArrayBuffer** is like a sealed box 📦, and a **view** is the *lens* 🔍 through which we can see and modify the data.

### Two Main Types of Views:

1. **🔢 Typed Arrays** (like `Uint8Array`, `Int16Array`, `Float32Array`, etc.)
   - For fixed-format binary data
   - Simpler to use but less flexible

2. **🛠️ DataView**
   - For flexible, byte-level access
   - Full control over endianness
   - More complex but more powerful

## Understanding DataView

`DataView` gives you **full control** over how you read/write data:

- ✅ Choose **data type** (Int8, Uint16, Float32, etc.)
- ✅ Specify **byte offsets** 
- ✅ Define **endianness** (little or big endian)
- ✅ Mix different data types in the same buffer

## Creating a DataView

### Basic Example

```javascript
// Step 1: Create a buffer of 4 bytes
const buffer = new ArrayBuffer(4);

// Step 2: Create a DataView for that buffer
const view = new DataView(buffer);

// Step 3: Write into the buffer
view.setInt8(0, 48);  // write 48 at byte 0
view.setInt8(1, 65);  // write 65 at byte 1

console.log(buffer);
// ArrayBuffer { [Uint8Contents]: <30 41 00 00>, byteLength: 4 }
```

### Constructor Options

```javascript
const buffer = new ArrayBuffer(16);

// View the entire buffer
const fullView = new DataView(buffer);

// View only bytes 4-12 (8 bytes starting at offset 4)
const partialView = new DataView(buffer, 4, 8);
```

## Writing Data with DataView

### Available Write Methods

| Method | Description | Bytes Used |
|--------|-------------|------------|
| `setInt8(offset, value)` | Signed 8-bit integer | 1 |
| `setUint8(offset, value)` | Unsigned 8-bit integer | 1 |
| `setInt16(offset, value, littleEndian?)` | Signed 16-bit integer | 2 |
| `setUint16(offset, value, littleEndian?)` | Unsigned 16-bit integer | 2 |
| `setInt32(offset, value, littleEndian?)` | Signed 32-bit integer | 4 |
| `setUint32(offset, value, littleEndian?)` | Unsigned 32-bit integer | 4 |
| `setFloat32(offset, value, littleEndian?)` | 32-bit floating point | 4 |
| `setFloat64(offset, value, littleEndian?)` | 64-bit floating point | 8 |

### Writing with Different Number Formats

```javascript
const buffer = new ArrayBuffer(4);
const view = new DataView(buffer);

// All of these represent the same number: 80
view.setInt8(0, 80);        // Decimal
view.setInt8(1, 0b1010000); // Binary
view.setInt8(2, 0x50);      // Hexadecimal
view.setInt8(3, 0o120);     // Octal

console.log(view.getInt8(0)); // 80
console.log(view.getInt8(1)); // 80
console.log(view.getInt8(2)); // 80
console.log(view.getInt8(3)); // 80
```

💡 **Key Point**: All these values represent the same number **80**, just written in different formats.

## Reading Data with DataView

### Available Read Methods

| Method | Description | Returns |
|--------|-------------|---------|
| `getInt8(offset)` | Read signed 8-bit integer | Number |
| `getUint8(offset)` | Read unsigned 8-bit integer | Number |
| `getInt16(offset, littleEndian?)` | Read signed 16-bit integer | Number |
| `getUint16(offset, littleEndian?)` | Read unsigned 16-bit integer | Number |
| `getInt32(offset, littleEndian?)` | Read signed 32-bit integer | Number |
| `getUint32(offset, littleEndian?)` | Read unsigned 32-bit integer | Number |
| `getFloat32(offset, littleEndian?)` | Read 32-bit floating point | Number |
| `getFloat64(offset, littleEndian?)` | Read 64-bit floating point | Number |

### Reading Example

```javascript
const buffer = new ArrayBuffer(8);
const view = new DataView(buffer);

// Write some data
view.setInt16(0, 1000);    // 2 bytes
view.setFloat32(2, 3.14);  // 4 bytes
view.setUint8(6, 255);     // 1 byte

// Read it back
console.log(view.getInt16(0));    // 1000
console.log(view.getFloat32(2));  // 3.140000104904175
console.log(view.getUint8(6));    // 255
```

📌 **Important**: The numbers returned are in decimal, but in memory they're stored as binary bytes.

## Multiple Views on the Same Buffer

You can have **multiple views** (DataViews or Typed Arrays) for the **same** buffer. Changing data through one view affects the others because they share the same memory.

```javascript
const buffer = new ArrayBuffer(4);

const viewA = new DataView(buffer);
const viewB = new DataView(buffer);

// Write through viewA
viewA.setInt8(0, 30);
viewA.setInt8(1, 40);

// Read through viewB - sees the same data!
console.log(viewB.getInt8(0)); // 30
console.log(viewB.getInt8(1)); // 40

// You can even mix DataView with Typed Arrays
const uint8View = new Uint8Array(buffer);
console.log(uint8View[0]); // 30
console.log(uint8View[1]); // 40
```

## Typed Arrays (Quick Introduction)

**Typed Arrays** are simpler than `DataView` but less flexible:

- They directly map binary data to a specific type
- Work like regular JavaScript arrays
- All elements must be the same type

### Common Typed Array Types

| Type | Description | Bytes per Element | Range |
|------|-------------|-------------------|-------|
| `Int8Array` | Signed 8-bit integers | 1 | -128 to 127 |
| `Uint8Array` | Unsigned 8-bit integers | 1 | 0 to 255 |
| `Int16Array` | Signed 16-bit integers | 2 | -32,768 to 32,767 |
| `Uint16Array` | Unsigned 16-bit integers | 2 | 0 to 65,535 |
| `Int32Array` | Signed 32-bit integers | 4 | -2³¹ to 2³¹-1 |
| `Uint32Array` | Unsigned 32-bit integers | 4 | 0 to 2³²-1 |
| `Float32Array` | 32-bit floating point | 4 | ±1.2×10⁻³⁸ to ±3.4×10³⁸ |
| `Float64Array` | 64-bit floating point | 8 | ±5.0×10⁻³²⁴ to ±1.8×10³⁰⁸ |

### Basic Typed Array Example

```javascript
const buffer = new ArrayBuffer(4);
const uint8 = new Uint8Array(buffer);

// Set values using array syntax
uint8[0] = 255;
uint8[1] = 128;
uint8[2] = 64;
uint8[3] = 32;

console.log(uint8); 
// Uint8Array(4) [ 255, 128, 64, 32 ]

// You can iterate like a normal array
for (let i = 0; i < uint8.length; i++) {
    console.log(`Byte ${i}: ${uint8[i]}`);
}
```

## Summary

| Concept | Description | Use Case |
|---------|-------------|----------|
| **ArrayBuffer** | Raw storage container | Foundation for binary data |
| **Views** | Interface to access/modify data | Required to work with ArrayBuffer |
| **DataView** | Flexible, byte-level control | Mixed data types, endianness control |
| **Typed Arrays** | Fixed-format, array-like interface | Homogeneous data, simple operations |

### Key Takeaways:

- 📦 **ArrayBuffer** = raw storage (cannot be directly accessed)
- 🔍 **Views** = way to access and modify the data
- 🛠️ **DataView** = flexible, byte-level control with endianness
- 🔢 **Typed Arrays** = easier but fixed-format
- 💾 You can write values using decimal, binary, octal, or hex — but they're all stored as bytes
- 🔄 Multiple views can share the same buffer and see each other's changes

## Code Examples

### Complete Working Example

```javascript
// Create a buffer to store mixed data
const buffer = new ArrayBuffer(16);
const view = new DataView(buffer);

// Store different types of data
view.setUint8(0, 65);           // ASCII 'A'
view.setInt16(1, -1000);        // Signed integer
view.setFloat32(3, 3.14159);    // Pi
view.setUint32(7, 0xDEADBEEF);  // Hex value
view.setFloat64(11, 2.718);     // Euler's number (partial)

// Read the data back
console.log('Character:', String.fromCharCode(view.getUint8(0))); // 'A'
console.log('Integer:', view.getInt16(1));        // -1000
console.log('Float:', view.getFloat32(3));        // 3.1415927410125732
console.log('Hex:', view.getUint32(7).toString(16)); // 'deadbeef'

// Show the raw bytes
const bytes = new Uint8Array(buffer);
console.log('Raw bytes:', Array.from(bytes).map(b => b.toString(16).padStart(2, '0')).join(' '));


**Ready to master binary data manipulation?** Let's dive deeper into advanced ArrayBuffer techniques! 🚀