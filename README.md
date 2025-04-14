# Bitovi Blog AI Agent

This workflow shows how to set up and run the created **Bitovi Blog AI Agent**.

## Prerequisites
- [Git](https://github.com/)
- [Docker](https://docs.docker.com/engine/install/)
- [Docker-Compose](https://docs.docker.com/compose/install/)

## Instructions

### Step 1: Clone the Repository
Clone the repository using:

    git clone https://github.com/nkofficial-1005/bitovi.git

### Step 2: Run Containers

#### n8n & qdrant:
Start the docker-compose service by running:

    docker-compose up --build

#### postgres (Network Configuration Note):
```bash
docker-compose up -d
```
If n8n and postgres (pg-n8n) are not detected on the same network, run the following commands:

    docker network create shared-network
    docker network connect shared-network n8n-getting-started-n8n-1
    docker network inspect shared-network

*(Ensure that both containers appear on the same network when inspected.)*

### Step 3: Setup PostgreSQL
Open the PostgreSQL SQL editor:

    docker exec -it pg-n8n psql -U n8n -d n8n

Check existing relations:

    \dt

Create the `blog_posts` table:

    CREATE TABLE blog_posts (
        id SERIAL PRIMARY KEY,
        link TEXT,
        title TEXT,
        date TEXT,
        category TEXT,
        summary TEXT,
        author TEXT
    );

### Step 4: Setup Qdrant
Create the collection in Qdrant by sending:

    curl -X PUT "http://localhost:6333/collections/bitovi_blog" \
         -H "Content-Type: application/json" \
         -d "{\"vectors\":{\"size\":384,\"distance\":\"Cosine\"}}"

### Step 5: Access the UIs
- **n8n UI:** http://localhost:5678/
- **Qdrant UI:** http://localhost:6333/dashboard

### Step 6: Import the Workflow in n8n
Import the provided JSON file named `Bitovi_Blog_AI_Agent` into n8n.

### Step 7: Configure Credentials

#### 7A: API Keys / Connections
Add credentials or API keys for Hugging Face, OpenAI, or OpenRouter.

#### 7B: PostgreSQL Connection
    Host: pg-n8n
    Database: n8n
    Username: n8n
    Password: password

#### 7C: Qdrant Connection
    Qdrant URL: http://host.docker.internal:6333/

### Step 8: Test the Workflow
Trigger the workflow (scheduled to run every midnight) by clicking **Test Workflow** in n8n. The blog post data and embeddings should now be stored in PostgreSQL and Qdrant, respectively.

### Step 9: Use the RAG AI Agent
The RAG AI Agent is now ready to accept prompts and generate answers. 🎉