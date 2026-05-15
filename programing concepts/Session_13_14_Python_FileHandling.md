# Sessions 13 & 14 – Python: File Handling, Permissions & Sockets
> **Syllabus:** 4T + 6L | Module B: Python

---

## 1. File Handling

### Opening Files

```python
# Open modes
f = open("file.txt", "r")    # read (default)
f = open("file.txt", "w")    # write (overwrites existing!)
f = open("file.txt", "a")    # append (adds to end)
f = open("file.txt", "x")    # exclusive create (fails if exists)
f = open("file.txt", "rb")   # read binary
f = open("file.txt", "wb")   # write binary
f = open("file.txt", "r+")   # read and write

f.close()    # always close! Or use context manager:
```

### Context Manager (Recommended)

```python
with open("file.txt", "r") as f:
    content = f.read()
# file is automatically closed after with block
```

### Reading Files

```python
with open("data.txt", "r") as f:
    content = f.read()          # entire file as one string
    
with open("data.txt", "r") as f:
    lines = f.readlines()       # list of lines (includes \n)
    
with open("data.txt", "r") as f:
    line = f.readline()         # one line at a time

# Memory-efficient line-by-line
with open("data.txt", "r") as f:
    for line in f:              # iterator — O(1) memory
        print(line.strip())
```

### Writing Files

```python
with open("output.txt", "w") as f:
    f.write("Hello, World!\n")
    f.writelines(["line1\n", "line2\n", "line3\n"])

# Append
with open("log.txt", "a") as f:
    f.write("New log entry\n")
```

### File Position

```python
with open("data.txt", "r") as f:
    f.read(5)            # read 5 chars
    print(f.tell())      # current position (5)
    f.seek(0)            # move to beginning
    f.seek(0, 2)         # move to end (whence=2)
```

---

## 2. Counting Lines and Words

```python
with open("data.txt", "r") as f:
    content = f.read()
    
lines = content.split("\n")
print(f"Lines: {len(lines)}")

words = content.split()       # splits on any whitespace
print(f"Words: {len(words)}")

chars = len(content)
print(f"Characters: {chars}")

# Alternative — one pass
with open("data.txt") as f:
    line_count = word_count = 0
    for line in f:
        line_count += 1
        word_count += len(line.split())
```

---

## 3. Reading Webpages

```python
import urllib.request

# Simple GET request
with urllib.request.urlopen("http://example.com") as response:
    html = response.read().decode("utf-8")
    print(html[:500])

# Using requests library (3rd party)
import requests

r = requests.get("https://api.github.com")
print(r.status_code)       # 200
print(r.headers)           # response headers
print(r.text)              # response body as text
print(r.json())            # parse JSON response

# POST request
data = {"username": "alice", "password": "pass"}
r = requests.post("https://api.example.com/login", json=data)
```

---

## 4. Directory & File Permissions

```python
import os
import stat

# Get permissions
permissions = os.stat("file.txt").st_mode

# Check specific permissions
os.access("file.txt", os.R_OK)   # readable
os.access("file.txt", os.W_OK)   # writable
os.access("file.txt", os.X_OK)   # executable

# Change permissions (Unix-style octal)
os.chmod("file.txt", 0o644)   # rw-r--r--
os.chmod("script.sh", 0o755)  # rwxr-xr-x
```

### Permission Octal Values

```
Owner | Group | Others
  7   |   5   |   5
 rwx  |  r-x  |  r-x

r = 4, w = 2, x = 1
rwx = 7, rw- = 6, r-x = 5, r-- = 4
```

---

## 5. Socket Programming (Extended)

### TCP vs UDP

| | TCP | UDP |
|--|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Guaranteed delivery | No guarantee |
| Order | Ordered | May arrive out of order |
| Speed | Slower | Faster |
| Use case | HTTP, FTP, SSH | DNS, streaming, gaming |

### Multi-client Server

```python
import socket
import threading

def handle_client(conn, addr):
    print(f"New connection: {addr}")
    while True:
        data = conn.recv(1024)
        if not data:
            break
        conn.send(data)           # echo back
    conn.close()

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
server.bind(("0.0.0.0", 9999))
server.listen(5)

while True:
    conn, addr = server.accept()
    thread = threading.Thread(target=handle_client, args=(conn, addr))
    thread.start()
```

### UDP Socket

```python
import socket

# UDP Server
server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
server.bind(("0.0.0.0", 9999))
data, addr = server.recvfrom(1024)
server.sendto(b"ACK", addr)

# UDP Client
client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
client.sendto(b"Hello", ("127.0.0.1", 9999))
data, server = client.recvfrom(1024)
```

---

## 6. Working with JSON and CSV Files

```python
import json

# Write JSON
data = {"name": "Alice", "scores": [85, 90, 78]}
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

# Read JSON
with open("data.json", "r") as f:
    loaded = json.load(f)

# JSON string ↔ dict
json_str = json.dumps(data)       # dict → string
parsed   = json.loads(json_str)   # string → dict
```

```python
import csv

# Write CSV
with open("data.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["Name", "Age", "Score"])
    writer.writerows([["Alice", 20, 95], ["Bob", 22, 87]])

# Read CSV
with open("data.csv", "r") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["Name"], row["Score"])
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between `read()` and `readlines()`?**
`read()` returns entire file content as one string. `readlines()` returns a list where each element is one line (including `\n`). For large files, iterate line-by-line instead of loading all into memory.

**Q2. What happens when you open a file in `"w"` mode if it already exists?**
The file is **overwritten (truncated to zero bytes)**. Existing content is destroyed. Use `"a"` to append.

**Q3. What is the difference between `"r"` and `"rb"` mode?**
`"r"` reads text (decodes bytes to string, handles newlines per OS). `"rb"` reads raw binary bytes — needed for images, PDFs, executables.

**Q4. Why should you use `with open()` instead of `open()`?**
The context manager guarantees the file is closed even if an exception occurs, preventing resource leaks and data corruption.

**Q5. What does `f.seek(0)` do?**
Moves the file cursor back to position 0 (beginning). After reading with `read()`, the cursor is at the end — seek(0) lets you re-read.

**Q6. What is the difference between TCP and UDP?**
TCP: connection-oriented, reliable, ordered, slower. UDP: connectionless, unreliable, faster. TCP for data integrity (HTTP), UDP for speed (DNS, video).

**Q7. What does `SO_REUSEADDR` socket option do?**
Allows reusing a port immediately after the previous program exits. Without it, you get "Address already in use" error.

**Q8. What is `json.dumps()` vs `json.dump()`?**
`json.dumps()` converts dict to JSON **string**. `json.dump()` writes JSON directly to a **file** object.

**Q9. What is `0o644` in file permissions?**
Octal `644` = `rw-r--r--`: owner can read/write; group and others can only read. Typical for files.

**Q10. How do you count lines without loading the whole file into memory?**
Iterate: `sum(1 for _ in open("file.txt"))` — reads one line at a time, O(1) memory.

---

## 📌 Quick Revision Checklist
- [ ] File modes: r, w, a, x, rb, wb, r+
- [ ] `with open()` context manager
- [ ] `read()`, `readline()`, `readlines()`, iterate
- [ ] `write()`, `writelines()`, seek(), tell()
- [ ] Count lines / words one-pass
- [ ] `urllib.request` and `requests` library
- [ ] File permissions: octal values (644, 755)
- [ ] `os.chmod()`, `os.access()`
- [ ] TCP vs UDP comparison table
- [ ] TCP server with threading
- [ ] `json.dump/load` vs `json.dumps/loads`
- [ ] CSV reading/writing
