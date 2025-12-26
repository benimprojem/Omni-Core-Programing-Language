# NIM (NIMBLE) Programming Language Reference Manual

**Version:** 1.0 (Final Reference)  
**Date:** 12.12.2025

This document is the **complete** reference source for the NIM (NIMBLE) programming language. All data types, syntax variants, compiler rules, and standard library functions offered by the language are explained in minute detail.

---

## Table of Contents

1.  [Introduction and Philosophy](#1-introduction-and-philosophy)
2.  [Basic Syntax](#2-basic-syntax)
3.  [Variables and Data Types](#3-variables-and-data-types)
4.  [Data Structures and Collections](#4-data-structures-and-collections)
5.  [Operators](#5-operators)
6.  [Control Flow](#6-control-flow)
7.  [Functions and Functional Programming](#7-functions-and-functional-programming)
8.  [Modular Programming and Libraries](#8-modular-programming-and-libraries)
9.  [Object-Oriented Programming (Struct & Group)](#9-object-oriented-programming-struct--group)
10. [Memory and System Access](#10-memory-and-system-access)
11. [Standard Library Reference](#11-standard-library-reference)
12. [Module-Independent Helper Functions (Built-ins)](#12-module-independent-helper-functions-built-ins)
13. [Compiler Targets and Optimization](#13-compiler-targets-and-optimization)

---

## 1. Introduction and Philosophy

NIMBLE is a hybrid language that gives control to the developer while offering modern conveniences.
*   **Full Control:** Direct access to memory (`ptr`), CPU registers (`cpu`), and GPU cores (`gpu`).
*   **Flexibility:** Supports both Functional (chaining, lambda, UFCS) and OOP (struct-group) paradigms.
*   **Clarity:** Explicit module prefix definitions (`:prefix;`) and readable C-style syntax.

---

## 2. Basic Syntax

### 2.1 Blocks and Expressions
*   **Termination:** A semicolon (`;`) is mandatory at the end of every expression.
*   **Blocks:** Curly braces `{ ... }` are used to create scopes.

### 2.2 Comments

```nim
// This is a single-line comment.

/* 
   This is a
   multi-line (block) comment.
*/
```

---

## 3. Variables and Data Types

NIMBLE is a statically typed language but supports dynamism with the `any` type.

### 3.1 Identifiers (`var`, `let`, `must`, `const`)

*   **`var`**: Mutable variable.
    ```nim
    var counter: i32 = 0;
    counter = 1; // Valid
    ```
*   **`let`**: Immutable constant (Default).
    ```nim
    let pi: f32 = 3.14;
    // pi = 3.15; // ERROR!
    ```
*   **`mut let`**: Mutable `let` (similar to `var`, style preference).
*   **`must`**: Values that must absolutely interpret or exist at compile-time (like Assertion).
    ```nim
    must var config = load_config(); // Compilation stops if it cannot be loaded.
    ```
*   **`const`**: Compile-time constant.
    ```nim
    const VERSION = "1.0.0";
    ```

### 3.2 Integer Types

| Type  | Bit Width | Signed? | Value Range | Common Usage |
|---|---|---|---|---|
| `i8`  | 8-bit | Yes | -128 .. 127 | ASCII Character, Signed Byte |
| `u8`  | 8-bit | No  | 0 .. 255    | Raw Data, Pixel, Byte |
| `i16` | 16-bit| Yes | -32,768 .. 32,767 | Audio Samples, Sensor Data |
| `u16` | 16-bit| No  | 0 .. 65,535 | Port Numbers, Counters |
| `i32` | 32-bit| Yes | -2.14 Billion .. +2.14 Billion | Default Integer |
| `u32` | 32-bit| No  | 0 .. 4.29 Billion | Sizes, Indices |
| `i64` | 64-bit| Yes | Very Large  | Timestamp, File Size |
| `u64` | 64-bit| No  | 0 .. 18 Quintillion | Memory Addresses (Pointer) |
| `i128`| 128-bit| Yes| Astronomical| Cryptography, Scientific Calculation |
| `u128`| 128-bit| No | Maximum     | Hash Values (UUID, GUID) |

### 3.3 Floating Point Numbers

*   `f32`: Single precision (7 digits). For graphics and game physics.
*   `f64`: Double precision (15 digits). Default float type.
*   `f128`: Quad precision. Scientific simulations.
*   Also, `float` types can be expressed as `f64[2]` when printing via `print()` or `echo()`, meaning only the first 2 decimal digits will be shown/considered.

### 3.4 Fixed Point Numbers

Used for currency calculations to avoid rounding errors.

*   `d32`: Low precision financial value.
*   `d64`: Standard financial value.
*   `d128`: High precision financial value.

### 3.5 Special Hardware and System Types
*   **`bit`**: Occupies only 1-bit (`0` or `1`). For hardware flags instead of Boolean.
*   **`byte`**: Alias for `u8`.
*   **`hex`**: Hexadecimal data literal. `var addr: hex = 0xFF1A;`
*   **`ptr`**: Untyped raw memory pointer (equivalent to `void*`).
*   **`any`**: Dynamic type. Can hold any data at runtime.

---

## 4. Data Structures and Collections

### 4.1 Arrays

In NIMBLE, arrays are defined and initialized with square brackets `[]`.

**Static (Fixed Size) Arrays:**
Stored on the Stack. Size must be known at compile time.

```nim
var numbers[5]: i32 = [1, 2, 3, 4, 5];
var matrix[3][3]: i32 = [
    [1, 0, 0],
    [0, 1, 0],
    [0, 0, 1]
];
```

**Dynamic (Growable) Arrays:**
Stored on the Heap. Uses `T[]` syntax.

```nim
var list: i32[] = [10, 20, 30];
list.push(40); // Add to end
```

**Functions:** (See: `array` Standard Module)
*   `push`, `pop`, `count`, `clear`, `find`, `sort`, `reverse`.

### 4.2 Maps / Dictionaries

Holds Key-Value pairs. `map<KeyType, ValueType>`

```nim
var grades: map<str, i32>;
grades["Alice"] = 90;
grades["Bob"] = 85;

var grade: i32 = grades["Alice"]; // 90
```

### 4.3 Mathematical Vectors
SIMD-supported types for game and graphics programming.

*   `Vec2`: `{x, y}`
*   `Vec3`: `{x, y, z}`
*   `Vec4`: `{x, y, z, w}`

```nim
var v1 = Vec3{1.0, 2.0, 3.0};
var v2 = Vec3{4.0, 5.0, 6.0};
var result = v1 + v2; // {5.0, 7.0, 9.0} automatically added.
```

---

## 5. Operators

*   **Arithmetic:** `+`, `-`, `*`, `/` (divide), `%` (mod), `++` (increment), `--` (decrement).
*   **Logical:** `&&` (AND), `||` (OR), `!` (NOT).
*   **Bitwise:** `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (LEFT SHIFT), `>>` (RIGHT SHIFT).
*   **Comparison:**
    *   `==`: Value equality.
    *   `===`: Identity (Reference/Address) equality.
    *   `!=`: Not equal.
    *   `!==`: Not same object.
    *   `<`, `>`, `<=`, `>=`.
*   **Access:**
    *   `.`: Member access (`human.name`).
    *   `.`: Static/Module access (`network.http.get`). // Due to group function structure, member access is done with dot.

---

## 6. Control Flow

### 6.1 `if - elseif - else`

```nim
var x = 10;
if (x > 100) {
    print("Large");
} elseif (x == 10) {
    print("Equal");
} else {
    print("Small");
}
```

### 6.2 `match` (Pattern Matching)

One of NIMBLE's most powerful features. There are 4 different usage variants.

**1. Simple Match:**
```nim
match (status) {
    1 => {print("Active")},
    0 => {print("Passive")},
    def => {print("Unknown")} // Default
}
```

**2. Multi-Selection:**
Matching multiple values in a single line.
```nim
match (num) {
    1, 3, 5, 7, 9 => {print("Odd Number")},
    2, 4, 6, 8, 0 => {print("Even Number")}
}
```

**3. As Expression:**
Assigning the result to a variable.
```nim
var message = match (code) {
    200 => "Success",
    404 => "Not Found",
    500 => "Server Error",
    def => "Unknown Error"
};
```

**4. With `Result` and `Option` Types:**
Used in error handling.
```nim
var f = file.open("test.txt", file.READ);
match (f) {
    Ok(h) => h.read_all(), // h: FileHandle
    Err(e) => echo("Error: {e}")
}
```

### 6.3 Loops

**1. C-Style For Loop:**
Traditional, comma-separated structure.
```nim
for (i=0, i<10, i++) {
    echo(i);
}
```
```nim
// i=0;
for (i, i<10, i++) {
    echo(i);
}
```

**2. Range Loop:**
`start..end` (inclusive).
```nim
// i is automatically defined as "i:i32=0" here.
// range can be defined as (A..Z), (a..z), (0..999).
for (i in 0..10) {
    echo(i); // 0, 1, ..., 10
}
```

**3. Collection Loop (Foreach):**
```nim
var names = ["Alice", "Bob"];
for (name in names) {
    echo(name);
}
```

**4. While Loop:**
```nim
while (condition) {
    // ...
}
```

**5. Infinite Loop (`loop`):**
```nim
loop {
    if (end) break;
}
```

### 6.4 `rolling` (Fault Tolerant Block)

A mechanism that allows a block of code to be retried a specified number of times if it errors. The special variable `$rolling` holds the current attempt count.

```nim
// Block name: CONNECTION
rolling:CONNECTION => {
    // $rolling variable is automatically defined and starts at 0.
    
    echo("Trying to connect... Attempt: {$rolling}");
    
    var conn = network.connect("server.com");
    
    if (conn.error && $rolling < 5) {
        time.sleep(1000); // Wait 1 second
        rolling:CONNECTION; // Rewind block (like goto but safe)
    }
}
```

---

## 7. Functions and Functional Programming

### 7.1 Definition Styles

**Standard Definition:**
```nim
fn add(a: i32, b: i32): i32 {
    return a + b;
}
```

**Void Function (Procedure):**
If return type is not specified, it is considered `void`.
```nim
fn greet(name: str) {
    print("Hello {name}");
}
```

**Expression Body (One Liner):**
uses `->` instead of curly braces.
```nim
fn square(x: i32) -> x * x;
```

**GPU Kernel Function (`gpu`):**
Functions that will run in parallel on the graphics card.
```nim
gpu fn pixel_proc(data: u8[]) {
    // GPU code...
}
```

### 7.2 Parameter Features

*   **Default Value:** `fn add(a: i32, b: i32 = 0)`
*   **Named Call:** `add(a: 5, b: 10)` or `add(b: 10, a: 5)`
*   **Variable Arguments (Varargs):** `...`
    ```nim
    fn log(messages: str...) { ... }
    log("Error", "File", "Missing");
    ```

### 7.3 UFCS (Uniform Function Call Syntax)
Allows functions to be called as if they were a method of their first parameter. This eases chaining.

```nim
// Standard call:
string.to_upper(string.trim("  nim  "));

// Call with UFCS (Readable):
"  nim  ".trim().to_upper();
```

### 7.4 Higher-Order Functions (HOF)
Functions that can take functions as parameters or return them.

```nim
fn process(arr: i32[], op: fn(i32): i32) {
    for (i in arr) {
        op(i);
    }
}
```

---

## 8. Modular Programming and Libraries

### 8.1 Module Definition (`:prefix;`)
At the very beginning of every module file (`.nim`), there is a special definition determining which prefix that module will be called with in other files.

**Example File: `network.nim`**
```nim
:net; /* uses as prefix.
:;  or used without any prefix. in this way only uses group (http) name. */
export group http { ... }
```

### 8.2 Access Modifiers (`export`)
By default, everything is **private**. `export` or `pub` is used to expose them.

```nim
var hidden: i32 = 0;
export var exposed: i32 = 1;

export fn public_func() { ... }
```

### 8.3 Usage (`use`)
```nim
use network;       // Load network.nim file.
// Since :net; is defined at file start:
net.http.get(...); 

use math as m;     // Load math module with alias 'm'.
m.sin(30);
```

---

## 9. Object-Oriented Programming (Struct & Group)

NIMBLE strictly separates data (`struct`) and behavior (`group`).

### 9.1 Struct (Data Structure)
Holds only fields. Does not contain methods.

```nim
struct Player {
    name: str,
    score: i32,
    active: bool
}
```

### 9.2 Group (Behavior Group)
Groups functions (methods).

**RULE:** If a `group` is going to add methods to a `struct`, their names must be **EXACTLY THE SAME**.

```nim
group Player {
    /* if struct is defined inside group, it cannot be accessed directly from outside,
        can only be reached using group methods */
    struct Player {  
        name: str,
        score: i32,
        active: bool
    }

    // Constructor - Static Function
    new => fn(name: str): Player {
        return Player { 
            name: name, 
            score: 0, 
            active: true 
        };
    }

    // Object Method
    // First parameter must be 'self'. Type (Player) is not specified (inferred).
    increase_score => fn(self, amount: i32) {
        self.score = self.score + amount;
    }
    
    // One-line method
    get_name => fn(self) -> self.name;
}

// Usage:
var p1 = Player.new("John");
p1.increase_score(10);
print(p1.get_name()); // John
```

---

## 10. Memory and System Access

### 10.1 `memory` Module: Manual Management
NIMBLE normally uses automatic memory management. However, C-style manual management is possible with the `memory` module. For advanced users.

> **Important Note:** Even when the `memory` module is used, the compiler checks allocated memory and data types in the background to prevent errors. In case of extreme mismatch or overflow, it produces an error at compile-time or runtime. This way, memory leaks and critical errors are prevented as much as possible even in manual management.

```nim
use memory;

// Allocate 1024 bytes
var ptr = memory.alloc(1024);

// Use memory (unsafe)
memory.set(ptr, 0, 1024); // Zero out

// Free
memory.free(ptr);

// Data Read/Write (Dereferencing)
memory.write<i32>(ptr, 0, 100);      // ptr[0] = 100
var val = memory.read<i32>(ptr, 0);  // val = ptr[0]

// Block Operations
memory.copy(src_ptr, dest_ptr, 1024); // memcpy
memory.move(src_ptr, dest_ptr, 1024); // memmove (safe)
```

**Basic Functions:**
*   `alloc(size) -> ptr`
*   `realloc(ptr, new_size) -> ptr`
*   `free(ptr)`
*   `set(ptr, val_u8, size)`: Fills specified area with byte (memset).
*   `read<T>(ptr, offset)`: Reads value from address.
*   `write<T>(ptr, offset, val)`: Writes value to address.
*   `copy(src_ptr, dest_ptr, 1024)`: memcpy
*   `move(src_ptr, dest_ptr, 1024)`: memmove (safe)

### 10.2 `asm` and `fastexec`:
fastexec is a wrapper for asm codes, where variable passing is done automatically and is an optimizing block.
CPU register control for Inline Assembly codes.

```nim

fn Calc():void {
	// aggressive optimization applied.
	fastexec {
		var a:i32 = 10;
		var b:i32 = 20;
		var total:i32;
		// if asm blocks are not within fastexec scope, error is produced.
        // asm blocks can make calls to other asm blocks like asm's normal label calls.
		asm: CRITICAL_ADD { 
			mov rax, %a 
			add rax, %b 
			mov %total, rax 
		} 
		echo(total)
	}
}

```

### 10.3 `cpu` Module: Processor Control
**Requirement:** `cpu` module functions work **only inside `fastexec` block**. If called outside this scope, the compiler produces an error.

This module allows direct interaction with cores, registers, and low-level processor instructions.

*   `core_count() -> i32`: Returns number of logical cores in the system.
*   `reg_get(reg_id) -> u64`: Reads value of specified register (e.g., `cpu.RAX`).
*   `reg_set(reg_id, val)`: Writes value to register. **Use with caution.**
*   `pause()`: Sends "pause" (spin-loop hint) signal to processor. Used in loops for energy saving and HT performance.
*   `flush(ptr)`: Clears specified address from CPU cache.
*   `rdtsc() -> u64`: Reads Time-Stamp Counter value (For very precise timing).
*   `get_freq() -> i32`: Returns instantaneous CPU frequency (MHz).

**Usage Example:**

```nim
fn LowLevelOp() {
    fastexec {
        // Get core count
        var cores = cpu.core_count();
        
        // Precise time measurement (in Cycles)
        var start = cpu.rdtsc();
        
        asm: OPERATION {
            mov rax, 100
            // ...
        }
        
        var end = cpu.rdtsc();
        var cycles = end - start;
        
        // Spin-wait loop example
        while (is_busy) {
            cpu.pause(); // Relax CPU
        }
    }
    // cpu.rdtsc(); // ERROR! cannot be used outside fastexec.
}
```

---

## 11. Standard Library Reference

This section is the complete list of modules in the NIMBLE standard library.

### 11.1 `io` Module
**Description:** Standard Input/Output operations.

*   `fn print(msg: str, style: any = "default"): void` - Writes formatted text to screen.
    *   **Styles:** `io.INFO`, `io.WARN`, `io.ERROR`, `io.SUCCESS`.
    *   **Custom:** Users can define their own style structs.
    *   **Usage:** `io.print("Operation failed", io.ERROR);`
*   `fn println(val: any): void` - Writes to screen and appends newline (Simple output).
*   `fn eprint(val: any): void` - Writes to error stream (stderr).
*   `fn prompt(msg: str): str` - Shows message and asks for input.
*   `fn input(): str` - Reads data from keyboard.
*   `fn flush(): void` - Clears output buffer.

**Example:**
```nim
var name = io.prompt("Name: ");
io.println("Hello {name}");

// Formatted Output
io.print("System Check...", io.INFO);
io.print("Critical Failure!", io.ERROR);
```

### 11.2 `string` Module
**Description:** Text processing functions.
// :str; 

*   `len(s)`: Character count. /str.len() / acts same as module-independent strlen(string).
*   `is_empty(s)`: Check emptiness.
*   `to_upper(s)`, `to_lower(s)`: Conversion.
*   `trim(s)`: Clean whitespaces.
*   `split(s, separator)`: Split and return array.
*   `join(array, joiner)`: Join.
*   `contains(s, search)`, `starts_with`, `ends_with`.
*   `replace(s, old, new)`.
*   `substring(s, start, length)`.

**Example:**
```nim
var s = "  hello world  ";
var clean = s.trim().to_upper(); // "HELLO WORLD"
if clean.contains("WORLD") { ... }
```

### 11.3 `array` Module
**Description:** Dynamic array tools.

*   `count(arr)`: Element count.
*   `capacity(arr)`: Allocated memory capacity.
*   `push(arr, val)`: Add to end.
*   `pop(arr)`: Remove from end (Returns `Result`).
*   `insert(arr, index, val)`: Insert in between.
*   `remove(arr, index)`: Delete.
*   `find(arr, val)`: Search (Returns `Option<index>`).
*   `sort(arr)`: Sort.
*   `reverse(arr)`: Reverse.
*   `clear(arr)`: Clear.
*   `new_capacity(size)`: Create optimized array.

**Example:**
```nim
var nums: i32[] = [3, 1, 4];
nums.sort(); // [1, 3, 4]
nums.push(2);
nums.reverse(); // [2, 4, 3, 1]
nums.sort(); // [1, 2, 3, 4]
nums.clear(); // []
nums.new_capacity(10); // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

### 11.4 `math` Module
**Description:** Mathematical functions and constants (works on `f64`).

*   **Constants:** `PI`, `E`, `INF`, `NAN`.
*   **Basic:** `abs`, `pow(x, y)`, `sqrt`.
*   **Logarithmic:** `exp`, `log` (ln), `log10`.
*   **Trigonometric:** `sin`, `cos`, `tan`, `atan2`.
*   **Conversion:** `deg_to_rad`, `rad_to_deg`.
*   **Rounding:** `round`, `floor`, `ceil`, `trunc`.

**Example:**
```nim
var radius = 5.0;
var area = math.PI * math.pow(radius, 2.0);
var deg_rad = math.deg_to_rad(45.0);
```

### 11.5 `file` Module
**Description:** File system operations.

*   `open(path, mode) -> Result<Handle, Error>`
*   Modes: `READ`, `WRITE`, `APPEND`, `READ_WRITE`.
*   `read_all(handle) -> str`
*   `write(handle, data)`
*   `close(handle)`
*   `exists(path) -> bool`
*   `delete(path)`
*   `copy(src, dest)`

**Example:**
```nim
match (file.open("note.txt", file.WRITE)) {
    Ok(h) => {
        file.write(h, "Test data");
        file.close(h);
    },
    Err(e) => io.eprint("Error: {e}")
}
```

### 11.6 `network` Module
**Prefix:** `:net;` (Called as `net` in usage.)

This module collects all network operations under three main groups.

#### ** Group: `net.http` (Web Client and Server)**

*   `get(url) -> Result<Response, Error>`
*   `post(url, body) -> Result<Response, Error>`
*   `server(port, handler_fn)`: Starts a simple HTTP server.

**Example (Client):**
```nim
var response = net.http.get("https://api.example.com/data");
if response.is_ok() {
    echo(response.unwrap().body);
}
```

**Example (Server):**
```nim
// Start server on port 8080
net.http.server(8080, fn(req) {
    if (req.path == "/hello") return "Hello World";
    return 404; // Not Found
});
```

#### ** Group: `net.socket` (Low Level Socket)**

*   `create(type)`: Creates `TCP` or `UDP` socket.
*   `bind(sock, addr, port)`
*   `listen(sock)`
*   `accept(sock)`
*   `connect(sock, addr, port)`
*   `send(sock, data)`
*   `recv(sock, size)`
*   `close(sock)`

**Example (Raw Socket):**
```nim
var s = net.socket.create(net.TCP);
net.socket.connect(s, "127.0.0.1", 9000);
net.socket.send(s, "Ping");
```

#### ** Group: `net.tcp` (TCP Helpers)**

*   `connect(host, port) -> Stream`: Fast connection.
*   `listen(port, handler)`: Fast server.

```nim
var stream = net.tcp.connect("google.com", 80);
stream.write("GET / HTTP/1.1\r\n\r\n");
```

### 11.7 `database` Module
**Prefix:** `:db;`

Contains `sqlite` and `nosql` groups for database operations.

#### ** Group: `db.sqlite` (Relational Database)**

*   `connect(path) -> Connection`
*   `exec(conn, sql)`: Execute SQL command (CREATE, INSERT).
*   `query(conn, sql) -> Rows`: Query data (SELECT).
*   `close(conn)`

**Example:**
```nim
var con = db.sqlite.connect("users.db");

// Create table
db.sqlite.exec(con, "CREATE TABLE IF NOT EXISTS user (id INT, name TEXT)");

// Insert data
db.sqlite.exec(con, "INSERT INTO user VALUES (1, 'John')");

// Query
var rows = db.sqlite.query(con, "SELECT * FROM user");
for (row in rows) {
    echo("User: {row['name']}");
}

db.sqlite.close(con);
```

#### ** Group: `db.nosql` (Key-Value Store)**

Simple, embedded KV (Key-Value) database.

*   `open(path) -> Store`
*   `set(store, key, value)`
*   `get(store, key) -> Option<val>`
*   `del(store, key)`

**Example:**
```nim
var store = db.nosql.open("cache.dat");

db.nosql.set(store, "session_123", "{user_id: 5}");

var val = db.nosql.get(store, "session_123");
if val.is_some() {
    echo("Session Data: {val.unwrap()}");
}
```

### 11.8 `os` Module
**Description:** OS specific operations.

*   `args()`: Gives command line arguments as array.
*   `get_env(key)`, `set_env(key, val)`.
*   `cwd()`: Current working directory.
*   `chdir(path)`, `mkdir(path)`, `rmdir(path)`.
*   `execute(cmd)`: Execute system command.
*   `exit(code)`: Exit program.

**Example:**
```nim
var files = os.execute("ls -la");
var user = os.get_env("USER").unwrap_or("Guest");
```

### 11.9 `crypto` Module
**Description:** Cryptographic operations.

*   `hash(algo, data)`: SHA256, MD5 etc.
*   `hmac(key, data)`.
*   `generate_key()`.
*   `encrypt(algo, key, data)`.
*   `decrypt(algo, key, data)`.

**Example:**
```nim
var pass_hash = crypto.hash(crypto.SHA256, "secret123");
print("Hash: {pass_hash}");
```

### 11.10 `time` Module
**Description:** Date and Time.

*   `now()`: Unix timestamp.
*   `sleep(ms)`: Wait.
*   `utc_now()`, `local_now()`: Returns DateTime structure.
*   `format(dt, fmt_str)`.
*   `measure_start()`, `measure_end()`: Performance measurement.

**Example:**
```nim
var start = time.measure_start();
time.sleep(500); // sleep 500ms
var duration = time.measure_end(start);
echo("Elapsed time: {duration}");
```

### 11.11 `rand` Module
**Description:** Random number generation.

*   `new_rng()`: Standard generator.
*   `new_secure()`: Cryptographically secure generator.
*   `range_i32(rng, min, max)`.
*   `f64(rng)`: Between 0.0 - 1.0.
*   `choice(rng, array)`.

**Example:**
```nim
var rng = rand.new_rng();
var dice = rand.range_i32(rng, 1, 7); // 1-6
```

### 11.12 `regex` Module
**Description:** Regular expressions.

*   `compile(pattern)`.
*   `match(regex, text)`, `find(regex, text)`.
*   `replace(regex, text, new)`.
*   `Match` structure: `grp(i)`, `start()`, `end()`.

**Example:**
```nim
var re = regex.compile("\\d+").unwrap(); // Find numbers
var result = re.find("Order No: 12345");
if result.is_some() {
    echo("Number: {result.unwrap().text()}"); // 12345
}
```

### 11.13 `json` Module
**Description:** JSON parsing and generation.

*   `parse(str) -> JsonValue`.
*   `stringify(val) -> str`.
*   `JsonValue` methods: `get(key)`, `at(index)`, `as_str()`, `as_i32()`.

**Example:**
```nim
var data = json.parse("{\"id\": 1, \"active\": true}").unwrap();
var id = data.get("id").as_i32().unwrap();
```

### 11.14 `log` Module
**Description:** Logging system.

*   Levels: `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL`.
*   Functions: `log.info(msg)`, `log.error(msg)` etc.
*   `set_level(lvl)`, `add_target(file)`.

**Example:**
```nim
log.set_level(log.WARN);
log.info("This is invisible");
log.error("This is visible");
```

### 11.15 `gpu` Module
**Description:** GPU programming interface.

*   `select_device(id)`.
*   `compile_kernel(fn)`.
*   `alloc_array(dev, size)`, `to_gpu(arr)`, `from_gpu(arr)`.
*   `launch(kernel, grid, block, args...)`.
*   `sync(dev)`.

**GPU Kernel Function (`gpu`):**
Functions that run in parallel on graphics card.
```nim
gpu fn pixel_proc(data: u8[]) {
    // GPU code...
}
```

### 11.16 `thread` Module
**Description:** Thread management.

*   `spawn(fn)`: New thread.
*   `join(handle)`: Wait.
*   `Mutex`, `Semaphore`, `Channel` (Messaging channels).

**Example:**
```nim
thread.spawn(fn() {
    print("Running in background...");
});
```

### 11.17 `ffi` Module (Foreign Function Interface)
**Description:** Connection with C/C++ libraries.

*   `extern fn`: Define external function.
*   `#[link(name="lib")]`: Link library.

**Example:**
```nim
#[link(name="m")]
extern fn sqrt(x: f64): f64;
// Usage: sqrt(16.0);
```

### 11.18 `error` Module
**Description:** Error types and management. `Result` and `Option` are basic building blocks.

*   `panic(msg)`: Stop program.
*   `unwrap()`, `unwrap_or(default)`.

### 11.19 `types` Module
**Description:** Advanced type conversion, type checking, and safe/unsafe conversion tools. Provides more detailed control than built-in `int()`/`float()` functions.

*   **Safe Conversions (Checked Casts):**
    *   `to_i32(val) -> Result<i32, Error>`: Error checked conversion.
    *   `to_f64(val) -> Result<f64, Error>`
    *   `to_str(val) -> str`
    *   `parse<T>(str) -> Result<T, Error>`: General parsing.
*   **Type Checking (Runtime Type Info):**
    *   `is_type<T>(val) -> bool`: Is variable that type?
    *   `name_of(val) -> str`: Returns type name.
    *   `size_of<T>() -> i32`: Size of type.
*   **Unsafe Conversions (Unsafe Casts):**
    *   `as_type<T>(val)`: Force convert (Data loss possible).
    *   `cast<T>(val)`: Bitwise Reinterpret Cast (Raw memory interpretation).

**Example:**
```nim
var s:str = "123";
if types.is_type<str>(s) {
    var num = types.parse<i32>(s).unwrap();
}

// Bitwise conversion (float -> int bits)
var f:f32 = 1.0;
var bits = types.cast<i32>(f);
```

### 11.20 `generics`
Core feature of the language. Used with `<T>` syntax.

```nim
fn make_box<T>(value: T) { ... }
```

### 11.21 `linalg` Module (Linear Algebra & Vector)
**Description:** Vector and Matrix arithmetic for game, physics, and graphics programming. Works on `Vec2`, `Vec3`, `Vec4` types.

*   `dot(v1, v2)`: Dot product (Scalar result).
*   `cross(v1, v2)`: Cross product (`Vec3`).
*   `normalize(v)`: Makes unit vector (Length = 1).
*   `access(v)`: Calculates length (magnitude).
*   `distance(v1, v2)`: Distance between two points.
*   `lerp(v1, v2, t)`: Linear interpolation (t: 0.0 - 1.0).
*   `rotate(v, axis, angle)`: Rotates vector.

**Example:**
```nim
var v = Vec3{0, 1, 0};
var dir = linalg.rotate(v, Vec3{0, 0, 1}, 90.0);
```

### 11.22 `collections` Module (Advanced Data Structures)
**Description:** Data structures other than standard `array` and `map`.

*   `Stack<T>`: LIFO (Last In First Out) stack. `push`, `pop`, `peek`.
*   `Queue<T>`: FIFO (First In First Out) queue. `enqueue`, `dequeue`.
*   `Set<T>`: Unique element set. `add`, `contains`, `remove`, `intersect`, `union`.
*   `LinkedList<T>`: Doubly linked list.

**Example:**
```nim
var set = collections.Set<i32>();
set.add(1);
set.add(1); // Not added (Already exists)
```

### 11.23 `path` Module (File Path Operations)
**Description:** Manages file paths independently of OS (Windows/Linux).

*   `join(parts...)`: Joins paths (automatic `/` or `\`).
*   `ext(path)`: Gives file extension.
*   `base(path)`: Gives file name.
*   `dir(path)`: Gives directory path.
*   `abs(path)`: Gives absolute path.

**Example:**
```nim
var full_path = path.join(os.cwd(), "data", "img.png");
```

### 11.24 `process` Module (Process Management)
**Description:** Execute and manage external programs.

*   `spawn(cmd, args) -> Pid`: Starts program.
*   `exec(cmd) -> Output`: Execute and get output.
*   `kill(pid)`: Terminates process.
*   `wait(pid)`: Wait for finish.
*   `pipe()`: Create data stream.

**Example:**
```nim
var output = process.exec("git status").stdout;
echo(output);
```

### 11.25 `encoding` Module
**Description:** Data encoding and compression.

*   `base64_encode(data)`, `base64_decode(str)`.
*   `hex_encode(data)`, `hex_decode(str)`.
*   `compress(data, algo)`, `decompress(data, algo)`: (Gzip, Zlib).

### 11.26 `test` Module (Unit Test)
**Description:** Test framework for software quality.

*   `assert_eq(a, b)`, `assert_ne(a, b)`.
*   `assert_true(cond)`.
*   `run_tests()`: Run all tests.

```nim
fn test_addition() {
    test.assert_eq(2 + 2, 4);
}
```

### 11.27 `data` Module (Data Manipulation)
**Description:** Python-like advanced list and data processing functions. Used for data analysis and manipulation.

*   `slice(arr, start, end) -> Arr`: Copies part of the array (like `arr[start:end]`).
*   `map(arr, fn) -> Arr`: Applies function to every element in array and returns new array.
*   `filter(arr, fn) -> Arr`: Filters elements satisfying the condition.
*   `reduce(arr, fn, init) -> val`: Reduces array to a single value.
*   `zip(arr1, arr2) -> Arr`: Joins two arrays like a zipper `[[a1, b1], [a2, b2]...]`.
*   `enumerate(arr) -> Arr`: Returns index and value pairs `[[0, val0], [1, val1]...]`.
*   `range(start, stop, step) -> Arr`: Creates sequence of numbers.
*   `any(arr, fn) -> bool`: Does at least one element satisfy condition?
*   `all(arr, fn) -> bool`: Do all elements satisfy condition?
*   `sum(arr) -> val`: Sums numerical array.

**Example:**
```nim
var numbers = [1, 2, 3, 4, 5];
var squares = data.map(numbers, fn(x) -> x * x); // [1, 4, 9, 16, 25]
var evens = data.filter(squares, fn(x) -> x % 2 == 0); // [4, 16]
var total = data.sum(evens); // 20
```

### 11.28 `tpu` Module (Tensor Processing Units)
**Description:** Targets specialized Tensor Processing Unit (TPU) or NPU (Neural Processing Unit) hardware for AI and Machine Learning (ML). Optimized for large matrix products and tensor operations.

**1. TPU Kernel (`tpu fn`):**
Code blocks to run on TPU. Usually contains matrix-based singular instructions (SIMD/MIMD).
```nim
tpu fn mat_mul(a: tensor<f32>, b: tensor<f32>) {
    // High speed matrix multiplication specific to TPU
    ...
}
```

**2. Functions:**
*   `load_tensor(data, shape) -> Tensor`: Loads data to TPU memory.
*   `matmul(t1, t2) -> Tensor`: Matrix multiplication (Hardware accelerated).
*   `conv2d(input, kernel) -> Tensor`: 2D Convolution (For CNN).
*   `relu(t) -> Tensor`: Activation function.
*   `sync()`: Waits for TPU operation to finish.

**Example:**
```nim
var t1 = tpu.load_tensor(data1, [1024, 1024]);
var t2 = tpu.load_tensor(data2, [1024, 1024]);
var result = tpu.matmul(t1, t2); // Processing at light speed
```

### 12. Module-Independent Helper Functions (Built-ins)

These functions can be called directly from anywhere without needing any `use` declaration. They are embedded in the core of the language.

#### 12.1 Basic Input/Output
*   **`echo(args...)`**: Versatile screen printing function. Performs automatic type inference and supports string interpolation with curly braces `{}`. More capable and practical version of `print` and `println` functions.
    ```nim
    echo("Hello World\n");             // Plain text
    echo(12345);                       // Numeric value
    echo("Result: {10 + 20}");         // Expression interpolation
    echo("Value: {variable}");         // Variable interpolation
    echo(func_call());                 // Function result
    ```
*   **`input(prompt)`**: Used to take data from user. Can display an optional message (prompt). Always returns `str` (text).
    ```nim
    var name = input("What's your name?: ");
    ```

#### 12.2 Memory and Type Info
*   **`sizeof(T)`**: Returns memory space occupied by a type or variable (in bytes).
*   **`typeof(val)`**: Returns the name of the variable's type at runtime (as `str`).
*   **`default(T)`**: Returns the default value of the specified type (zero, false, null etc.).

#### 12.3 Safety and Debugging
*   **`assert(cond, msg)`**: Stops the program with specified message and error code if condition is `false`. Works only in development (debug) mode.
*   **`panic(msg)`**: Immediately terminates the program and prints stack trace in case of unrecoverable error.
*   **`todo(msg)`**: Placeholder for unimplemented code blocks. Gives "Not Implemented" error if executed.

#### 12.4 Flow Control and Process Management
*   **`exit(code)`**: Immediately terminates program with specified exit code (`0`: Success, `1` and above: Error). Same as `os.exit` but doesn't require module.
*   **`defer(block)`**: Queues code to be executed when the containing block (scope) finishes. Ideal for resource cleanup (file closing, free memory).
    ```nim
    {
        var f = file.open("data.txt", file.READ);
        defer { file.close(f); } // Automatically runs when block ends.
        // ... file operations ...
    }
    ```

#### 12.5 Math and Logic Helpers
*   **`min(a, b)`**: Returns the smaller of two values.
*   **`max(a, b)`**: Returns the larger of two values.
*   **`clamp(val, min, max)`**: Limits value to specified range (returns `min` if `val < min`, `max` if `val > max`).
*   **`swap(a, b)`**: Swaps values of two variables safely and quickly.

#### 12.6 Universal Type Conversions
Shortcuts for quick type casting without using module.

*   **`int(val)` / `stri32(val)`**: Converts given value (especially string) to integer. Necessary to use data from `input` in math.
    ```nim
    var age = int(input("Age: ")); // or stri32(input)
    ```
*   **`float(val)`**: Converts value to float (`f64`).
*   **`str(val)`**: Converts any value to text.
*   **`bool(val)`**: Converts value to logical (`true/false`) type.

#### 12.7 Result Management Methods (Result & Option)
In NIMBLE, functions that can return error or empty value return `Result` or `Option` type. These methods are used to get the real data (or error) inside these boxes.

*   **`val.unwrap()`**: Opens the box and returns value **if successful**. **If error (Err) or empty (None)**, explodes (stops) program with `panic`. Use only when 100% sure.
*   **`val.unwrap_or(default)`**: If box is empty or faulty, returns default value given as parameter instead of exploding. Safe method.
    ```nim
    var num = types.parse<i32>("abc").unwrap_or(0); // Returns 0 if error.
    ```
*   **`val.expect(msg)`**: Like `unwrap` but explodes with your custom message in case of error. Eases debugging.
*   **`val.is_ok()` / `val.is_err()`**: Tells if operation is successful or failed as `bool`.
*   **`val.is_some()` / `val.is_none()` / `val.is_null()`**: Checks if value exists (for `Option`).


### 13. Compiler Targets and Optimization (Targets)

NIMBLE uses `@target` notation and advanced compiler flags to determine on which hardware the code will run most efficiently.

#### 13.1 Processor Architectures (CPU)
Ensures your code is compiled at "Native" speed for a specific processor family.

*   **General Labels:** `x86_64`, `arm64` (Apple Silicon/Raspberry Pi), `riscv64`, `wasm32`.
*   **Usage (`@target`):** You can optimize per function or module.

```nim
// This version compiles/runs only on ARM processors (e.g. M2 Chip)
@target(arch="arm64", feature="neon")
fn fast_matrix() { ... }

// Use this if RISC-V vector extension exists
@target(arch="riscv64", feature="v")
fn fast_matrix() { ... }
```

#### 13.2 GPU and Accelerator Targets
Custom kernel compilations can be defined for different GPU brands and architectures.

*   `nvidia`: Optimized for CUDA cores.
*   `amd`: Optimized for ROCm/HIP.
*   `intel`: Optimized for OneAPI/LevelZero.
*   `apple`: Optimized for Metal performance.

```nim
// Special optimization for NVIDIA cards
@target(vendor="nvidia", arch="sm_90") // H100 GPU
gpu fn calculate() { ... }
```
#### 13.3 Cross-Compilation
Target platform can be changed with flags given to compile. Can be checked with `os.target_arch()` in code.

*   **Supported Targets:**
    *   `win-x64`: Windows Intel/AMD/ARM
    *   `win-x86`: Windows x86
    *   `linux-x64`: Linux Intel/AMD/ARM
    *   `linux-x86`: Linux x86
    *   `linux-arm64`: Linux ARM (Server/Embedded)
    *   `android-riscv`: RISC-V Android Devices
    *   `mac-m` series: Apple Silicon


---
**End of NIMBLE Reference Document.**
