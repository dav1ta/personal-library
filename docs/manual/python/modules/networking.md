# Networking: Sockets and Selectors

## TCP Echo Server/Client

```python
# server.py
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind(("127.0.0.1", 9000))
    s.listen()
    conn, addr = s.accept()
    with conn:
        while data := conn.recv(4096):
            conn.sendall(data)
```

```python
# client.py
import socket

with socket.create_connection(("127.0.0.1", 9000), timeout=5) as s:
    s.sendall(b"hello\n")
    print(s.recv(4096))
```

---

## Line Protocols with Text Wrapping

```python
import socket, io

with socket.create_connection(("127.0.0.1", 9000)) as s:
    bio = s.makefile("rwb", buffering=0)               # binary file-like
    txt = io.TextIOWrapper(bio, encoding="utf-8", newline="\n")
    txt.write("PING\n"); txt.flush()
    print(txt.readline().strip())
```

---

## Non-blocking with `selectors`

```python
import selectors, socket

sel = selectors.DefaultSelector()
s = socket.socket()
s.connect_ex(("example.com", 80))
s.setblocking(False)
sel.register(s, selectors.EVENT_WRITE)

while True:
    for key, events in sel.select(timeout=1):
        if events & selectors.EVENT_WRITE:
            s.send(b"GET / HTTP/1.0\r\nHost: example.com\r\n\r\n")
            sel.modify(s, selectors.EVENT_READ)
        elif events & selectors.EVENT_READ:
            data = s.recv(8192)
            if not data:
                sel.unregister(s); s.close(); break
            print(data)
    else:
        continue
    break
```

---

## TLS Client with `ssl`

```python
import socket, ssl

ctx = ssl.create_default_context()
with socket.create_connection(("example.com", 443)) as sock:
    with ctx.wrap_socket(sock, server_hostname="example.com") as ssock:
        ssock.sendall(b"HEAD / HTTP/1.1\r\nHost: example.com\r\n\r\n")
        print(ssock.recv(2048))
```

---

## Tips

- Set timeouts on sockets; default is blocking forever.
- Use `SO_REUSEADDR` for quick dev iterations on servers.
- Prefer `socket.create_connection` for clients; it handles DNS and timeouts.
- Wrap sockets with `makefile` + `TextIOWrapper` for line-based protocols.

