# Basic vs. Advanced WebSocket Implementations

Below are two contrasting approaches for handling WebSocket connections in Python: a **basic in-memory** approach and a more **advanced Redis-based** approach for scalability.

---

## 1. Basic Implementation (Single-Instance, In-Memory)

**Key Points**  
- Stores WebSocket connections in a Python set or dict.  
- Fine for small-scale or prototype scenarios.  
- Not resilient: if the server restarts, all connections are lost.  
- Difficult to scale: you can’t easily share in-memory state across multiple server instances.

**Code Example**

```python
from sanic import Sanic, Request, Websocket
from sanic.exceptions import WebSocketClosed

app = Sanic("WebSocketBasic")

connected_clients = set()

@app.websocket("/feed")
async def feed(request: Request, ws: Websocket):
    connected_clients.add(ws)
    print("WebSocket connection established")

    try:
        async for message in ws:
            print(f"Received message: {message}")
            # Broadcast to all *other* clients
            for client in list(connected_clients):
                if client != ws:
                    try:
                        await client.send(f"Broadcast: {message}")
                    except Exception as e:
                        print(f"Error sending to client: {e}")
    except WebSocketClosed:
        print("WebSocket connection closed")
    finally:
        connected_clients.remove(ws)
        print("Connection cleanup completed")

@app.route("/")
async def index(request):
    return {"message": "Basic single-instance WebSocket server is running."}

if __name__ == "__main__":
    # This setup is not designed for multiple processes or servers.
    app.run(host="0.0.0.0", port=8000, workers=1)
```

---

## 2. Advanced Implementation (Multi-Instance, Redis Pub/Sub)

**Key Points**  
- Each server instance stores only *its* local WebSocket connections.  
- A Redis Pub/Sub channel is used to broadcast messages across instances.  
- This allows horizontal scaling: multiple workers or servers can handle clients concurrently.  
- If one server goes down, it only affects the clients connected to that instance.

**Code Example**

```python
import uuid
import asyncio
import aioredis
from sanic import Sanic, Request, Websocket
from sanic.exceptions import WebSocketClosed

app = Sanic("DistributedWebSocketApp")

REDIS_URL = "redis://localhost:6379"
REDIS_CHANNEL = "ws_broadcast"

# Store local active WebSocket connections: { client_id: Websocket }
local_ws_connections = {}

@app.listener("before_server_start")
async def setup_redis(app, loop):
    """
    Create Redis pool and start a background task to listen for messages.
    """
    app.ctx.redis = await aioredis.create_redis_pool(REDIS_URL)
    app.ctx.pubsub = app.ctx.redis.pubsub()
    await app.ctx.pubsub.subscribe(REDIS_CHANNEL)

    async def redis_listener():
        """Listens on REDIS_CHANNEL for messages to broadcast locally."""
        while True:
            try:
                message = await app.ctx.pubsub.get_message(
                    ignore_subscribe_messages=True, 
                    timeout=1.0
                )
                if message:
                    # message['data'] is bytes; convert to string
                    data = message["data"].decode()
                    # Broadcast to all local connections
                    for ws in list(local_ws_connections.values()):
                        try:
                            await ws.send(f"Redis broadcast -> {data}")
                        except Exception:
                            pass
            except asyncio.CancelledError:
                break
            except Exception as e:
                app.logger.error(f"Redis listener error: {e}")

    # Schedule Redis subscriber listening in the background
    app.add_task(redis_listener())

@app.listener("after_server_stop")
async def close_redis(app, loop):
    """Cleanly close Redis connections."""
    await app.ctx.pubsub.unsubscribe(REDIS_CHANNEL)
    app.ctx.pubsub.close()
    app.ctx.redis.close()
    await app.ctx.redis.wait_closed()

@app.websocket("/ws")
async def handle_websocket(request: Request, ws: Websocket):
    client_id = str(uuid.uuid4())
    local_ws_connections[client_id] = ws
    app.logger.info(f"Client connected: {client_id}")

    try:
        async for msg in ws:
            app.logger.info(f"Received from {client_id}: {msg}")
            # Publish incoming message to Redis, so all instances see it
            await app.ctx.redis.publish(
                REDIS_CHANNEL, 
                f"{client_id} says: {msg}"
            )
    except WebSocketClosed:
        app.logger.info(f"WebSocket closed: {client_id}")
    except Exception as e:
        app.logger.error(f"Error in websocket: {e}")
    finally:
        # Remove from local connections
        local_ws_connections.pop(client_id, None)
        app.logger.info(f"Client disconnected: {client_id}")

@app.route("/")
async def index(request):
    return {"message": "Advanced WebSocket server instance with Redis Pub/Sub."}

if __name__ == "__main__":
    # Launch multiple instances on different ports as needed, 
    # all connecting to the same Redis instance.
    #   python websocket_server.py --port=8001
    #   python websocket_server.py --port=8002
    app.run(host="0.0.0.0", port=8001, workers=1)
```

