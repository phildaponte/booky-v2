 # API Documentation for AI James Clear Chatbot
  
  ## 📌 Overview
  This document outlines the API structure for the AI James Clear chatbot, including authentication, chat interactions, book retrieval, and user memory storage.
  
  ---
  
  ## **1️⃣ Authentication API (Supabase Auth)** 🔐
  ### `POST /api/auth/login`
  **Description:** Handles user login and authentication via Supabase.
  #### **Request Body:**
  ```json
  {
    "email": "user@example.com",
    "password": "securepassword"
  }
  ```
  #### **Response:**
  ```json
  {
    "token": "jwt-auth-token",
    "user": { "id": "12345", "email": "user@example.com" }
  }
  ```
  
  ### `POST /api/auth/register`
  **Description:** Registers a new user.
  #### **Request Body:**
  ```json
  {
    "email": "user@example.com",
    "password": "securepassword"
  }
  ```
  #### **Response:**
  ```json
  {
    "message": "User registered successfully",
    "user_id": "12345"
  }
  ```
  
  ---
  
  ## **2️⃣ Chat API (GPT-4 Turbo + Pinecone)** 💬
  ### `POST /api/chat`
  **Description:** Handles AI chatbot interactions.
  #### **Request Body:**
  ```json
  {
    "user_id": "12345",
    "message": "How do I build a habit of reading?",
    "book_id": "atomic_habits"
  }
  ```
  #### **Response:**
  ```json
  {
    "ai_response": "Try habit stacking: Link reading to an existing habit, like drinking coffee.",
    "source": "Atomic Habits - Chapter 3"
  }
  ```
  
  ---
  
  ## **3️⃣ Book Retrieval API (Pinecone Vector Search)** 📖
  ### `GET /api/books`
  **Description:** Fetches the list of available AI-powered books.
  #### **Response:**
  ```json
  [
    { "id": "atomic_habits", "title": "Atomic Habits", "author": "James Clear" },
    { "id": "deep_work", "title": "Deep Work", "author": "Cal Newport" }
  ]
  ```
  
  ### `GET /api/books/:book_id`
  **Description:** Fetches details of a specific book.
  #### **Response:**
  ```json
  {
    "id": "atomic_habits",
    "title": "Atomic Habits",
    "author": "James Clear",
    "description": "A book about habit formation and behavior change."
  }
  ```
  
  ---
  
  ## **4️⃣ User Memory API (Supabase Storage)** 🧠
  ### `GET /api/user-memory`
  **Description:** Retrieves stored user habits, goals, and past interactions.
  #### **Request:**
  ```json
  {
    "user_id": "12345"
  }
  ```
  #### **Response:**
  ```json
  {
    "goals": ["Wake up at 6 AM", "Read 10 pages daily"],
    "challenges": ["Struggles with consistency"],
    "last_conversation": "Hey James, I finally woke up early!"
  }
  ```
  
  ### `POST /api/user-memory`
  **Description:** Saves or updates user habits and goals.
  #### **Request Body:**
  ```json
  {
    "user_id": "12345",
    "goals": ["Exercise every morning"],
    "habits": ["2-Minute Rule"]
  }
  ```
  #### **Response:**
  ```json
  {
    "message": "User memory updated successfully"
  }
  ```
  
  ---
  
  ## 🎯 **Final Notes**
  ✅ **Rate limiting:** Prevent API abuse by limiting excessive requests.
  ✅ **Error handling:** Ensure proper validation & error messages.
  ✅ **Authentication:** Use JWT tokens for secure API access.