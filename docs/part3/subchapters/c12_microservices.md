---
title: Microservices with FastAPI
parent: Applying Advanced Concepts 
nav_order: 3
---

## Chapter 12: Microservices with FastAPI

*   **12.1 Introduction to Microservices:**

    Microservices have emerged as a dominant architectural style for building modern, scalable, and resilient applications. In contrast to monolithic applications, which are built as a single, tightly coupled unit, microservices are composed of small, independent services that communicate with each other over a network.

    **Benefits of Microservices:**

    *   **Scalability:** Each microservice can be scaled independently, allowing you to allocate resources where they are needed most.
    *   **Independent Deployment:** Microservices can be deployed and updated independently, without affecting other parts of the system. This allows for faster release cycles and reduced risk.
    *   **Fault Isolation:** If one microservice fails, it does not necessarily bring down the entire application. Other microservices can continue to operate normally.
    *   **Technology Diversity:** Microservices allow you to use different technologies and programming languages for different parts of the system, allowing you to choose the best tool for each job.

    **Challenges of Microservices:**

    *   **Distributed Systems Complexities:** Microservices introduce the complexities of distributed systems, such as network latency, fault tolerance, and data consistency.
    *   **Increased Operational Overhead:** Managing a large number of microservices can be more complex than managing a single monolithic application.
    *   **Monitoring and Logging Challenges:** Due to their distributed nature it is hard to monitor and implement logging to identify if all microservices are functioning as intended.

    **When to Use Microservices (and When Not To):**

    Microservices are a good choice for complex applications that require high scalability, independent deployment, and fault isolation. However, they may not be the best choice for small, simple applications where the overhead of managing a distributed system outweighs the benefits. As a general rule, microservices should be considered only when a team is very confident in their abilities and can measure an increase in performance.

*   **12.2 FastAPI: A Modern, Fast (high-performance), Web Framework for building APIs:**

    FastAPI has rapidly gained popularity as a modern and efficient framework for building APIs in Python. It offers several compelling advantages over traditional web frameworks:

    *   **Performance:** FastAPI is built on top of Starlette and Pydantic, which provide excellent performance. It's one of the fastest Python web frameworks available.
    *   **Automatic Data Validation:** FastAPI uses Pydantic models for request and response body validation, ensuring that your API endpoints receive and return data in the correct format.
    *   **API Documentation (Swagger UI):** FastAPI automatically generates interactive API documentation using Swagger UI and ReDoc, making it easy for developers to explore and test your APIs.
    *   **Dependency Injection:** FastAPI's dependency injection system makes it easy to manage dependencies and write testable code.
    *   **Support for async code:** Designed to use async from the ground up.

    **Setting up a FastAPI Project:**

    1.  **Install Dependencies:**

        ```bash
        pip install fastapi uvicorn
        ```

        You may also want to install other dependencies, such as:

        *   `pydantic`: For data validation
        *   `uvicorn`: An ASGI server for running FastAPI applications
        *   `python-multipart`: For handling file uploads
        *   `httpx`: For making HTTP requests in tests

    2.  **Create a Basic Application:**

        Create a file named `main.py` with the following content:

        ```python
        from fastapi import FastAPI

        app = FastAPI()

        @app.get("/")
        async def read_root():
            return {"Hello": "World"}
        ```

    3.  **Run the Application:**

        ```bash
        uvicorn main:app --reload
        ```

        This will start the FastAPI application on `http://127.0.0.1:8000`. The `--reload` flag enables automatic reloading, which means that the server will automatically restart whenever you make changes to your code.

    **Defining API Endpoints:**

    You can define API endpoints using decorators such as `@app.get`, `@app.post`, `@app.put`, `@app.delete`, and `@app.patch`. The decorator specifies the HTTP method and the path for the endpoint.

    Here's an example:

    ```python
    from fastapi import FastAPI

    app = FastAPI()

    @app.get("/items/{item_id}")
    async def read_item(item_id: int, q: str = None):
        return {"item_id": item_id, "q": q}

    @app.post("/items/")
    async def create_item(item: Item):
        return item
    ```

    **Request Body Validation with Pydantic Models:**

    Pydantic is a data validation and settings management library that is tightly integrated with FastAPI. You can use Pydantic models to define the structure of your request and response bodies, and FastAPI will automatically validate the data against the model.

    Here's an example:

    ```python
    from fastapi import FastAPI
    from pydantic import BaseModel

    class Item(BaseModel):
        name: str
        description: str = None
        price: float
        tax: float = None

    app = FastAPI()

    @app.post("/items/")
    async def create_item(item: Item):
        return item
    ```

    In this example, the `Item` class is a Pydantic model that defines the structure of the request body. FastAPI will automatically validate the request body against this model and return an error if the data is invalid.

    **Path Parameters and Query Parameters:**

    Path parameters are used to capture values from the URL path. They are defined using curly braces `{}` in the path. Query parameters are passed as part of the URL query string.

    Here's an example:

    ```python
    from fastapi import FastAPI

    app = FastAPI()

    @app.get("/items/{item_id}")
    async def read_item(item_id: int, q: str = None):
        return {"item_id": item_id, "q": q}
    ```

    In this example, `item_id` is a path parameter and `q` is a query parameter. You can access the values of the path and query parameters using function arguments.

    **Asynchronous Request Handling:**

    FastAPI is built on top of Asynchronous Server Gateway Interface (ASGI), which allows you to handle requests asynchronously. To define an asynchronous API endpoint, you use the `async def` syntax:

    ```python
    from fastapi import FastAPI

    app = FastAPI()

    @app.get("/")
    async def read_root():
        return {"Hello": "World"}
    ```

    Asynchronous request handling allows your application to handle multiple requests concurrently without blocking the main thread.

