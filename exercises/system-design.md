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