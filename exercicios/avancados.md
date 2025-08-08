# Fullstack AI Coding Challenge Practice

## 🧠 System Design, Architecture & Engineering Logic

> Describe how you would structure a multi-phase agent pipeline (warm-up → scraping → validation).

I would setup with a distinct service involving login, scrapping and browser automation on the given platform. Another for ai, choosing a framework with structure output. All of this would be deployed initally in a monorepo for backend, but with a option to use lambda aws functions for serverless computing. 

> What are the advantages of using FastAPI for building agent-driven backends?

Fast api is a kitchen-sink type framework. In its core is a aggregation of multiple python libraries like pydantic, uvicorn and starlette that ensures a high performace and scalable server.

> How would you make the pipeline resilient in case one stage fails?

The key to make the pipeline fail-safe is to have a modularized architecture. That way the main workflow can be called as a background async task. Across each stage it would be a function that saves the status remotly. Also, each step would have a fallback function. Thay way both user and developer can monitor the process effectively. 

> How would you separate the system into components or services, and why?

I would use the single reposability principle. The services would have a main class with common initializers. Each subsequente step would extend its class.
Then the mains workflow would trigger all of them 

> What are the tradeoffs between using WebSockets and polling for real-time feedback?
Websockers ensures real time communication with server and client. Pooling relies on multiple requests on a given interval and can hit api throtle limits.


> How would you version API endpoints and why is it important?
The common way to to this is to add a vX prefix for each endpoint. this is crucial to maintan backward compatibility and highlight different iterations of the APi. also you can configure in middleware rules according to each type to facilitate transitions or change endpoint behaviour

> How would you prevent race conditions when multiple agents run at the same time?

Fast api provides some solutions to handdle this because it manages async operations. However, the ideal approach here is using message queues technologies like Redis or RabbitMQ

> Describe how you would scale this system to handle thousands of prompts per hour.

The key for this is having load balancing across multple server isntances. Its also essential caching frequented computed values on the server on redis or other memomory management service, while mantaining the application stateless concering user data (each request has all the info it needs) 

> How would you structure logs and metadata to help with observability?

Implement structlogs for consistent JSON logs combined with Prometheus and Grafana for visualizaiton.

> How would you design the system to support multiple users and prompt histories?

For supporting multiple users and prompt histories with FastAPI, implement JWT-based authentication with OAuth2PasswordBearer for secure user identification, use Pydantic models for structured data validation, and store user data and prompt histories in MongoDB collections with proper indexing. 

> What steps would you take to limit abuse of your scraping and validation endpoints?

To limit abuse of endpoints, implement rate limiting with the FastAPI-Limiter library (Redis-backed), add API key authentication for sensitive endpoints, implement request validation with Pydantic, use IP-based throttling, and add CAPTCHA verification for public endpoints. Both approaches leverage FastAPI's dependency injection system for clean implementation.


## 🧮 Programming Basics & Data Structures (Python + JavaScript)

1. Write a Python function that reverses a string.

```python
my_string = "hello"
my_array = list(my_string)
array_length = len(my_array)
inverted_array = []

for i in range(my_array):
    inverted_array.append(my_array[array_length - 1 -i])

inverted_string = ''.join(inverted_array)


## ou

my_string="hello"
inverted_string = my_string[::-1]

```

2. Write a JavaScript function that checks if a number is even.

```ts
function is_even(number: number):string {
    const condition = number % 2 === 0
    const status = condition ? "even" : "odd"
    return status
}
```

3. Implement a Python function that returns the maximum number in a list.

```python
number_list [42, 30, 4, 100]
max_value = number_list[0]
for i in range(1, len(number_list))
    if number_list[i] > max_value:
        max_value = number_list[i]


# or

number_list [42, 30, 4, 100]
max_value = max(number_list)
print(max_value)

```

4. Implement a JS function that filters an array of numbers greater than 10.
5. Write a Python function that counts the vowels in a string.
6. Write a JS function that removes duplicates from an array.
7. Write a Python class representing a `User` with `name` and `email`.
8. Implement a simple linked list in Python or JS.
9.  Write a function that returns the intersection of two arrays.
10. Implement a recursive function in Python that calculates factorial.
11. Implement a binary search function in JS.
12. Write a function that checks if a string is a palindrome.
13. Implement a simple LRU cache in Python.
14. Given a list of numbers, return a sorted version (no built-ins).
15. Solve the two-sum problem in both Python and JS.

---

## ⚙️ Backend Development (FastAPI + MongoDB)

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

---

## 🧑‍💻 Frontend Development (React + TypeScript)

31. Create a React component that renders a button with a click counter.

```js
import { useState } from "react"

export default function App(){
const [count, setCount] = useState(0)


function incrementValue() {
    const newValue = count + 1
    setCount(newValue)
}

return (
    <>
    <div> {count} clicks</div>
    <button onClick={incrementValue}>Click here! </button>
    </>
)

}

```

32. Build a form with controlled inputs for name and email.
33. Fetch data from an API and render it as a list of cards.

```js
import { useEffect, useState } from "react"
import axios from "axios"

export default function App() {

const [data, setData] = useState([])

useEffect(()=>{

async function getData() {
    const response = await axios.get("http://localhost:8000/api/cards")
    setData(response.data)
}

getData()
},[])

function renderCards(){
    if (data) {
    return data.map((element, index) =>  (
        <div key={index}>{element.label}</div>
    ))

    } else {
        return <div>No data</div>
    }
}

return (
    <> 
    
    {renderCards()}

    </>
)

}
```


34. Show a loading spinner while fetching data from an endpoint.
35. Add a type to a `User` object with fields `name`, `email`, `id`.
36. Create a dropdown menu to filter items by status.
37. Build a progress bar that updates based on polling a backend.
38. Make a POST request from React to start a background job.
39. Add error handling to an API call and display error messages.
40. Build a React hook that fetches and returns data from any URL.
41. Create a component that shows image thumbnails from a list.
42. Build a modal that shows image metadata when clicked.
43. Add sorting by match score to a gallery view.
44. Create tabs to toggle between Approved / Disqualified / All items.
45. Implement infinite scroll or "Load More" button with pagination.

---

## 🤖 AI + Agentic System Integration

46. Write a function that sends a prompt to OpenAI API and logs the response.




47. Create a fake image validator function that returns match_score and explanation.
48. Design a schema for a validation result using MongoDB.
49. Simulate an agent pipeline: warm-up → scrape → validate → store.
50. Create a status tracker that logs each phase of the agent's execution.
51. Decide which AI model to use for image–text similarity and justify the choice.
52. Simulate scraping Pinterest with Playwright or Puppeteer (pseudo-code).
53. Implement retries and exponential backoff for failed agent steps.
54. Build a system that queues agent tasks for execution (e.g. asyncio or Celery).
55. Create an endpoint to re-validate all pins from a previous prompt.
56. Implement real-time feedback (WebSocket or long polling) for agent progress.
57. Design a way to parallelize validation of multiple images.
58. Discuss how you would add a fine-tuned image classifier to replace OpenAI.

---

## 🚀 Deployment, DevOps & Scaling

59. Write a Dockerfile for a FastAPI app and describe each line.
60. Explain how to deploy this app to AWS using EC2 or Elastic Beanstalk.
61. Describe how you would set up a CI pipeline for linting + tests + deploy.
62. Explain how to scale scraping agents horizontally (workers, queues).
63. Describe how to secure secrets (API keys, DB URIs) in production.
64. Discuss trade-offs of using MongoDB Atlas vs. local MongoDB.
65. Describe how you would monitor the system and get alerted on failures.