*   **12.3 API Design Best Practices:**

    Designing a well-structured and consistent API is crucial for the usability and maintainability of your microservice. Here are some best practices for API design:

    *   **RESTful API Principles:**
        *   **Resources:** Represent your data as resources, which are identified by URLs.
        *   **HTTP Methods:** Use HTTP methods (GET, POST, PUT, DELETE, PATCH) to perform operations on resources.
        *   **Status Codes:** Use HTTP status codes to indicate the success or failure of a request.

    *   **Designing Clean and Consistent API Endpoints:**

        *   Use clear and descriptive names for your resources and endpoints.
        *   Follow a consistent naming convention for your API endpoints.
        *   Use plural nouns for collections of resources (e.g., `/users` instead of `/user`).
        *   Use singular nouns for individual resources (e.g., `/users/{user_id}` instead of `/user/{user_id}`).

    *   **Versioning APIs:**

        Versioning your APIs allows you to introduce changes without breaking existing clients. You can version your APIs using different approaches, such as:

        *   **URI Versioning:** Include the version number in the URI (e.g., `/v1/users`, `/v2/users`).
        *   **Header Versioning:** Include the version number in a custom header (e.g., `X-API-Version: 2`).
        *    Versioning example:

        ```python
            from fastapi import FastAPI

            app = FastAPI()

            @app.get("/v1/items/{item_id}")
            async def read_item_v1(item_id: int):
                return {"version": 1, "item_id": item_id}

            @app.get("/v2/items/{item_id}")
            async def read_item_v2(item_id: int):
                return {"version": 2, "item_id": item_id}
        ```
    *   **Error Handling and Response Codes:**

        Use appropriate HTTP status codes to indicate the success or failure of a request. Some common status codes include:

        *   `200 OK`: The request was successful.
        *   `201 Created`: A new resource was created successfully.
        *   `204 No Content`: The request was successful, but there is no content to return.
        *   `400 Bad Request`: The request was invalid.
        *   `401 Unauthorized`: Authentication is required.
        *   `403 Forbidden`: The user does not have permission to access the resource.
        *   `404 Not Found`: The resource was not found.
        *   `500 Internal Server Error`: An unexpected error occurred on the server.
           Code snippet for error Handling:

            ```python
            from fastapi import FastAPI, HTTPException

            app = FastAPI()

            items = {"foo": "The Foo Wrestlers"}

            @app.get("/items/{item_id}")
            async def read_item(item_id: str):
                if item_id not in items:
                    raise HTTPException(status_code=404, detail="Item not found")
                return {"item": items[item_id]}
            ```

    *   **API Documentation with Swagger UI and ReDoc:**

        FastAPI automatically generates interactive API documentation using Swagger UI and ReDoc. You can access the documentation by navigating to `/docs` (for Swagger UI) or `/redoc` (for ReDoc) in your browser.

