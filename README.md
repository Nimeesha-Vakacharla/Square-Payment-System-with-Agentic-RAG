# Square-Payment-System-with-Agentic-RAG
## Overview

This project implements a payment system integrated with the Square API for a small business, tailored for processing payments, retrieving payment details, and issuing refunds. It uses **Agentic Retrieval-Augmented Generation (RAG)** to dynamically retrieve relevant Square API details and generate full-stack code (frontend + backend) based on user queries.

Key components include:
- **LangChain** for agentic behavior
- **Chroma VectorDB** for retrieval
- **Google Gemini LLM** for NLP and code generation

The result is a set of HTML files with the generated code and a zipped archive for easy sharing.

---

## Problem Statement

The system addresses key challenges:

- **Simplify API Discovery:** Automate finding endpoints, parameters, and examples from Square APIs.
- **Automate Code Generation:** Eliminate manual effort in writing frontend/backend code.
- **Handle User Queries:** Convert user-friendly queries (e.g., "How do I create a payment?") into working code.
- **Ensure Scalability & Reusability:** Easily adapt to new APIs and user scenarios.

---

## Why Retrieval-Augmented Generation (RAG)?

- **Efficient Information Retrieval** from pre-processed Square API dataset using Chroma VectorDB.
- **Context-Aware Responses** by combining retrieved data with Gemini-generated output.
- **Scalability** by updating the dataset with new APIs.
- **Accuracy** by grounding generations in real data, reducing hallucination.

---

## Why Agentic RAG?

Built using **LangChain's ReAct framework**, Agentic RAG brings:

- **Dynamic Tool Usage** (e.g., retrieval + code generation)
- **Iterative Problem Solving** via reasoning and refinement
- **Error Handling & Fallbacks** (e.g., mock responses)
- **Conversational Memory** for coherent multi-turn interactions

---

## Square API Focus

Square Payments API (v2):

- **Create Payment:** `POST /v2/payments`
- **Retrieve Payment:** `GET /v2/payments/{payment_id}`
- **Create Refund:** `POST /v2/refunds`
- **List Payments:** `GET /v2/payments?...`
- **Create Customer:** `POST /v2/customers`

Dataset: `square_payments_apis.csv`  
Includes endpoint, description, parameters, method, and Node.js examples.

---

## What We Did

### 1. Dataset Preparation
- Created a CSV with key API details.
- Generated dynamically if not found.

### 2. Environment Setup
- Installed: `langchain`, `chromadb`, `pandas`, `dotenv`, `numpy`, `langchain-google-genai`
- Loaded secrets (Gemini API key) securely.

### 3. Chroma VectorDB Setup
- Created documents from CSV (name + description + endpoint).
- Embedded using `GoogleGenerativeAIEmbeddings`.
- Stored in Chroma VectorDB.

### 4. Agentic RAG System
Defined tools:
- `retrieve_api_details`: fetches API info
- `generate_code`: builds frontend (JS) + backend (Node.js/Express) code

Agent:
- LangChain ReAct agent using `gemini-1.5-flash-001`
- `ConversationBufferMemory` for context
- Prompt template to output in structured JSON

### 5. Query Processing
Handled queries like:
- "How do I create a payment using Square?"
- "How can I retrieve payment details?"
- "How do I create a refund?"

For each:
- Retrieved API details
- Generated full-stack code
- Saved output as HTML + `.zip`

### 6. Output Generation
- HTML files for each query result
- Downloadable `.zip` file
- Links shown via `IPython.display.FileLink`

### 7. Error Handling
- `tenacity` retries for Gemini API errors
- Logged errors and fallbacks
- Mock responses used when quotas were exceeded

---

## System Design

### Agent Architecture

Single-agent system using LangChain ReAct:
- **Input:** User query
- **Agent Layer:** Reason + act with tools
- **Retrieval Layer:** Chroma VectorDB
- **Generation Layer:** Gemini LLM
- **Output Layer:** Generated HTML code

---

## Technologies Used

- **Python** – Core language
- **LangChain** – Agent orchestration
- **Chroma VectorDB** – Embedding-based retrieval
- **Google Gemini (gemini-1.5-flash-001)** – LLM
- **Node.js / Express** – Backend code
- **JavaScript** – Frontend code
- **pandas / numpy** – Dataset handling
- **Kaggle** – Notebook runtime for GPU + secrets

---

## Usage

###  Run the Notebook

- Execute the Jupyter notebook to process the predefined queries.
- You can also modify the `queries` list to include your own custom queries.

**Example Queries:**
- "How do I create a payment using Square?"
- "How can I retrieve details of a specific payment?"
- "How do I create a refund for a payment?"

---

###  View Generated Code

- The system will **print the frontend and backend code** for each query.
- HTML files will be saved (e.g.,  
  `full_stack_code_how_do_i_create_a_payment_using_square.html`).
- A zip file `square_payment_system.zip` will be created containing all outputs.

---

###  Integrate the Code

- Use the generated **frontend JavaScript** code in your client-side app.
- Use the generated **backend Node.js/Express** code in your server-side app.
- Replace the placeholder values:
  - `YOUR_ACCESS_TOKEN` → with your actual Square access token
  - `UUID_PLACEHOLDER` → with your actual UUID or dynamically generated ID

---

##  Example Outputs

For the query **"How can I retrive details of a specific payemnt?"**, the system generates:

---

###  Frontend (JavaScript)

```javascript
// Frontend code (JavaScript)
const data = {
payment_id: "J4cpcYKgzOfvQ7VO1ilH3CIpk7KZY",
};
fetch('http://localhost:3001/payments?payment_id={data.payment_id}', {
    method: 'GET',
    headers: {
        'Content-Type': 'application/json'
    },
    
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error('Error:', error));

###  Backend (Node.js/Express)

```javascript
// Backend code (Node.js/Express)
const express = require('express');
const { Client, Environment } = require('square');
const router = express.Router();

const client = new Client({
  environment: Environment.Sandbox,
  accessToken: 'YOUR_ACCESS_TOKEN'
});

router.get('/payments', async (req, res) => {
  try {
    const { paymentId } = req.query;
    if (!paymentId) throw new Error('Missing required parameters');
    
    const response = await client.paymentsApi.getPayment(paymentId);

    res.json(response.result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

module.exports = router;
