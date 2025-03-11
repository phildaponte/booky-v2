# Database Schema for AI James Clear Chatbot

## 📌 Overview
This document outlines the database structure for storing **user data, chat history, and book embeddings** in **Supabase (PostgreSQL) and Pinecone (Vector Database).**

---

## **1️⃣ Users Table (Supabase)** 👤
Stores registered user information.
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email TEXT UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

---

## **2️⃣ User Memory Table (Supabase)** 🧠
Stores user-specific habit goals and long-term memory.
```sql
CREATE TABLE user_memory (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    goals TEXT[],
    habits TEXT[],
    last_updated TIMESTAMP DEFAULT NOW()
);
```

### **Example Data:**
```json
{
  "user_id": "12345",
  "goals": ["Wake up at 6 AM", "Read 10 pages daily"],
  "habits": ["Habit stacking", "2-Minute Rule"]
}
```

---

## **3️⃣ Chat History Table (Supabase)** 💬
Stores user conversations with the AI.
```sql
CREATE TABLE chat_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    message TEXT,
    ai_response TEXT,
    timestamp TIMESTAMP DEFAULT NOW()
);
```

### **Example Data:**
```json
{
  "user_id": "12345",
  "message": "How do I build a habit of exercising?",
  "ai_response": "Try habit stacking: Link exercise to an existing habit, like brushing your teeth.",
  "timestamp": "2024-03-10T12:00:00Z"
}
```

---

## **4️⃣ Books Table (Supabase)** 📖
Stores metadata for available AI-powered books.
```sql
CREATE TABLE books (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    author TEXT NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### **Example Data:**
```json
{
  "id": "atomic_habits",
  "title": "Atomic Habits",
  "author": "James Clear",
  "description": "A book about habit formation and behavior change."
}
```

---

## **5️⃣ Book Embeddings (Pinecone Vector Database)** 🔍
Stores processed book chunks as vector embeddings for retrieval.

### **Embedding Structure:**
Each book is split into **500-1000 word chunks**, converted into vector embeddings, and stored with metadata.
```json
{
  "id": "chunk_001",
  "vector": [0.123, 0.456, ..., 0.789],
  "metadata": {
    "book": "Atomic Habits",
    "chapter": "Habit Stacking",
    "content": "To build a habit, stack it onto another habit you already do."
  }
}
```

### **Example Query to Retrieve Relevant Sections:**
```python
import pinecone
import openai

pinecone.init(api_key="YOUR_PINECONE_API_KEY", environment="us-west1")
index = pinecone.Index("book-embeddings")

query = "How do I start a habit?"
query_vector = openai.Embedding.create(
    input=query,
    model="text-embedding-ada-002"
)["data"][0]["embedding"]

results = index.query(vector=query_vector, top_k=3, include_metadata=True)
```

---

## 🎯 **Final Notes**
✅ **Supabase stores user-related data** (habits, goals, chats, books).  
✅ **Pinecone stores book embeddings** for fast AI retrieval.  
✅ **Tables are relational**, ensuring efficient lookups and filtering.