---

## Wrap-Up

- **Basic**: 
  - Easiest to implement, but limited to a single process. 
  - Fine for quick demos, small usage, or internal tools.

- **Advanced** (Redis Pub/Sub): 
  - Allows you to scale out by running multiple server instances. 
  - A common, production-friendly approach for real-time apps that need more than a single machine. 
  - Fault-tolerant: a crash on one instance doesn’t kill the entire system.




# Collection of System Design Examples

Below are various system design examples with code snippets. Each section is self-contained and demonstrates a distinct pattern or approach.

---

### Example 1: Rate Limiter with Redis

**Scenario**: Limit API calls per user within a time frame.

```python
import time
import redis

r = redis.StrictRedis(host='localhost', port=6379, db=0)

RATE_LIMIT = 10  # max requests
TIME_WINDOW = 60  # seconds

def is_rate_limited(user_id):
    key = f"rate_limit:{user_id}"
    current = r.get(key)

    if current is None:
        pipe = r.pipeline()
        pipe.set(key, 1, ex=TIME_WINDOW)
        pipe.execute()
        return False
    elif int(current) < RATE_LIMIT:
        r.incr(key)
        return False
    else:
        return True

# Usage
user_id = "user123"
if is_rate_limited(user_id):
    print("Rate limit exceeded. Try later.")
else:
    print("Request allowed.")
```

---

### Example 2: Asynchronous Task Processing with Celery

**Scenario**: Offload tasks to background workers.

**celery_app.py**:
```python
from celery import Celery

app = Celery('tasks', broker='pyamqp://guest@localhost//')

@app.task
def long_running_task(x, y):
    import time
    time.sleep(5)
    return x + y
```

**producer.py**:
```python
from celery_app import long_running_task

result = long_running_task.delay(10, 20)
print("Task sent. Waiting for result...")
print("Result:", result.get(timeout=10))
```

---

### Example 3: Circuit Breaker Pattern

**Scenario**: Prevent repeated calls to a failing service.

```python
import time
import requests

class CircuitBreaker:
    def __init__(self, failure_threshold=3, recovery_timeout=30):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.failure_count = 0
        self.last_failure_time = None
        self.open = False

    def call(self, func, *args, **kwargs):
        if self.open:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                self.open = False
            else:
                raise Exception("Circuit is open. Call blocked.")

        try:
            result = func(*args, **kwargs)
            self.failure_count = 0
            return result
        except Exception as e:
            self.failure_count += 1
            self.last_failure_time = time.time()
            if self.failure_count >= self.failure_threshold:
                self.open = True
            raise e

breaker = CircuitBreaker()

def unreliable_service():
    response = requests.get("http://example.com/api")
    response.raise_for_status()
    return response.json()

try:
    result = breaker.call(unreliable_service)
    print("Service call succeeded:", result)
except Exception as ex:
    print("Service call failed or circuit open:", ex)
```

---

### Example 4: Distributed Caching with Redis

**Scenario**: Cache expensive database queries.

```python
import redis
import time
import hashlib

r = redis.StrictRedis(host='localhost', port=6379, db=0)

def get_data_from_db(query):
    time.sleep(2)
    return f"Results for {query}"

def cached_query(query, ttl=60):
    key = f"cache:{hashlib.sha256(query.encode()).hexdigest()}"
    result = r.get(key)
    if result:
        return result.decode()
    result = get_data_from_db(query)
    r.setex(key, ttl, result)
    return result

query = "SELECT * FROM users WHERE id = 1"
print(cached_query(query))
```

