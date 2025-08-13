# 📂 Node.js File Operations — Rename, Delete, Move, Copy & More

In Node.js, all file operations are handled by the `fs` (File System) module. You can use `fs` in two ways:

- **Call-back API** → `fs`
- **Promise API** → `fs/promises` *(modern and preferred)*

## 1️⃣ Renaming a File

### Using Promises (Recommended)

```javascript
import fs from "node:fs/promises";

await fs.rename("oldName.txt", "newName.txt");
console.log("File renamed successfully!");
```

💡 **Note:**
- If the file doesn't exist, you'll get an error.
- `rename()` can also **move a file** to a different folder.

## 2️⃣ Deleting a File

### Using Promises

```javascript
import fs from "node:fs/promises";

await fs.unlink("fileToDelete.txt");
console.log("File deleted successfully!");
```

💡 If the file doesn't exist, Node.js will throw an error.

## 3️⃣ Moving a File

Moving is basically **renaming** with a different path.

```javascript
import fs from "node:fs/promises";

await fs.rename("file.txt", "newFolder/file.txt");
console.log("File moved successfully!");
```

💡 This works even across folders (within the same drive).

## 4️⃣ Copying a File

```javascript
import fs from "node:fs/promises";

await fs.copyFile("source.txt", "destination.txt");
console.log("File copied successfully!");
```

💡 You can also use flags like `fs.constants.COPYFILE_EXCL` to **prevent overwriting**.

### Advanced Copy Options

```javascript
import fs from "node:fs/promises";

// Prevent overwriting if destination exists
await fs.copyFile("source.txt", "dest.txt", fs.constants.COPYFILE_EXCL);

// Copy and preserve file mode
await fs.copyFile("source.txt", "dest.txt", fs.constants.COPYFILE_FICLONE);
```

## 5️⃣ Creating a File

```javascript
import fs from "node:fs/promises";

await fs.writeFile("newFile.txt", "Hello, World!");
console.log("File created successfully!");
```

💡 If the file exists, `writeFile()` will overwrite it.

## 6️⃣ Appending to a File

```javascript
import fs from "node:fs/promises";

await fs.appendFile("log.txt", "New log entry\n");
console.log("Data appended successfully!");
```

## 7️⃣ Checking if a File Exists

```javascript
import fs from "node:fs/promises";

try {
    await fs.access("file.txt");
    console.log("File exists!");
} catch {
    console.log("File does not exist.");
}
```

### Advanced File Checks

```javascript
import fs from "node:fs/promises";

try {
    // Check if file is readable
    await fs.access("file.txt", fs.constants.R_OK);
    console.log("File is readable");
    
    // Check if file is writable
    await fs.access("file.txt", fs.constants.W_OK);
    console.log("File is writable");
    
    // Check if file is executable
    await fs.access("file.txt", fs.constants.X_OK);
    console.log("File is executable");
} catch (error) {
    console.log("Access check failed:", error.message);
}
```

## 8️⃣ Reading a Directory

```javascript
import fs from "node:fs/promises";

const files = await fs.readdir("./");
console.log("Files in directory:", files);
```

### Advanced Directory Reading

```javascript
import fs from "node:fs/promises";

// Get detailed file information
const files = await fs.readdir("./", { withFileTypes: true });

files.forEach(file => {
    if (file.isDirectory()) {
        console.log(`📁 ${file.name} (directory)`);
    } else {
        console.log(`📄 ${file.name} (file)`);
    }
});
```

## 📝 Summary Table

| Operation | Method | Example |
|-----------|--------|---------|
| **Rename** | `rename(oldPath, newPath)` | `fs.rename("a.txt", "b.txt")` |
| **Delete** | `unlink(path)` | `fs.unlink("a.txt")` |
| **Move** | `rename(oldPath, newPath)` | `fs.rename("a.txt", "folder/a.txt")` |
| **Copy** | `copyFile(src, dest)` | `fs.copyFile("a.txt", "b.txt")` |
| **Create/Write** | `writeFile(path, data)` | `fs.writeFile("a.txt", "Hello")` |
| **Append** | `appendFile(path, data)` | `fs.appendFile("a.txt", "More text")` |
| **Check Exists** | `access(path)` | `fs.access("a.txt")` |
| **List Files** | `readdir(path)` | `fs.readdir("./")` |

