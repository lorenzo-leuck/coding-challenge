> Create a FastAPI route that returns “Hello World”.

```python
from fastapi import FastAPI

app = FastApi()

@app.get("/api/helloWorld")
def hello_world():
    return "hello world"

```


>  Define a Pydantic model with fields: `name`, `email`, `created_at`.

```python
from pydantic import BaseModel
from datetime import datetime

class User(BaseModel):
    name: str
    email: str
    created_at: datetime

```

> Create a POST endpoint that receives a JSON body and returns it.

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/api/json")
def receive_json(data:dict):
    return data

```


> Connect FastAPI to MongoDB using `pymongo`.

```python
from fastapi import FastAPI
from pymongo import MongoClient
import os

app = FastAPI()

MONGO_URL = os.getenv("MONGO_URL", "url")
client = MongoClient(MONGODB_URL)
database = client["mydatabase"]

@app.get("/mongodb-test")
def test_mongodb_connection():
    try:
        client.admin.command("ismaster")
        return {"message": "mongodb success"}
    except catch Exception as e:
        return {"error": str(e)}

```


> Insert a document into a MongoDB collection from an API route.

> Create a GET route to retrieve a document by `ObjectId`.

> Update a field in a document using a PUT route.

> Create a DELETE endpoint to remove a document by ID.

> Add pagination (limit + offset) to a GET endpoint.

> Write a route that returns only documents where `status = "approved"`.

> Write an endpoint that aggregates documents and returns the count per status.

> Create an endpoint to upload a list of items at once (bulk insert).

> Build a logging function that stores process logs in a `log` field (array).

> Create a schema for a `pin` object with title, image_url, and match_score.

> Secure an endpoint using an API key or token-based auth.
