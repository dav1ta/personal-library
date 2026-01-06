# Security Basics in Python

Focus on safe primitives built into the standard library.

## Randomness and Tokens

```python
import secrets

token = secrets.token_urlsafe(32)  # for links, cookies, CSRF, etc.
key = secrets.token_bytes(32)      # 256-bit key material
secrets.compare_digest(a, b)       # constant-time equality
```

---

## Password Hashing

Use a KDF with salt and iterations. Prefer dedicated libs (argon2/bcrypt/scrypt). In the stdlib:

```python
import os, hashlib

salt = os.urandom(16)
dk = hashlib.pbkdf2_hmac("sha256", b"password", salt, 200_000, dklen=32)

# Python 3.6+: scrypt
dk2 = hashlib.scrypt(b"password", salt=salt, n=2**14, r=8, p=1, dklen=32)
```

Store: algorithm params + salt + derived key. Never store raw passwords.

---

## HMAC Signatures

```python
import hmac, hashlib

secret = b"shared-key"
msg = b"payload"
sig = hmac.new(secret, msg, hashlib.sha256).digest()

# verify
hmac.compare_digest(sig, hmac.new(secret, msg, hashlib.sha256).digest())
```

---

## TLS and Certificates (client)

```python
import ssl, socket

ctx = ssl.create_default_context()  # verifies certs by default

with socket.create_connection(("example.com", 443), timeout=5) as sock:
    with ctx.wrap_socket(sock, server_hostname="example.com") as ssock:
        ssock.sendall(b"GET / HTTP/1.1\r\nHost: example.com\r\n\r\n")
        print(ssock.recv(1024))
```

Avoid disabling verification; do not set `check_hostname=False` or `verify_mode=CERT_NONE` unless in tightly controlled test environments.

---

## Input Handling

- Never `eval` or `exec` untrusted input.
- Use parameterized SQL queries; avoid string interpolation.
- Validate and sanitize external input; whitelist over blacklist.

---

## Secrets Management

- Use environment variables and secret stores; avoid committing secrets.
- Rotate keys and enforce least privilege.

Next: [SQLite](sqlite.md)
