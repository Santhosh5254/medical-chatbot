# Medical Chatbot

A medical question-answering chatbot built using **LangChain, Groq, Pinecone, FastEmbed, and Flask**.

The application uses a **Retrieval-Augmented Generation (RAG)** pipeline to retrieve relevant information from a medical knowledge base stored in Pinecone and generate responses using a Groq-hosted LLM.

> **Disclaimer:** This project is for educational purposes only and is not a substitute for professional medical advice, diagnosis, or treatment.

## Live Demo

**[Medical Chatbot](https://medical-chatbot-one-sable.vercel.app/)**

---

## Tech Stack

* **Python 3.11**
* **LangChain**
* **Flask**
* **Groq**
* **Pinecone**
* **FastEmbed**
* **Docker**
* **Vercel**

---

# How to Run Locally

## Step 1 — Clone the Repository

```bash
git clone https://github.com/Santhosh5254/medical-chatbot.git

cd medical-chatbot
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

Create a `.env` file in the root directory of the project:

```ini
PINECONE_API_KEY="your_pinecone_api_key"
GROQ_API_KEY="your_groq_api_key"
```

**Never commit your `.env` file or expose your API keys publicly.**

Make sure `.env` is included in `.gitignore`.

---

## Step 5 — Store Embeddings in Pinecone

If setting up the project from scratch, run:

```bash
python store_index.py
```

This processes the medical PDF, generates embeddings using **FastEmbed**, and stores the vectors in Pinecone.

> If you are using an existing Pinecone index that has already been populated with the `all-MiniLM-L6-v2` embeddings, you do not need to run this step again.

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

The project follows a **Retrieval-Augmented Generation (RAG)** architecture.

```text
Medical PDF
     ↓
Document Loading
     ↓
Text Splitting
     ↓
FastEmbed
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

When a user asks a question, the application searches Pinecone for relevant medical information and provides the retrieved context to the Groq LLM before generating the final response.

---

# LLM

This project uses **Groq** for response generation.

The application is currently configured with:

```text
openai/gpt-oss-20b
```

The model is accessed through the Groq API using:

```ini
GROQ_API_KEY="your_groq_api_key"
```

The model name contains `openai/`, but the API request is made through **Groq's service** using LangChain's `ChatGroq` integration.

---

# Embeddings

The project uses **FastEmbed** with the following embedding model:

```text
sentence-transformers/all-MiniLM-L6-v2
```

FastEmbed is used instead of the heavier Sentence Transformers/PyTorch stack, making the application significantly more suitable for serverless deployment.

The generated embeddings are stored and searched using Pinecone.

---

# Project Structure

```text
medical-chatbot/
│
├── data/
│   └── Medical_book.pdf
│
├── src/
│   ├── __init__.py
│   ├── helper.py
│   └── prompt.py
│
├── static/
│   └── style.css
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

> `.env` is intentionally excluded from version control and should never be committed.

---

# Deployment

The application is deployed using **Vercel**.

The GitHub repository is connected to Vercel, allowing new changes pushed to the `main` branch to trigger a new deployment.

### Deployment Architecture

```text
GitHub Repository
       ↓
     Vercel
       ↓
Flask Application
       ↓
   Pinecone
       ↓
     Groq
```

### Production Environment Variables

The following environment variables are configured in Vercel:

```text
PINECONE_API_KEY
GROQ_API_KEY
```

API keys are stored as Vercel environment variables rather than being included in the source code.

---

# Docker

The project also includes a Dockerfile for containerized deployment.

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

| Variable           | Purpose                              |
| ------------------ | ------------------------------------ |
| `PINECONE_API_KEY` | Connects the application to Pinecone |
| `GROQ_API_KEY`     | Authenticates requests to Groq       |

For local development, store these values in `.env`.

For Vercel deployment, configure them through the project's Environment Variables settings.

**Never hard-code API keys in the source code.**

---

# License

This project is licensed under the **Apache License 2.0**.

See the `LICENSE` file for details.

This repository is a modified/extended version of an existing open-source medical chatbot project. The original license and required notices are retained in accordance with the Apache License 2.0.