*   **12.4 Dependency Injection:**

    Dependency injection is a design pattern that allows you to decouple components of your application and make them more testable and maintainable. In dependency injection, dependencies are "injected" into a component, rather than being created by the component itself.

    FastAPI has a powerful dependency injection system that makes it easy to manage dependencies and write testable code. You can create dependencies for database connections, authentication, and other services, and then use the `Depends` function to inject those dependencies into your API endpoints.

    Example:
    ```python
    from typing import Optional

    from fastapi import Depends, FastAPI

    app = FastAPI()

    async def common_parameters(q: Optional[str] = None, skip: int = 0, limit: int = 100):
        return {"q": q, "skip": skip, "limit": limit}

    @app.get("/items/")
    async def read_items(commons: dict = Depends(common_parameters)):
        return commons
    ```

*   **12.5 Database Integration (Asynchronous):**

    To interact with a database asynchronously in FastAPI, you'll need to use an asynchronous database library. Some popular options include:

    *   `asyncpg` (for PostgreSQL)
    *   `databases` (for multi-database support - SQLite, MySQL, PostgreSQL)

    Using the `databases` library as an example:

    1.  **Install Dependencies:**

        ```bash
        pip install databases asyncpg
        ```

    2.  **Connect to a Database Asynchronously:**

        ```python
        import databases
        import sqlalchemy
        from fastapi import FastAPI

        DATABASE_URL = "postgresql://user:password@host:port/database"  # Replace with your database URL

        database = databases.Database(DATABASE_URL)

        metadata = sqlalchemy.MetaData()

        users = sqlalchemy.Table(
            "users",
            metadata,
            sqlalchemy.Column("id", sqlalchemy.Integer, primary_key=True),
            sqlalchemy.Column("name", sqlalchemy.String(50)),
        )

        engine = sqlalchemy.create_engine(DATABASE_URL)
        metadata.create_all(engine)

        app = FastAPI()

        @app.on_event("startup")
        async def startup():
            await database.connect()

        @app.on_event("shutdown")
        async def shutdown():
            await database.disconnect()
        ```
*   **12.6 Authentication and Authorization:**

    Authentication and authorization are essential for securing your microservices. Authentication verifies the identity of a user, while authorization determines what a user is allowed to do.

        *   **Authentication:** Verifying the identity of a user.

        *   **Authorization:** Determining what a user is allowed to do.

       FastAPI provides several ways to implement authentication and authorization:

        *   **API Keys:** A simple authentication mechanism that involves generating a unique key for each user and requiring the user to include the key in every request.
        *   **OAuth 2.0:** A standard protocol for authorization that allows users to grant third-party applications access to their resources without sharing their credentials.
        *   **JWT (JSON Web Tokens):** A standard for creating access tokens that contain claims about the user. JWTs can be used for both authentication and authorization.

        Here's an example of implementing authentication using API keys:

        ```python
        from fastapi import FastAPI, Depends, HTTPException, Header
        from typing import Optional

        app = FastAPI()

        #Store API KEYS
        api_keys = {
            "secret-key-1": "jhon",
            "secret-key-2": "alice",
        }

        async def verify_api_key(x_api_key: Optional[str] = Header(None)):
            if x_api_key not in api_keys:
                raise HTTPException(status_code=401, detail="Invalid API Key")
            return api_keys[x_api_key] #Return the user logged

        @app.get("/items/")
        async def read_items(username: str = Depends(verify_api_key)):
            return {"message": f"Hello {username}!"}
        ```
*   **12.7 Testing FastAPI Applications:**

    Testing is an essential part of developing robust and reliable microservices. FastAPI makes it easy to write unit tests for your API endpoints.