---

### Example 5: OAuth2 with Flask and Authlib

**Scenario**: OAuth2 Authorization Code Flow setup.

```python
from flask import Flask, request, jsonify
from authlib.integrations.flask_oauth2 import AuthorizationServer
from authlib.oauth2.rfc6749 import grants

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret'
authorization = AuthorizationServer(app)

clients = {}
tokens = {}

class AuthorizationCodeGrant(grants.AuthorizationCodeGrant):
    def save_authorization_code(self, code, request):
        clients[code] = request.client_id

    def query_authorization_code(self, code, client):
        if clients.get(code) == client.client_id:
            return code

    def delete_authorization_code(self, authorization_code):
        clients.pop(authorization_code, None)

    def authenticate_user(self, authorization_code):
        return {'user_id': '123'}

authorization.register_grant(AuthorizationCodeGrant)

@app.route('/oauth/authorize', methods=['GET', 'POST'])
def authorize():
    if request.method == 'GET':
        return '<form method="post"><button type="submit">Authorize</button></form>'
    grant_user = {'user_id': '123'}
    return authorization.create_authorization_response(grant_user=grant_user)

@app.route('/oauth/token', methods=['POST'])
def issue_token():
    return authorization.create_token_response()

if __name__ == "__main__":
    app.run(debug=True)
```

---

### Example 6: Webhook Handler

**Scenario**: Receive and process incoming webhook events.

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/webhook', methods=['POST'])
def webhook():
    data = request.json
    print("Received webhook:", data)
    return jsonify({"status": "received"}), 200

if __name__ == "__main__":
    app.run(port=5000)
```

---

### Example 7: File Upload Service with Flask

**Scenario**: Accept file uploads and save them.

```python
from flask import Flask, request, jsonify
import os

app = Flask(__name__)
UPLOAD_FOLDER = './uploads'
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

@app.route('/upload', methods=['POST'])
def upload_file():
    if 'file' not in request.files:
        return jsonify({"error": "No file provided"}), 400
    file = request.files['file']
    file.save(os.path.join(UPLOAD_FOLDER, file.filename))
    return jsonify({"status": "success", "filename": file.filename})

if __name__ == "__main__":
    app.run(port=5000)
```

---

### Example 8: Chat Server with Flask-SocketIO

**Scenario**: Simple real-time chat using WebSockets.

```python
from flask import Flask
from flask_socketio import SocketIO, emit

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret!'
socketio = SocketIO(app)

@app.route('/')
def index():
    return "WebSocket Chat Server Running"

@socketio.on('message')
def handle_message(msg):
    print('Received message:', msg)
    emit('message', msg, broadcast=True)

if __name__ == '__main__':
    socketio.run(app, port=5000)
```

---

### Example 9: RESTful API with FastAPI

**Scenario**: Create a simple RESTful endpoint.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
  
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

---

### Example 10: gRPC Communication in Python

**Scenario**: Set up a basic gRPC server and client.

**`example.proto`**:
```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

**Generate Python code**:
```bash
python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. example.proto
```

**Server (server.py)**:
```python
from concurrent import futures
import grpc
import time
import example_pb2
import example_pb2_grpc

class Greeter(example_pb2_grpc.GreeterServicer):
    def SayHello(self, request, context):
        return example_pb2.HelloReply(message='Hello, ' + request.name)

server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
example_pb2_grpc.add_GreeterServicer_to_server(Greeter(), server)
server.add_insecure_port('[::]:50051')
server.start()

try:
    while True:
        time.sleep(86400)
except KeyboardInterrupt:
    server.stop(0)
```

**Client (client.py)**:
```python
import grpc
import example_pb2
import example_pb2_grpc

channel = grpc.insecure_channel('localhost:50051')
stub = example_pb2_grpc.GreeterStub(channel)
response = stub.SayHello(example_pb2.HelloRequest(name='World'))
print("Greeter client received: " + response.message)
```

---

This consolidated Markdown file presents various system design examples with code. Each snippet is encapsulated and ready for use. Further topics can be added similarly as needed.




