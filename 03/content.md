# I/O & Threading

### Rust ISP 2019 Lecture 01
Based on: [CIS 198 slides](https://github.com/cis198-2016s/slides)

Artyom Pavlov, 2019.

---
# I/O

---
## Traits!

```rust
pub trait Read {
    fn read(&mut self, buf: &mut [u8]) -> Result<usize>;

    // Other methods implemented in terms of read().
}

pub trait Write {
    fn write(&mut self, buf: &[u8]) -> Result<usize>;
    fn flush(&mut self) -> Result<()>;

    // Other methods implemented in terms of write() and flush().
}
```

- Standard IO traits implemented for a variety of types:
    - `File`s, `TcpStream`s, `Vec<T>`s, `&[u8]`s.
- Careful: return types are `std::io::Result`, not `std::Result`!
    - `type Result<T> = Result<T, std::io::Error>;`

---
## `std::io::Read`

```rust
use std::io;
use std::io::prelude::*;
use std::fs::File;

let mut f = try!(File::open("foo.txt"));
let mut buffer = [0; 10];

// read up to 10 bytes
try!(f.read(&mut buffer));
```

- `buffer` is an array, so the max length to read is encoded into the type.
- `read` returns the number of bytes read, or an `Err` specifying the problem.
    - A return value of `Ok(n)` guarantees that `n <= buf.len()`.
    - It can be `0`, if the reader is empty.

---
## Ways of Reading

```rust
/// Required.
fn read(&mut self, buf: &mut [u8]) -> Result<usize>;

/// Reads to end of the Read object.
fn read_to_end(&mut self, buf: &mut Vec<u8>) -> Result<usize>

/// Reads to end of the Read object into a String.
fn read_to_string(&mut self, buf: &mut String) -> Result<usize>

/// Reads exactly the length of the buffer, or throws an error.
fn read_exact(&mut self, buf: &mut [u8]) -> Result<()>
```

- `Read` provides a few different ways to read into a variety of buffers.
    - Default implementations are provided for them using `read`.
- Notice the different type signatures.

---
## Reading Iterators

```rust
fn bytes(self) -> Bytes<Self> where Self: Sized

// Unstable!
fn chars(self) -> Bytes<Self> where Self: Sized
```

- `bytes` transforms some `Read` into an iterator which yields byte-by-byte.
- The associated `Item` is `Result<u8>`.
    - So the type returned from calling `next()` on the iterator is
      `Option<Result<u8>>`.
    - Hitting an `EOF` corresponds to `None`.

- `chars` does the same, and will try to interpret the reader's contents as a
  UTF-8 character sequence.
    - Unstable; Rust team is not currently sure what the semantics of this
      should be. See issue [#27802][].

[#27802]: https://github.com/rust-lang/rust/issues/27802

---
## Iterator Adaptors

```rust
fn chain<R: Read>(self, next: R) -> Chain<Self, R>
    where Self: Sized
```
- `chain` takes a second reader as input, and returns an iterator over all bytes
  from `self`, then `next`.

```rust
fn take<R: Read>(self, limit: u64) -> Take<Self>
    where Self: Sized
```
- `take` creates an iterator which is limited to the first `limit` bytes of the
  reader.

---
## `std::io::Write`

```rust
pub trait Write {
    fn write(&mut self, buf: &[u8]) -> Result<usize>;
    fn flush(&mut self) -> Result<()>;

    // Other methods omitted.
}
```

- `Write` is a trait with two required methods, `write()` and `flush()`
    - Like `Read`, it provides other default methods implemented in terms of
      these.
- `write` (attempts to) write to the buffer and returns the number of bytes
  written (or queued).
- `flush` ensures that all written data has been pushed to the target.
    - Writes may be queued up, for optimization.
    - Returns `Err` if not all queued bytes can be written successfully.
---
## Writing

```rust
let mut buffer = try!(File::create("foo.txt"));

try!(buffer.write("Hello, Ferris!"));
```

---
## Writing Methods

```rust
/// Attempts to write entire buffer into self.
fn write_all(&mut self, buf: &[u8]) -> Result<()> { ... }

/// Writes a formatted string into self.
/// Don't call this directly, use `write!` instead.
fn write_fmt(&mut self, fmt: Arguments) -> Result<()> { ... }

/// Borrows self by mutable reference.
fn by_ref(&mut self) -> &mut Self where Self: Sized { ... }
```

---
## `write!`

- Actually using writers can be kind of clumsy when you're doing a general
  application.
    - Especially if you need to format your output.
- The `write!` macro provides string formatting by abstracting over
  `write_fmt`.
- Returns a `Result`.

```rust
let mut buf = try!(File::create("foo.txt"));

write!(buf, "Hello {}!", "Ferris").unwrap();
```

---
## IO Buffering

- IO operations are really slow.
- Like, _really_ slow:

```rust
TODO: demonstrate how slow IO is.
```

- Why?

---
## IO Buffering

- Your running program has very few privileges.
- Reads are done through the operating system (via system call).
    - Your program will do a _context switch_, temporarily stopping execution so
      the OS can gather input and relay it to your program.
    - This is veeeery slow.
- Doing a lot of reads in rapid succession suffers hugely if you make a system
  call on every operation.
    - Solve this with buffers!
    - Read a huge chunk at once, store it in a buffer, then access it
      little-by-little as your program needs.
- Exact same story with writes.

---
## BufReader

```rust
fn new(inner: R) -> BufReader<R>;
```
```rust
let mut f = try!(File::open("foo.txt"));
let buffered_reader = BufReader::new(f);
```

- `BufReader` is a struct that adds buffering to *any* reader.
- `BufReader` itself implements `Read`, so you can use it transparently.

---
## BufReader

- `BufReader` also implements a separate interface `BufRead`.

```rust
pub trait BufRead: Read {
    fn fill_buf(&mut self) -> Result<&[u8]>;
    fn consume(&mut self, amt: usize);

    // Other optional methods omitted.
}
```

---
## BufReader

- Because `BufReader` has access to a lot of data that has not technically been
  read by your program, it can do more interesting things.
- It defines two alternative methods of reading from your input, reading up
  until a certain byte has been reached.

```rust
fn read_until(&mut self, byte: u8, buf: &mut Vec<u8>)
    -> Result<usize> { ... }
fn read_line(&mut self, buf: &mut String)
    -> Result<usize> { ... }
```

- It also defines two iterators.

```rust
fn split(self, byte: u8)
    -> Split<Self> where Self: Sized { ... }
fn lines(self)
    -> Lines<Self> where Self: Sized { ... }
```
---
## BufWriter

- `BufWriter` does the same thing, wrapping around writers.

```rust
let f = try!(File::create("foo.txt"));
let mut writer = BufWriter::new(f);
try!(buffer.write(b"Hello world"));
```

- `BufWriter` doesn't implement a second interface like `BufReader` does.
- Instead, it just caches all writes until the `BufWriter` goes out of scope,
  then writes them all at once.

---
## `StdIn`

```rust
let mut buffer = String::new();

try!(io::stdin().read_line(&mut buffer));
```

- This is a very typical way of reading from standard input (terminal input).
- `io::stdin()` returns a value of `struct StdIn`.
- `stdin` implements `read_line` directly, instead of using `BufRead`.

---
## `StdInLock`

- A "lock" on standard input means only that current instance of `StdIn` can
  read from the terminal.
    - So no two threads can read from standard input at the same time.
- All `read` methods call `self.lock()` internally.
- You can also create a `StdInLock` explicitly with the `stdin::lock()` method.

```rust
let lock: io::StdInLock = io::stdin().lock();
```

- A `StdInLock` instance implements `Read` and `BufRead`, so you can call any of
  the methods defined by those traits.

---
## `StdOut`

- Similar to `StdIn` but interfaces with standard output instead.
- Directly implements `Write`.
- You don't typically use `stdout` directly.
    - Prefer `print!` or `println!` instead, which provide string formatting.
- You can also explicitly `lock` standard out with `stdout::lock()`.

---
## Special IO Structs

- `repeat(byte: u8)`: A reader which will infinitely yield the specified byte.
    - It will always fill the provided buffer.
- `sink()`: "A writer which will move data into the void."
- `empty()`: A reader which will always return `Ok(0)`.
- `copy(reader: &mut R, writer: &mut W) -> Result<u64>`: copies all bytes from
  the reader into the writer.

---
# Serialization

---
## [Serde](https://serde.rs/)

- **Ser**ialization/**De**serialization.
- Serde is built on Rust's powerful trait system. A data structure that knows how to serialize and deserialize itself is one that implements Serde's `Serialize` and `Deserialize` traits (or uses Serde's derive attribute to automatically generate implementations at compile time).
- Supports many formats, like: JSON, bincode, MessagePack, XML, YAML, TOML, etc.

---
## Serd example

```rust
#[macro_use]
extern crate serde_derive;

extern crate serde;
extern crate serde_json;

#[derive(Serialize, Deserialize, Debug)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let point = Point { x: 1, y: 2 };

    // Convert the Point to a JSON string.
    let serialized = serde_json::to_string(&point).unwrap();

    // Prints serialized = {"x":1,"y":2}
    println!("serialized = {}", serialized);

    // Convert the JSON string back to a Point.
    let deserialized: Point = serde_json::from_str(&serialized).unwrap();

    // Prints deserialized = Point { x: 1, y: 2 }
    println!("deserialized = {:?}", deserialized);
}
```


---
## Networking

---
### Sockets

- Most of this section is preface.
- We're not actually going to cover most of what's in it directly.

---
### Sockets

- A basic way to send data over the network.
    - Not to be confused with IPC sockets, which are a Unix thing.
- Abstractly, a socket is just a channel that can send and/or receive data over
    some network.
- Many layers of socket-programming providers:
    - Operating system-provided system calls.
    - Low-level/low-abstraction programming language standard library.
    - Higher-level networking libraries or libraries handling a specific
      protocol (e.g. HTTP).
- Usually, you won't use sockets directly unless you want to do some
    low-level networking.
- Two general types: datagram & stream.

---
### Datagram Sockets (UDP)

- **U**ser **D**atagram **P**rotocol sockets
- Stateless: no connection to establish with another network device.
    - Simply send data to a destination IP and port, and assume they're
        listening.
- "At least once" delivery.
    - Packets are not guaranteed to be delivered in order.
    - Packets may be received more than once.
- Traditionally implement two methods:
    - send_to(addr) -- sends data over the socket to the specified address
    - recv_from() -- listens for data being sent to the socket

---
### `std::net::UdpSocket`

```rust
// Try to bind a UDP socket
let mut socket = try!(UdpSocket::bind("127.0.0.1:34254"));

// Try to receive data from the socket we've bound
let mut buf = [0; 10];
let (amt, src) = try!(socket.recv_from(&mut buf));

// Send a reply to the socket we just received data from
let buf = &mut buf[..amt];
buf.reverse();
try!(socket.send_to(buf, &src));

// Close the socket
drop(socket);
```

&sup1;Taken from the Rust docs.

---
### Stream Sockets (TCP)

- "This is where the drugs kick in" - Matt Blaze on TCP sockets
- **T**ransmission **C**ontrol **P**rotocol sockets
- Stateful: require a connection to be established and acknowledged between two
    clients (using SYN packet).

    - Connection must also be explicitly closed.
- Packets are delivered in-order, exactly once.
    - Achieved via packet sequence numbers.
- Packets have delivery acknowledgement (ACK packet).
- Generally two types of TCP socket:
    - TCP listeners: listen for data
    - TCP streams: send data

---
### `std::net::TcpStream`

- A TCP stream between a local socket and a remote socket.

```rust
// Create a TCP connection
let mut stream = TcpStream::connect("127.0.0.1:34254").unwrap();

// Uses std::io::{Read, Write}

// Try to write a byte to the stream
let write_result = stream.write(&[1]);

// Read from the stream into buf
let mut buf = [0; 128];
let read_result = stream.read(&mut buf);

// ...
// Socket gets automatically closed when it goes out of scope
```

---
### `std::net::TcpListener`

- A TCP socket server.

```rust
let listener = TcpListener::bind("127.0.0.1:80").unwrap();

fn handle_client(stream: TcpStream) { /* ... */  }

// Accept connections and process them,
// spawning a new thread for each one.
for stream in listener.incoming() {
    match stream {
        Ok(stream) => {
            thread::spawn(move|| {
                // connection succeeded
                handle_client(stream)
            });
        }
        Err(e) => { /* connection failed */ }
    }
}

// close the socket server
drop(listener);
```

---
### `SocketAddr`

- A socket address representation.
- May be either IPv4 or IPv6.
- Easily created using...

---
### `ToSocketAddrs`

```rust
pub trait ToSocketAddrs {
    type Iter: Iterator<Item=SocketAddr>;
    fn to_socket_addrs(&self) -> Result<Self::Iter>;
}
```

- A trait for objects which can be converted into `SocketAddr` values.
- Methods like `TcpStream::connect(addr: A)` specify that `A: ToSocketAddr`.
    - This makes it easier to specify what may be converted to a socket
        address-like object.
- See the docs for the full specification.

---
### HTTP
- Request-response text-based protocol which works on top of TCP.
- Foundation of the moddern Web.
- Check-out [arewewebyet.org/](https://www.arewewebyet.org/) for list of crates which can be used for Web development in Rust.
