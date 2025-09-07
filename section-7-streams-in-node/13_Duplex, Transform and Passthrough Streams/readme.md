# Duplex, Transform, and PassThrough Streams in Node.js

Streams in Node.js are not limited to just **readable** and **writable**. There are special types of streams that give you more power when handling data flow:

* **Duplex Streams**
* **Transform Streams**
* **PassThrough Streams**

## 1. Duplex Streams

A **Duplex stream** is a type of stream that is **both readable and writable**. The **key point**: Reading and writing are **independent operations**.

Example:
* `net.Socket` is a duplex stream.
* You can **read incoming data** from the socket.
* You can **write outgoing data** to the socket.
* Both use **separate internal buffers**.

```
           ┌───────────┐
Incoming → │  Read Buf │
           └───────────┘
                ↑
                │   Duplex Stream
                ↓
           ┌───────────┐
Outgoing ← │ Write Buf │
           └───────────┘
```

🔑 Key Points:
* Two separate buffers:
   * One for **reading** (incoming).
   * One for **writing** (outgoing).
* Read and write operations can happen **simultaneously**.

📌 **Examples**:
* `net.Socket`
* `tls.TLSSocket`
* `process.stdin` (in some modes)

## 2. Transform Streams

A **Transform stream** is a special type of **Duplex stream**. It can both **read** and **write**, but it **modifies data** as it passes through.

Coffee Machine Analogy ☕
1. Input (write): Coffee beans
2. Processing: Grinding + brewing
3. Output (read): Hot coffee

```
Input Data → [ Write Side ] 
                │
                ▼
         ┌──────────────┐
         │ Transform Fn │   (e.g., toUpperCase, gzip)
         └──────────────┘
                │
                ▼
Output Data ← [ Read Side ]
```

🔑 Key Points:
* Input is **transformed** before being output.
* Implemented using the `_transform()` method.

📌 **Examples of real-world usage**:
* Compression → `zlib.createGzip()`
* Encryption → `crypto.createCipheriv()`
* Data formatting → e.g., `.toUpperCase()`

Example Flow:
* Input: `"hello world"`
* Transform logic: `.toUpperCase()`
* Output: `"HELLO WORLD"`

👉 So, data is **modified on the fly** before being passed along.

## 3. PassThrough Streams

A **PassThrough stream** is basically a **Transform stream with no transformation**. It just passes the data along, unchanged.

```
Input Data → [ PassThrough ] → Output Data
```

🔑 Key Points:
* Output = Input (no modifications).

📌 **Useful for**:
* Debugging pipelines (observe the data without changing it).
* Measuring data flow (count chunks, log data sizes).
* Acting as a placeholder before adding real transformation logic.

Example Flow:
* Input: `"hello world"`
* PassThrough → no transformation
* Output: `"hello world"`

## 💡 Quick Comparison

| Stream Type | Can Read | Can Write | Data Transformed? | Example Use Case |
|-------------|----------|-----------|-------------------|------------------|
| **Duplex** | ✅ Yes | ✅ Yes | ❌ No (independent) | Network socket (read/write independently) |
| **Transform** | ✅ Yes | ✅ Yes | ✅ Yes | Compression, encryption, formatting |
| **PassThrough** | ✅ Yes | ✅ Yes | ❌ No (output = input) | Debugging, logging, measuring |

👉 In short:
* **Duplex** = Two-way communication (independent).
* **Transform** = Two-way communication with modification.
* **PassThrough** = Transform with zero changes.