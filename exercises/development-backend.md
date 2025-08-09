16. Create a FastAPI route that returns “Hello World”.

```python
from fastapi import FastAPI

app = FastApi()

@app.get("/api/helloWorld")
def hello_world():
    return "hello world"

```


17. Define a Pydantic model with fields: `name`, `email`, `created_at`.

```python
from pydantic import BaseModel
from datetime import datetime

class User(BaseModel):
    name: str
    email: str
    created_at: datetime

```

18. Create a POST endpoint that receives a JSON body and returns it.

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/api/json")
def receive_json(data:dict):
    return data

```


19. Connect FastAPI to MongoDB using `pymongo`.

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


20. Insert a document into a MongoDB collection from an API route.
21. Create a GET route to retrieve a document by `ObjectId`.
22. Update a field in a document using a PUT route.
23. Create a DELETE endpoint to remove a document by ID.
24. Add pagination (limit + offset) to a GET endpoint.
25. Write a route that returns only documents where `status = "approved"`.
26. Write an endpoint that aggregates documents and returns the count per status.
27. Create an endpoint to upload a list of items at once (bulk insert).
28. Build a logging function that stores process logs in a `log` field (array).
29. Create a schema for a `pin` object with title, image_url, and match_score.
30. Secure an endpoint using an API key or token-based auth.