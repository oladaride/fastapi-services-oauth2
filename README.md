📖 Overview
This project focuses on the design, containerization, and deployment of a functional 2-tier web application using Docker and Docker Compose. The goal was to establish a portable, consistent environment that ensures reliable data persistence across a FastAPI backend and a PostgreSQL database.

🛠️ 1. Environment Setup & Tooling
The deployment was executed on a local Ubuntu Virtual Machine. Before containerization, the VM was updated and configured with the necessary runtime tools.
Installation & Configuration
All administrative commands were executed with sudo privileges to ensure proper permissioning for the Docker socket.

# Update system packages
`sudo apt update && sudo apt upgrade -y`

# Install Docker Engine and Docker Compose
`sudo apt install docker.io -y`
`sudo apt install docker-compose -y`
# Verify installations
`docker --version`
`docker-compose --version`

📂 2. Repository & Configuration Management
Version Control: Forked the repository to a personal GitHub account and cloned it to the local Ubuntu VM environment.
Security & Environment: Sensitive data and database credentials were decoupled from the source code and managed via a .env file.
Service Discovery: Configured the FastAPI application to connect to PostgreSQL using Docker DNS, allowing the app to resolve the database location via its service name.

🏗️ 3. System Architecture
The application utilizes a 2-tier architecture to separate the logic layer from the data layer:
Tier 1: FastAPI (Python 3.11) – Manages application logic, request handling, and API routing.
Tier 2: PostgreSQL 15 – Provides robust, persistent data storage.

📄 4. Container Specifications
The Dockerfile
Built using a python:3.11-slim base image to maintain a small footprint and high performance.

Dockerfile
```
FROM python:3.11-slim
WORKDIR /app
COPY pyproject.toml .
COPY README.md .
COPY app ./app
RUN pip install --no-cache-dir .
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```
The Docker Compose Orchestration
Defines the interaction between services, internal networking, and persistent volumes.

YAML
```
version: '3.8'
services:
  web:
    build: .
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - db
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data: # Ensures data persists even if the container is removed
  ```

🚀 5. Execution & Testing
To deploy the stack, I used the following command to ensure the image was built from the latest source:
`docker-compose up --build`
Verification: Deployment was confirmed by testing the endpoint via curl.
`curl http://localhost:8000/`

🔍 6. Technical Challenges & Insights
- Syntax & Versioning
During the task, I successfully navigated the nuances of Docker Compose syntax, specifically the use of the --build flag. This reinforced my understanding of the evolution from the older Python-based docker-compose to the modern Docker V2 CLI.
- Port Mapping Concepts (8000:8000)
A key part of my research involved understanding why the application was mapped to port 8000. I gained a deep understanding of the Host-to-Container bridge: the VM listens on the host port to forward traffic to the container port where the FastAPI application is active.

✅ Conclusion
This implementation successfully demonstrates a functional 2-tier containerized application. By leveraging Docker Compose, I achieved a "one-command" deployment that is portable and maintains data integrity through volumes.