# Collection of System Design Examples (Continued)

Below are 10 more diverse system design examples with code snippets, compiled together.

---

### Example 11: Event-Driven Architecture with Kafka

**Scenario**: Produce and consume messages using Apache Kafka.

**Prerequisites**: Install `kafka-python` (`pip install kafka-python`) and have a Kafka cluster running.

**Producer Example**
```python
from kafka import KafkaProducer
producer = KafkaProducer(bootstrap_servers='localhost:9092')
for i in range(5):
    producer.send('my-topic', b'Sample message %d' % i)
producer.flush()
```

**Consumer Example**
```python
from kafka import KafkaConsumer
consumer = KafkaConsumer('my-topic', 
                         bootstrap_servers='localhost:9092', 
                         auto_offset_reset='earliest')
for message in consumer:
    print(f"Received: {message.value.decode()}")
```

---

### Example 12: Simple Event Sourcing Mechanism

**Scenario**: Record changes as events and rebuild state by replaying them.

```python
import json

# Event Store (in-memory for demo)
event_store = []

def record_event(event_type, data):
    event = {"type": event_type, "data": data}
    event_store.append(event)

def replay_events():
    state = {}
    for event in event_store:
        if event["type"] == "user_created":
            state[event["data"]["id"]] = event["data"]
        elif event["type"] == "user_updated":
            state[event["data"]["id"]].update(event["data"])
    return state

# Usage
record_event("user_created", {"id": 1, "name": "Alice"})
record_event("user_updated", {"id": 1, "email": "alice@example.com"})
print(replay_events())
```

---

### Example 13: Simple Notification System with WebSockets

**Scenario**: Notify connected clients in real-time using Flask-SocketIO.

```python
from flask import Flask
from flask_socketio import SocketIO, emit

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret!'
socketio = SocketIO(app)

@app.route('/')
def index():
    return "Notification Server Running"

@app.route('/notify', methods=['POST'])
def notify():
    message = "Notification message"  # Typically extracted from request data
    socketio.emit('notification', {'data': message})
    return "Notification sent!"

if __name__ == '__main__':
    socketio.run(app, port=5000)
```

---

### Example 14: Simple URL Shortener

**Scenario**: Create short URLs mapping to original URLs.

```python
from flask import Flask, request, redirect, jsonify
import hashlib

app = Flask(__name__)
url_mapping = {}

def shorten_url(original_url):
    short_hash = hashlib.sha256(original_url.encode()).hexdigest()[:6]
    url_mapping[short_hash] = original_url
    return short_hash

@app.route('/shorten', methods=['POST'])
def create_short_url():
    data = request.json
    original_url = data.get('url')
    short_url = shorten_url(original_url)
    return jsonify({"short_url": request.host_url + short_url})

@app.route('/<short_url>')
def redirect_url(short_url):
    original_url = url_mapping.get(short_url)
    if original_url:
        return redirect(original_url)
    return "URL not found", 404

if __name__ == "__main__":
    app.run(port=5000)
```

---

### Example 15: Data Warehouse ETL Pipeline (Simplified)

**Scenario**: Extract, Transform, and Load data from a source to a target.

```python
import csv
import sqlite3

# Extract: read CSV file
def extract_data(csv_file):
    with open(csv_file, newline='') as f:
        reader = csv.DictReader(f)
        return list(reader)

# Transform: simple transformation (e.g., converting strings to integers)
def transform_data(data):
    for row in data:
        row['age'] = int(row['age'])
    return data

# Load: insert data into SQLite database
def load_data(data, db_file):
    conn = sqlite3.connect(db_file)
    cursor = conn.cursor()
    cursor.execute('CREATE TABLE IF NOT EXISTS users (id INTEGER, name TEXT, age INTEGER)')
    for row in data:
        cursor.execute('INSERT INTO users VALUES (?, ?, ?)', (row['id'], row['name'], row['age']))
    conn.commit()
    conn.close()

# Running the ETL process
data = extract_data('users.csv')
data = transform_data(data)
load_data(data, 'users.db')
```

---

### Example 16: Distributed Lock with Redis

