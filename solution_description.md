

# HealtheLink AI Exploration Architecture

This document outlines the architecture and workflow for a voice- and text-enabled AI-driven query engine deployed on AWS. The solution leverages a local LLM, Vanna.AI, and a Postgres database, with all components containerized and integrated via secure, scalable services.

---

## 🔷 Architecture Components

### 🧑‍💬 User Interaction: NLP Question Input (Optional Speech-to-Text)

Users initiate queries via natural language, optionally using speech-to-text capabilities for accessibility. Input is securely transmitted via HTTPS to the AI processing engine.

### 🧠 Prompt Generation (via Vanna.AI)

Vanna.AI orchestrates the conversion of natural language into SQL using vector search, context-aware prompt engineering, and a connected local LLM. This component runs in a secured container within a private VPC.

### 🔍 Search to Identify Intent

The search module queries the vector store to retrieve relevant metadata (DDL, schema documentation, validated queries) to provide context for prompt construction. This ensures accurate translation of user intent.

### ✍️ Prompt Engineering (via Vanna.AI)

Search results are used to construct a prompt dynamically. This prompt is fed to the LLM to generate SQL. Prompt crafting follows a secure, stateless design and is isolated within a sandboxed Lambda or container environment.

### 📚 Vector Store (RAG)

A vector database (ChromaDB) stores embeddings of structured metadata (schemas, definitions, documentation). It supports semantic search with role-based access control and encryption-at-rest.

### 🧠 Local LLM

A containerized 7.5B parameter LLM processes the engineered prompt and returns SQL. The model runs within AWS infrastructure (e.g., ECS, EC2, or EKS) with access logging, isolated compute, and encrypted volumes.

### 💾 SQL Execution Layer

PostgreSQL serves as the backend database. Queries are executed via secure, read-only connections. Activity is logged and monitored using AWS CloudWatch and GuardDuty.

### 🟣 Query Output & Visualization

Query results are displayed using Plotly Dash or Streamlit within a web-based UI. The application supports interactive charts and enables follow-up questions.

---

## 🧪 Output Validation & Feedback Loop

### ✅ Correct Results (Positive Path)

If the user confirms the output is correct, results can be exported or visualized. The interaction may continue via natural language follow-up questions, forming a conversational loop.

### ❌ Incorrect Results (Negative Path)

If results are unsatisfactory, the user is prompted to refine the question or manually adjust the query.

### 🛠️ Manual Query Rewrite

Users can bypass the AI to directly input or edit SQL queries. A manual override interface is provided with built-in syntax validation and limited access to prevent unsafe operations.

### 🔁 Query Loopback

Any corrected query can be resubmitted, with the process rerunning through the execution and visualization layer for re-validation.

---

## 🔐 Security Architecture

* **Encryption**: All data in transit is secured with TLS 1.2+. All data at rest (vector store, database, logs) is encrypted with AWS KMS.
* **Firewalls**: AWS Security Groups and Network ACLs restrict inbound and outbound traffic. Only approved services can communicate with the internal components.
* **IAM & Access Control**: Fine-grained IAM policies restrict access to compute, storage, and database services.
* **Audit Logging**: All access and system activity is logged using AWS CloudTrail and CloudWatch.
* **Secrets Management**: Environment variables and credentials are managed via AWS Secrets Manager or Parameter Store.
* **Vulnerability Scanning**: Containers are regularly scanned using Amazon Inspector or equivalent.
* **Schema / Database Filtering**:  What access is required to protect certain aspects of the database. 