To enable a testing environment you need to install *pip install pytest httpx*

    1) Create a test file

        *   Testing request body validation.
        *   Testing error handling.
        *   Mocking dependencies for testing.

        ```python
        from fastapi.testclient import TestClient
        from .main import app

        client = TestClient(app)

        def test_read_main():
            response = client.get("/")
            assert response.status_code == 200
            assert response.json() == {"Hello": "World"}
        ```
    2) Run the test.

        From your directory folder *pytest* and check it out
*   **12.8 Deployment and Production Considerations:**

    Deploying a FastAPI application to production involves several steps:

    *   **Choosing a Deployment Platform:**
        *   **Docker:** Is great choice to easily deploy applications from one environment to another
        *   **Kubernetes:** Use orchestrate your applications and scale in many different servers
        *   **Cloud Platforms:** AWS, Google Cloud, Azure.

    *   **Containerizing a FastAPI Application with Docker:**

        First, create a `Dockerfile`

        ```dockerfile
        FROM python:3.9-slim-buster

        WORKDIR /app

        COPY requirements.txt ./
        RUN pip install --no-cache-dir -r requirements.txt

        COPY . .

        CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
        ```

        Second, build the Docker image:

        ```
        docker build -t my-fastapi-app .
        ```

        Third, run the Docker container:

        ```
        docker run -p 8000:8000 my-fastapi-app
        ```
    *   **API Gateway:**
        *   An API gateway sits in front of your microservices and handles tasks such as routing requests, authentication, authorization, rate limiting, and caching.


*   **Project 12.1: Building a Task Management Microservice**

```python
from typing import List, Optional

from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
import datetime

app = FastAPI()

# Data model for a task
class Task(BaseModel):
    id: int
    title: str
    description: Optional[str] = None
    owner: str # Owner of the task (e.g., username)
    priority: str = "Normal" # Example priority
    due_date: Optional[datetime.date] = None
    completed: bool = False

# In-memory task store (replace with a database in a real application)
tasks = []
task_id_counter = 1

# Dependency to simulate authentication (replace with a real authentication system)
async def get_current_user(x_token: str = Header(...)):
    """Simulates authentication using a simple header-based API key."""
    if x_token == "valid_api_key":
        return "example_user"  # Replace with actual user retrieval
    else:
        raise HTTPException(status_code=401, detail="Invalid API Key")

# API Endpoints

@app.post("/tasks/", response_model=Task)
async def create_task(task: Task, current_user: str = Depends(get_current_user)):
    """Creates a new task."""
    global task_id_counter
    task.id = task_id_counter
    task_id_counter += 1
    tasks.append(task)
    return task

@app.get("/tasks/", response_model=List[Task])
async def list_tasks(current_user: str = Depends(get_current_user)):
    """Lists all tasks for the current user."""
    return [task for task in tasks if task.owner == current_user]

@app.get("/tasks/{task_id}", response_model=Task)
async def read_task(task_id: int, current_user: str = Depends(get_current_user)):
    """Reads a specific task by ID."""
    task = next((task for task in tasks if task.id == task_id and task.owner == current_user), None)
    if task is None:
        raise HTTPException(status_code=404, detail="Task not found")
    return task

@app.put("/tasks/{task_id}", response_model=Task)
async def update_task(task_id: int, updated_task: Task, current_user: str = Depends(get_current_user)):
    """Updates an existing task."""
    task = next((task for task in tasks if task.id == task_id and task.owner == current_user), None)
    if task is None:
        raise HTTPException(status_code=404, detail="Task not found")

    # Update the task (more robust update logic would be needed in a real application)
    task.title = updated_task.title
    task.description = updated_task.description
    task.priority = updated_task.priority
    task.due_date = updated_task.due_date
    task.completed = updated_task.completed
    return task

@app.delete("/tasks/{task_id}", status_code=204)
async def delete_task(task_id: int, current_user: str = Depends(get_current_user)):
    """Deletes a task."""
    global tasks
    tasks = [task for task in tasks if not (task.id == task_id and task.owner == current_user)]
    return None # 204 No Content

# To run: uvicorn main:app --reload

```