**Scenario**: Use Redis to implement a simple distributed lock.

```python
import redis
import time

r = redis.StrictRedis(host='localhost', port=6379, db=0)

def acquire_lock(lock_name, timeout=10):
    lock = r.lock(lock_name, timeout=timeout)
    acquired = lock.acquire(blocking=True)
    return lock if acquired else None

def release_lock(lock):
    lock.release()

# Usage
lock = acquire_lock('my_lock')
if lock:
    try:
        print("Lock acquired, processing critical section.")
        # Critical section code
        time.sleep(2)
    finally:
        release_lock(lock)
        print("Lock released.")
else:
    print("Failed to acquire lock.")
```

---

### Example 17: Simple File Storage Service (S3-like)

**Scenario**: Upload and download files using Flask.

```python
from flask import Flask, request, send_from_directory, jsonify
import os

app = Flask(__name__)
UPLOAD_FOLDER = './files'
os.makedirs(UPLOAD_FOLDER, exist_ok=True)

@app.route('/upload', methods=['POST'])
def upload():
    file = request.files['file']
    file.save(os.path.join(UPLOAD_FOLDER, file.filename))
    return jsonify({"message": "File uploaded", "filename": file.filename})

@app.route('/download/<filename>', methods=['GET'])
def download(filename):
    return send_from_directory(UPLOAD_FOLDER, filename)

if __name__ == "__main__":
    app.run(port=5000)
```

---

### Example 18: Implementing gRPC Streaming (Server-Side)

**Scenario**: Stream responses from a gRPC server.

**streaming.proto:**
```proto
syntax = "proto3";

service Streamer {
  rpc StreamData(Empty) returns (stream DataChunk) {}
}

message Empty {}

message DataChunk {
  string content = 1;
}
```

Generate Python code:
```bash
python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. streaming.proto
```

**Server (stream_server.py):**
```python
import time
from concurrent import futures
import grpc
import streaming_pb2
import streaming_pb2_grpc

class StreamerServicer(streaming_pb2_grpc.StreamerServicer):
    def StreamData(self, request, context):
        for i in range(5):
            yield streaming_pb2.DataChunk(content=f"Chunk {i}")
            time.sleep(1)

server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
streaming_pb2_grpc.add_StreamerServicer_to_server(StreamerServicer(), server)
server.add_insecure_port('[::]:50052')
server.start()
server.wait_for_termination()
```

**Client (stream_client.py):**
```python
import grpc
import streaming_pb2
import streaming_pb2_grpc

channel = grpc.insecure_channel('localhost:50052')
stub = streaming_pb2_grpc.StreamerStub(channel)
for chunk in stub.StreamData(streaming_pb2.Empty()):
    print("Received:", chunk.content)
```

---

### Example 19: Implementing Feature Flags

**Scenario**: Toggle features on/off dynamically.

```python
# Simple in-memory feature flag system
feature_flags = {
    "new_dashboard": False,
    "beta_feature": True
}

def is_feature_enabled(feature_name):
    return feature_flags.get(feature_name, False)

# Usage
if is_feature_enabled("new_dashboard"):
    print("Render new dashboard")
else:
    print("Render old dashboard")
```

---

### Example 20: Implementing a Distributed Lock Service

**Scenario**: A more robust distributed lock using Redlock algorithm with Redis.

```python
import redis
import uuid
import time

class Redlock:
    def __init__(self, redis_client, lock_key, ttl=10000):
        self.redis = redis_client
        self.lock_key = lock_key
        self.ttl = ttl
        self.lock_value = str(uuid.uuid4())

    def acquire(self):
        result = self.redis.set(self.lock_key, self.lock_value, nx=True, px=self.ttl)
        return result

    def release(self):
        # Lua script for safe delete
        script = """
        if redis.call("get",KEYS[1]) == ARGV[1] then
            return redis.call("del",KEYS[1])
        else
            return 0
        end
        """
        self.redis.eval(script, 1, self.lock_key, self.lock_value)

r = redis.StrictRedis(host='localhost', port=6379, db=0)
lock = Redlock(r, "resource_lock")
if lock.acquire():
    try:
        print("Lock acquired, processing resource.")
        # Critical resource processing
        time.sleep(2)
    finally:
        lock.release()
        print("Lock released.")
else:
    print("Could not acquire lock.")
```



