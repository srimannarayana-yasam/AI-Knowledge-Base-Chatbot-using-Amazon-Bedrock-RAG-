# AI-Knowledge-Base-Chatbot-using-Amazon-Bedrock-RAG
This project is a serverless AI-powered chatbot built on AWS using Amazon Bedrock Knowledge Bases (RAG).
It allows users to ask questions via a web interface and receive grounded, accurate answers based on curated knowledge stored in Bedrock.

🚀 Project Overview

The application uses a Retrieval-Augmented Generation (RAG) architecture:

A static web UI hosted on Amazon S3

A serverless backend using AWS Lambda (Function URL)

Amazon Bedrock Knowledge Base for retrieval

Foundation Model via Amazon Bedrock for response generation

The chatbot answers questions using only the data provided in the Knowledge Base, ensuring accuracy and relevance.

🏗️ Architecture Flow

High-level request flow:

User opens the web UI (S3 static website)

User submits a question

Browser sends a POST request to a Lambda Function URL

Lambda calls Amazon Bedrock RetrieveAndGenerate

Bedrock:

Retrieves relevant content from the Knowledge Base (vector search)

Generates a response using a foundation model

Lambda returns the response to the UI

UI displays the answer to the user

Browser
  → S3 Static Website
    → Lambda Function URL
      → Amazon Bedrock Agent Runtime
        → Knowledge Base (Retrieve)
        → Foundation Model (Generate)
      → Lambda
  → Browser

🧩 AWS Services Used
1️⃣ Amazon S3

Hosts the static frontend website (HTML, CSS, JavaScript)

Public static website hosting enabled

Serves the chatbot UI to users

2️⃣ AWS Lambda

Acts as the backend API

Exposed via Lambda Function URL (no API Gateway required)

Handles:

CORS

Input validation

Calls to Amazon Bedrock

Response formatting

3️⃣ Amazon Bedrock

Provides access to foundation models

Used for text generation

Integrated via Bedrock Agent Runtime

4️⃣ Amazon Bedrock Knowledge Base

Stores domain-specific documents

Uses vector embeddings for semantic retrieval

Enables Retrieval-Augmented Generation (RAG)

5️⃣ AWS IAM

IAM role attached to Lambda

Least-privilege permissions for:

bedrock:RetrieveAndGenerate

Bedrock model access

Knowledge Base access

🧠 Retrieval-Augmented Generation (RAG)

Instead of sending the question directly to a model, this project uses RAG:

🔍 Retrieve: Find relevant content from the Knowledge Base

✍️ Generate: Use retrieved content + question to generate a response

📚 Grounded answers: Reduces hallucinations and improves accuracy

⚙️ Configuration (Environment Variables)

Lambda uses the following environment variables:

BEDROCK_REGION=us-east-2
KNOWLEDGE_BASE_ID=48MNNTPTJF
MODEL_ARN=arn:aws:bedrock:us-east-2::foundation-model/meta.llama3-3-70b-instruct-v1:0

🖥️ Frontend Features

Chat-style UI

Example questions for quick testing

Copy response button

Status indicator (online / error)

Timeout handling and error messages

✅ Key Highlights

Fully serverless

No API Gateway required (uses Lambda Function URL)

Secure, scalable, and cost-efficient

Demonstrates real-world GenAI + AWS integration

Production-ready RAG architecture

📌 Use Cases

Personal portfolio chatbot

Internal knowledge assistant

Resume / profile Q&A bot

Enterprise knowledge base search

📈 What This Project Demonstrates

AWS serverless architecture

Amazon Bedrock RAG implementation

Knowledge Base integration

Proper region and IAM configuration

Debugging real-world GenAI issues

Frontend ↔ backend integration

🧑‍💻 Author

Venkata Srimannarayana Yasam
Software Engineer | AWS Certified Solutions Architect

📧 Email: srimannarayana.yasam@gmail.com

💼 LinkedIn: https://www.linkedin.com/in/venkata-srimannarayana-yasam

💻 GitHub: https://github.com/srimannarayana-yasam
