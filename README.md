# Medical Chatbot

A medical question-answering chatbot built using **LangChain, Groq, Pinecone, Hugging Face embeddings, and Flask**.

The application uses a **Retrieval-Augmented Generation (RAG)** pipeline to retrieve relevant information from a medical knowledge base stored in Pinecone and generate responses using a Groq-hosted LLM.

> **Disclaimer:** This project is for educational purposes only and is not a substitute for professional medical advice, diagnosis, or treatment.

---

## Tech Stack

* **Python 3.11**
* **LangChain**
* **Flask**
* **Groq**
* **Pinecone**
* **Hugging Face Embeddings**
* **Sentence Transformers**
* **Docker**
* **AWS EC2**
* **AWS ECR**
* **GitHub Actions**

---

# How to Run Locally

## Step 1 — Clone the Repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd <YOUR_REPOSITORY_FOLDER>
```

---

## Step 2 — Create a Virtual Environment

This project is configured to run with Python 3.11.

### Windows

```powershell
py -3.11 -m venv .venv
```

Activate the environment:

```powershell
.\.venv\Scripts\Activate.ps1
```

### Linux / macOS

```bash
python3.11 -m venv .venv
source .venv/bin/activate
```

---

## Step 3 — Install Dependencies

```bash
pip install -r requirements.txt
```

---

## Step 4 — Configure Environment Variables

Create a `.env` file in the root directory of the project.

```ini
PINECONE_API_KEY="your_pinecone_api_key"
GROQ_API_KEY="your_groq_api_key"
```

**Never commit your `.env` file or expose your API keys publicly.**

Make sure `.env` is included in `.gitignore`.

---

## Step 5 — Store Embeddings in Pinecone

Run:

```bash
python store_index.py
```

This processes the medical documents, generates embeddings using the Hugging Face embedding model, and stores them in Pinecone.

---

## Step 6 — Start the Application

Run:

```bash
python app.py
```

The Flask application runs on port `8080`.

Open:

```text
http://localhost:8080
```

---

# How the Application Works

The project follows a Retrieval-Augmented Generation (RAG) architecture.

```text
Medical Documents
       ↓
Document Loading
       ↓
Text Splitting
       ↓
Hugging Face Embeddings
       ↓
Pinecone Vector Database
       ↓
User Question
       ↓
Similarity Search
       ↓
Relevant Medical Documents
       ↓
Groq LLM
       ↓
Generated Answer
       ↓
Flask Web Interface
```

The application retrieves relevant documents from Pinecone before sending the context and user's question to the LLM.

---

# LLM

This project uses **Groq** instead of OpenAI for response generation.

The application is currently configured with:

```text
openai/gpt-oss-20b
```

The Groq API key is loaded from:

```ini
GROQ_API_KEY="your_groq_api_key"
```

---

# Embeddings

The project uses the Hugging Face Sentence Transformers model:

```text
sentence-transformers/all-MiniLM-L6-v2
```

These embeddings are stored and searched using Pinecone.

---

# Project Structure

```text
Medical-Chatbot/
│
├── .github/
│   └── workflows/
│       └── cicd.yaml
│
├── data/
│   └── medical documents
│
├── research/
│   └── experiments and notebooks
│
├── src/
│   ├── helper.py
│   └── prompt.py
│
├── static/
│
├── templates/
│   └── chat.html
│
├── .env
├── .gitignore
├── Dockerfile
├── LICENSE
├── README.md
├── app.py
├── requirements.txt
├── setup.py
├── store_index.py
└── template.sh
```

---

# AWS CI/CD Deployment

The project can be containerized with Docker and deployed to AWS using GitHub Actions.

The deployment architecture is:

```text
GitHub Repository
       ↓
GitHub Actions
       ↓
Build Docker Image
       ↓
Push Image to Amazon ECR
       ↓
AWS EC2
       ↓
Pull Docker Image
       ↓
Run Container
```

---

## AWS Components

The deployment uses:

* **Amazon EC2** — hosts the application
* **Amazon ECR** — stores the Docker image
* **GitHub Actions** — automates the deployment process
* **Docker** — containerizes the application

---

# AWS Setup

## 1. Create an IAM User

Create an IAM user with the permissions required for your deployment.

For a learning/demo setup, the tutorial uses:

* `AmazonEC2ContainerRegistryFullAccess`
* `AmazonEC2FullAccess`

For a production deployment, use more restrictive, least-privilege policies.

---

## 2. Create an ECR Repository

Create an Amazon ECR repository to store the Docker image.

After creating the repository, save its repository URI.

Do not hard-code your personal AWS account ID or repository URI in this README.

---

## 3. Create an EC2 Instance

Create an Ubuntu EC2 instance.

The EC2 instance will host the Docker container.

---

## 4. Install Docker on EC2

Update the system:

```bash
sudo apt-get update -y
sudo apt-get upgrade
```

Install Docker:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Add the Ubuntu user to the Docker group:

```bash
sudo usermod -aG docker ubuntu
```

Apply the group change:

```bash
newgrp docker
```

---

## 5. Configure GitHub Actions Self-Hosted Runner

In your GitHub repository:

```text
Settings
→ Actions
→ Runners
→ New self-hosted runner
```

Select the appropriate operating system and follow the commands provided by GitHub.

---

# GitHub Secrets

Add the following secrets to:

```text
GitHub Repository
→ Settings
→ Secrets and variables
→ Actions
```

Required secrets:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_DEFAULT_REGION
ECR_REPO
PINECONE_API_KEY
GROQ_API_KEY
```

### Important

Do not put API keys directly inside:

* `app.py`
* `store_index.py`
* `README.md`
* `Dockerfile`
* GitHub workflow files

Use environment variables and GitHub Secrets instead.

---

# Docker

Build the Docker image:

```bash
docker build -t medical-chatbot .
```

Run the container locally:

```bash
docker run -p 8080:8080 medical-chatbot
```

Then open:

```text
http://localhost:8080
```

---

# Environment Variables

The application requires:

| Variable           | Purpose                              |
| ------------------ | ------------------------------------ |
| `PINECONE_API_KEY` | Connects the application to Pinecone |
| `GROQ_API_KEY`     | Authenticates requests to Groq       |

For local development, these values should be stored in `.env`.

For GitHub Actions/AWS deployment, use GitHub Secrets.

---

# License

This project is licensed under the **Apache License 2.0**.

See the [`LICENSE`](LICENSE) file for details.

This repository is a modified/extended version of an existing open-source medical chatbot project. The original license and required notices are retained in accordance with the Apache License 2.0.