```
### Example 21: Cache Eviction Strategy Implementation

**Scenario**: Implement an LRU (Least Recently Used) cache in Python.

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity

    def get(self, key):
        if key not in self.cache:
            return -1
        # Move key to end to mark as recently used
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key, value):
        if key in self.cache:
            # Update existing key and mark as recently used
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            # Pop the first (least recently used) item
            self.cache.popitem(last=False)

# Usage
cache = LRUCache(2)
cache.put(1, 'A')
cache.put(2, 'B')
print(cache.get(1))  # 'A'
cache.put(3, 'C')    # Evicts key 2
print(cache.get(2))  # -1 (not found)
```

---

### Example 22: Microservice Communication via gRPC with Load Balancing

**Scenario**: Implement client-side load balancing among multiple service instances using gRPC.

**Setup**: Assume multiple Greeter services running on different ports.

```python
import grpc
import random
import example_pb2
import example_pb2_grpc

# List of available server addresses
server_addresses = ['localhost:50051', 'localhost:50052', 'localhost:50053']

def get_stub():
    # Randomly pick a server for each request (simple load balancing)
    channel = grpc.insecure_channel(random.choice(server_addresses))
    return example_pb2_grpc.GreeterStub(channel)

stub = get_stub()
response = stub.SayHello(example_pb2.HelloRequest(name='LoadBalancedClient'))
print("Response:", response.message)
```

---

### Example 23: Actor Model with Pykka

**Scenario**: Use the actor model to manage concurrent state.

**Prerequisites**: Install `Pykka` (`pip install pykka`).

```python
import pykka

class CounterActor(pykka.ThreadingActor):
    def __init__(self):
        super().__init__()
        self.count = 0

    def on_receive(self, message):
        if message.get('cmd') == 'increment':
            self.count += 1
            return self.count
        elif message.get('cmd') == 'get':
            return self.count

# Start an actor
counter = CounterActor.start()

# Interact with the actor
future = counter.ask({'cmd': 'increment'})
print("Count after increment:", future)

current = counter.ask({'cmd': 'get'})
print("Current count:", current)

counter.stop()
```

---

### Example 24: Service Discovery with Consul

**Scenario**: Register and discover services using Consul's HTTP API.

**Prerequisites**: Consul agent running locally.

```python
import requests
import time

CONSUL_ADDRESS = 'http://localhost:8500'

def register_service(name, service_id, address, port):
    url = f"{CONSUL_ADDRESS}/v1/agent/service/register"
    payload = {
        "Name": name,
        "ID": service_id,
        "Address": address,
        "Port": port,
        "Check": {
            "HTTP": f"http://{address}:{port}/health",
            "Interval": "10s"
        }
    }
    response = requests.put(url, json=payload)
    print("Service registration:", response.status_code)

def discover_service(name):
    url = f"{CONSUL_ADDRESS}/v1/catalog/service/{name}"
    response = requests.get(url)
    services = response.json()
    return services

# Register a sample service
register_service("my-service", "my-service-1", "127.0.0.1", 5000)
time.sleep(2)  # Wait for registration

# Discover the registered service
services = discover_service("my-service")
print("Discovered services:", services)
```

---

### Example 25: Distributed Task Queue with Redis and RQ

**Scenario**: Use Redis Queue (RQ) for simple background task processing.

**Prerequisites**: Install `rq` (`pip install rq`) and run a Redis server.

**tasks.py**:
```python
import time

def background_task(x, y):
    time.sleep(5)  # Simulate long computation
    return x + y
```

**enqueue_task.py**:
```python
from redis import Redis
from rq import Queue
from tasks import background_task

# Connect to Redis server
redis_conn = Redis()
q = Queue(connection=redis_conn)

# Enqueue a task
job = q.enqueue(background_task, 10, 20)
print(f"Enqueued job: {job.id}")

# Wait for the job to finish
result = job.result or job.wait(timeout=10)
print("Task result:", result)
```

To process tasks, run an RQ worker in another terminal:
```bash
rq worker
```
```
