# fullstack ai app
AI-driven platform for fetching, evaluating, and displaying images that match user-provided visual prompts.

## deploy

> docker setup

```yml
version '3.8'

services:
    frontend:
        build: ./frontend
        ports:
            -   "3000:3000"
        depends_on:
            -   backend
        network:
            -   app-network
    backend:
        build: ./backend
        ports:
            -   "8000:8000"
        volumes:
            -   ./backend:/app
        environment:
            -   OPENAI_API_KEY=${OPENAI_API_KEY}
            -   MONGODB_URL=${MONGODB_URL}
        networks:
            -   app-network
        restart: unless-stopped
    
networks:
    app-network:
        driver: bridge
```

## backend

> initial commands
```
python3 -m venv venv
source venv/bin/activate
playwright install
pip install fastapi uvicorn pymongo openai playwright pydantic-ai pydantic
pip freeze &gt; requirements.txt
```

> docker setup

```docker
FROM python:3.11 as builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.11

RUN apt-get update && apt-get install -y --no-install-reccomends \
    ca-certificates \
    wget \
    playwright-deps

RUN playwright install chromium
RUN playwright install-deps-chromium

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```

> server

```py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(
    title="AI Pinterest Scrapper",
    version="1.0.0",
    description="AI Image curator"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=[""],
    allo_methods=["*"]
)

@app.get("/")
def root():
    return {"message": "api ok"}

from app.routes.main import router as main_router
app.include_router(main_router)

if __name__ == "__main__":
    import uvicorn
    uvicorn.run("app.main:app", host="0.0.0.0", port=8000, reload=True)


```

> api
```py
from fastapi import APIRouter, HTTPException, BackgroundTasks
from pydantic import BaseModel

router = APIRouter(
    prefix="/api",
    tags=["prompts"]
)

class PromptRequest(BaseModel):
    text: str

class PromptResponse(BaseModel):
    prompt_id: str
    status: str
    message: str

@router.post("/prompts", response_model=PromptResponse)
async def create_prompt(prompt_request: PromptRequest, background_tasks: BackgroundTasks):
    """
    Trigger workflow
    """
    try: 
        background_tasks.add_task(
            run_complete_workflow_background,
            prompt_request.text,
            str(prompt_id)
        )

        return PromptResponse(
            ...
            status:"peding"
        )
    except Except as e:
        raise HTTPException(
            status_code=500,
            detail=f"failed {str(e)}"
        )

```

> pydantic-ai

```py
from pydantic import BaseModel, Field
from pydantic_ai import Agent
from pydantic_ai.models.openai import OpenAIModel
from pydantic_ai.providers.openai import OpenAIProvider
from pydantic_ai.settings import ModelSettings 

class PinValidation(BaseModel):
    match_score: float
    status: str
    explanation: str

def evaluate(api_key: str, prompt_text: str, image_url:str):
    provider = OpenAIProvider(api_key=api_key)
    model = OpenAIModel("gpt-4o")

    model_settings = ModelSettings(temperature=0.7)
    agent = Agent(
        model=model,
        result_type=PinValidation,
        system_prompt="",
        model_settings=model_settings

    )

    evaluation_prompt=""
    messages = [evaluation_prompt, image_url]
    result = agent.run(messages)
    return result.data

```


## frontend

initial commands
```
npm create vite@latest client -- --template react-ts
npm, install tailwind
```

> docker setup
```
FROM node:18-alpine
WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . . 

RUN sed -i 's/"allowImportingTsExtensions": true 

npm install -g serve

EXPOSE 3000

CMD ["serve", "-s", "dist" , "-l", "3000"]

```

> api 

```js
import axios from 'axios'

export const baseUrl = 'http://localhost:1340/api/'

const api = axios.create({ baseUrl });

api.interceptors.request.use(config => {
    config.headers = config.headers || {};

    const userString = localStorage.getItem('user')

    if (userString) {
        try {
            const user = JSON.parse(userString)
            if (user.id) {
                config.headers['UserId'] = user.id;
            }
        } catch(error) {
            console.log("eerrror", error)
        }
    }
})

```

> app

```ts
import {useState, useEffect } from 'react'
import { ApiService } from '../services/api'

interface ImageReviewProps {
    promptId: string
}

export default function ImageReview({promptId }: ImageReviewProps) {
const [pins, setPins] = useState<Pin[]>([])
const [loading, setLoading] = useState(true)

useEffect(() => {
const fetchPins = async() =>{
    try {
        const response = await APIService.getPins(promptId)
        setPins(response.pins)
    }
    catch(error){
        console.log(error)
    } finally {
        setLoading(false)
    }
}

fetchPins()
}, [promptId])

return (
    <>
    <div className="text-2xl font-bold text-gray-900 dark:text-white">{pins.length}</div>
    </>
)


}

```