# EventEmitter in Node.js

🔹 **1. What is EventEmitter?**

In Node.js, **EventEmitter** is a **core module** that provides a mechanism to implement the **event-driven architecture**.

It allows objects to:
* **Emit events** (signal that something happened).
* **Listen (subscribe)** to events and react when they occur.

👉 It's part of the built-in `events` module in Node.js.

🔹 **2. Importing EventEmitter**

```javascript
const EventEmitter = require("events");
const emitter = new EventEmitter();
```

Now, `emitter` can:
* **emit events** → `emitter.emit("eventName", data)`
* **listen to events** → `emitter.on("eventName", callback)`

🔹 **3. Basic Example**

```javascript
const EventEmitter = require("events");
const emitter = new EventEmitter();

// Listener (Consumer)
emitter.on("greet", (name) => {
    console.log(`Hello, ${name}!`);
});

// Emit event (Producer)
emitter.emit("greet", "Shailesh");
```

👉 Output:

```
Hello, Shailesh!
```

🔹 **4. Multiple Listeners for Same Event**

```javascript
emitter.on("orderPlaced", (id) => {
    console.log(`Order confirmed: ${id}`);
});

emitter.on("orderPlaced", (id) => {
    console.log(`Sending email for order: ${id}`);
});

emitter.emit("orderPlaced", 101);
```

👉 Output:

```
Order confirmed: 101
Sending email for order: 101
```

✅ One event can trigger **multiple listeners**.

🔹 **5. One-Time Listener**

Sometimes, you want an event listener to run **only once**.

```javascript
emitter.once("login", (user) => {
    console.log(`${user} logged in (first time only)!`);
});

emitter.emit("login", "Alice");
emitter.emit("login", "Alice");
```

👉 Output:

```
Alice logged in (first time only)!
```

✅ The listener is removed automatically after first execution.

🔹 **6. Removing Listeners**

```javascript
function notify() {
    console.log("Notification sent!");
}

emitter.on("notify", notify);

// Remove listener
emitter.removeListener("notify", notify);
// OR emitter.off("notify", notify);

emitter.emit("notify"); // Nothing happens
```

🔹 **7. Error Handling in EventEmitter**

By default, if an `error` event is emitted **without a listener**, Node.js will crash.

```javascript
emitter.on("error", (err) => {
    console.error("Error handled:", err.message);
});

emitter.emit("error", new Error("Something went wrong!"));
```

👉 Output:

```
Error handled: Something went wrong!
```

✅ Always attach an `error` listener in production apps.

🔹 **8. Real-World Example: File Upload System**

```javascript
const EventEmitter = require("events");
const uploader = new EventEmitter();

// Listeners
uploader.on("start", (file) => console.log(`Starting upload: ${file}`));
uploader.on("progress", (percent) => console.log(`Progress: ${percent}%`));
uploader.on("done", (file) => console.log(`Upload complete: ${file}`));

// Emit events
uploader.emit("start", "photo.png");
uploader.emit("progress", 50);
uploader.emit("progress", 100);
uploader.emit("done", "photo.png");
```

👉 Output:

```
Starting upload: photo.png
Progress: 50%
Progress: 100%
Upload complete: photo.png
```

🔹 **9. Key Methods in EventEmitter**

| Method | Description |
|--------|-------------|
| `.on(event, listener)` | Add a listener (multiple allowed). |
| `.once(event, listener)` | Add a listener that runs only once. |
| `.emit(event, [args])` | Trigger an event. |
| `.removeListener(event, listener)` | Remove a specific listener. |
| `.removeAllListeners(event)` | Remove all listeners for an event. |
| `.listenerCount(event)` | Count number of listeners for an event. |

🔹 **10. When to Use EventEmitter?**

* Handling **async events** (file uploads, DB updates, network requests).
* **Decoupling components** (producers don't need to know consumers).
* Building **custom event-driven systems** in Node.js.

✅ **Conclusion:** The **EventEmitter** is the backbone of Node.js's **event-driven model**. It allows building flexible, loosely coupled applications where different parts of the system can **react to events asynchronously**.