# Networking and Data

## net/http Client
```go
client := &http.Client{Timeout: 5 * time.Second}
resp, err := client.Get("https://example.com")
if err != nil {
    return err
}
defer resp.Body.Close()
```

## net/http Server
```go
http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("ok"))
})
_ = http.ListenAndServe(":8080", nil)
```

## net and crypto
```go
ln, _ := net.Listen("tcp", ":9000")
_ = ln

buf := make([]byte, 32)
_, _ = rand.Read(buf) // crypto/rand
```

## database/sql
```go
db, err := sql.Open("postgres", dsn)
if err != nil {
    return err
}
defer db.Close()

row := db.QueryRow("select id, name from users where id=$1", id)
```