## 💡 Best Practices

- Prefer **Promise-based API** (`fs/promises`) for cleaner code with async/await.
- Always wrap file operations in **try...catch** to handle errors.
- Avoid **blocking (sync)** methods in production.

## 🛠️ Error Handling Examples

### Comprehensive File Operations with Error Handling

```javascript
import fs from "node:fs/promises";

async function fileOperations() {
    try {
        // Check if file exists before operations
        await fs.access("source.txt");
        console.log("✅ Source file exists");
        
        // Copy file
        await fs.copyFile("source.txt", "backup.txt");
        console.log("✅ File copied to backup");
        
        // Rename original
        await fs.rename("source.txt", "renamed.txt");
        console.log("✅ File renamed");
        
        // Read directory contents
        const files = await fs.readdir("./");
        console.log("📁 Directory contents:", files);
        
    } catch (error) {
        console.error("❌ Error:", error.message);
        
        // Handle specific error types
        if (error.code === 'ENOENT') {
            console.log("File or directory does not exist");
        } else if (error.code === 'EACCES') {
            console.log("Permission denied");
        }
    }
}

fileOperations();
```

## 📁 Directory Operations

### Creating Directories

```javascript
import fs from "node:fs/promises";

// Create a single directory
await fs.mkdir("newFolder");

// Create nested directories
await fs.mkdir("path/to/nested/folder", { recursive: true });
```

### Removing Directories

```javascript
import fs from "node:fs/promises";

// Remove empty directory
await fs.rmdir("emptyFolder");

// Remove directory and all contents (Node.js 14.14.0+)
await fs.rm("folderWithContents", { recursive: true, force: true });
```

## 📊 File Information

### Getting File Stats

```javascript
import fs from "node:fs/promises";

const stats = await fs.stat("file.txt");

console.log("File size:", stats.size, "bytes");
console.log("Created:", stats.birthtime);
console.log("Modified:", stats.mtime);
console.log("Is file:", stats.isFile());
console.log("Is directory:", stats.isDirectory());
```

## 🔄 Bulk Operations Example

```javascript
import fs from "node:fs/promises";
import path from "node:path";

async function processAllTextFiles() {
    try {
        const files = await fs.readdir("./");
        const textFiles = files.filter(file => path.extname(file) === '.txt');
        
        for (const file of textFiles) {
            // Create backup
            await fs.copyFile(file, `backup_${file}`);
            
            // Add timestamp to original
            const timestamp = new Date().toISOString();
            await fs.appendFile(file, `\n\nProcessed: ${timestamp}`);
        }
        
        console.log(`✅ Processed ${textFiles.length} text files`);
    } catch (error) {
        console.error("❌ Bulk operation failed:", error.message);
    }
}
```

## 🚀 Quick Commands Reference

| Task | Command |
|------|---------|
| Create file | `await fs.writeFile("file.txt", "content")` |
| Read file | `await fs.readFile("file.txt", "utf-8")` |
| Copy file | `await fs.copyFile("src.txt", "dest.txt")` |
| Move file | `await fs.rename("old.txt", "new/path.txt")` |
| Delete file | `await fs.unlink("file.txt")` |
| Check exists | `await fs.access("file.txt")` |
| List directory | `await fs.readdir("./")` |
| Get file info | `await fs.stat("file.txt")` |
| Create folder | `await fs.mkdir("folder")` |
| Delete folder | `await fs.rm("folder", { recursive: true })` |

Remember: Always handle errors gracefully and prefer the Promise-based API for modern, clean code!