# 🚀 Types of Streams in Node.js

Node.js provides **four main types of streams** that help in handling data efficiently.

## 1️⃣ **Readable Streams**
* Used to **read data from a source**, piece by piece.
* Data flows **into** your program.

📘 **Examples**:
* Reading a file using `fs.createReadStream()`
* Receiving data from an HTTP request

✅ **Real-world Analogy**: Like reading a **book page by page** instead of reading the whole book at once.

## 2️⃣ **Writable Streams**
* Used to **write data to a destination**, piece by piece.
* Data flows **out of** your program.

📘 **Examples**:
* Writing data to a file
* Sending a response back to the client in HTTP

✅ **Real-world Analogy**: Like writing an **essay line by line** instead of finishing it in a single go.

## 3️⃣ **Duplex Streams**
* Streams that can **both read and write** data.
* Data flows **in and out** simultaneously.

📘 **Examples**:
* Network sockets (e.g., TCP connections)

✅ **Real-world Analogy**: Like a **walkie-talkie** — you can **send and receive** messages at the same time.

## 4️⃣ **Transform Streams**
* A special type of duplex stream that can **modify or transform** the data as it passes through.

📘 **Examples**:
* Compressing a file (`zlib.createGzip()`)
* Encrypting/decrypting data
* Converting text to uppercase

✅ **Real-world Analogy**: Like a **machine on an assembly line** that **modifies each product** as it moves through.

## 📌 Quick Recap
* **Readable** → Get data
* **Writable** → Send data
* **Duplex** → Both read & write
* **Transform** → Read + Write + Modify

⚡ With this foundation, you're ready to explore **how to use these streams in real code** (like reading/writing files or handling network requests).