*   **Key Concepts:**

    *   **Pydantic Models:** `Task` model defines data structure and validation.
    *   **Dependency Injection:** `get_current_user` simulates authentication and is injected into endpoints.
    *   **CRUD Operations:** API endpoints for creating, reading, updating, and deleting tasks.
    *   **In-Memory Data Store:** The `tasks` list simulates a database (replace with a real database in production).

*   **Project 12.2: Real-time Data Streaming Microservice**

```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect, Depends
import asyncio
import random
from typing import List, Optional
from fastapi import Header, HTTPException

app = FastAPI()

# Replace with a secure authentication mechanism in production
async def authenticate(websocket: WebSocket, x_token: Optional[str] = Header(None)):
    """Authenticate clients based on API key. Replace for production!"""
    if x_token != "secure_api_key":
        await websocket.close(code=4000)
        raise HTTPException(status_code=401, detail="Invalid API Key")
    return True  # Indicate successful authentication

class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []

    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)

    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)

    async def send_personal_message(self, message: str, websocket: WebSocket):
        try:
            await websocket.send_text(message)
        except Exception as e:
            print(f"Error sending message: {e}")

    async def broadcast(self, message: str):
        for connection in self.active_connections:
            await self.send_personal_message(message, connection)

manager = ConnectionManager()

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket, authenticated: bool = Depends(authenticate)):
    """WebSocket endpoint to stream real-time sensor data"""
    await manager.connect(websocket)
    try:
        while True:
            # Simulate a data point
            data = random.randint(20, 30)  # Simulate temperature reading
            message = f"Sensor Data: {data}°C"

            await manager.send_personal_message(message, websocket) #Send to the user

            await asyncio.sleep(2)  # Send data every 2 seconds
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        print("Client disconnected")
```

*   **Key Concepts:**

    *   **WebSockets:** Real-time communication with clients.
    *   **Asynchronous Background Tasks:** Simulating data generation and pushing data to clients asynchronously.
    *   **Authentication:** Protecting the data stream with API key authentication.

*   **Project 12.3: Machine Learning Model Serving Microservice**

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
import joblib  # For loading Scikit-learn models
import numpy as np
from typing import Optional
from fastapi import Header

app = FastAPI()

# 1. Load the Pre-trained Model
MODEL_FILE = "my_model.joblib" # Replace with your model file
try:
    model = joblib.load(MODEL_FILE)
except FileNotFoundError:
    print(f"Error: Model file '{MODEL_FILE}' not found.")
    exit()

# Sample Scikit-learn model creation
def create_sample_model():
    from sklearn.linear_model import LinearRegression
    import joblib
    #Train some data
    model = LinearRegression()
    model.fit([[1], [2], [3]], [1, 2, 3])
    joblib.dump(model, 'my_model.joblib')

create_sample_model()

# 2. Define the Input Data Model using Pydantic
class InputData(BaseModel):
    feature1: float
    feature2: float
    feature3: float = 0 # Allow a default

#Add a sample class
class PredictionOutput(BaseModel):
    prediction: float

#Simulate Autentication
api_keys = {
    "secret-key-1": "jhon",
    "secret-key-2": "alice",
}

#Implement verification
async def verify_api_key(x_api_key: Optional[str] = Header(None)):
    if x_api_key not in api_keys:
        raise HTTPException(status_code=401, detail="Invalid API Key")
    return api_keys[x_api_key] #return username

# 3. Define the Prediction Endpoint
@app.post("/predict", response_model = PredictionOutput)
async def predict(data: InputData, username : str = Depends(verify_api_key)):
    """
    API endpoint for making predictions using the pre-trained model.
    """
    try:
        # Prepare the input data for the model
        input_array = np.array([[data.feature1, data.feature2, data.feature3]])

        # Make the prediction
        prediction = model.predict(input_array)[0] #Get 1 element

        return {"prediction": prediction}

    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

*   **Key Concepts:**

    *   **Model Loading:** The code loads a pre-trained Scikit-learn model from a file. You can create with the current code.
    *   **Input Validation:** The `InputData` Pydantic model defines the structure of the input data and enforces validation rules.
    *   **API Endpoint:** The `/predict` endpoint accepts input data, makes a prediction using the model, and returns the prediction as a JSON response.

