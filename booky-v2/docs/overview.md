### 🔹 UI Components Breakdown
- `/` (Home Page) → Landing page with AI chatbot overview
- `/chat` (Chat UI) → The main chat page where users interact with the AI
- `/books` (Book Directory UI) → Users browse/select books
- `/profile` (User Memory UI) → Displays stored goals, past conversations
- `/settings` (Settings UI) → Allows customization of chat experience

---

## 2️⃣ Backend Development (FastAPI or Next.js API Routes)
### 🔹 API Endpoints & Business Logic
- `POST /api/auth/login` → Handles user authentication (Supabase Auth)
- `GET /api/books` → Fetches available AI-powered books
- `POST /api/chat` → Handles AI chatbot interactions
- `GET /api/user-memory` → Retrieves stored user habits/goals
- `POST /api/user-memory` → Saves/updates user habits/goals

### 🔹 Chatbot Logic
1. **User sends a message** → API receives it
2. **Retrieve relevant book sections** (from Pinecone vector search)
3. **Pass book context + user query to GPT-4 Turbo**
4. **GPT-4 generates a response** in James Clear’s style
5. **Response is returned to frontend** (chat UI updates)
6. **User conversation is stored** in Supabase (for memory)

---

## 3️⃣ AI Logic (OpenAI GPT-4 Turbo + Pinecone)
### 🔹 AI Workflow
1. **User asks a question** ("How do I build a habit of reading?")
2. **System searches Pinecone** for related *Atomic Habits* concepts
3. **GPT-4 Turbo generates a structured response** based on retrieved text
4. **AI responds like James Clear** (habit stacking, identity-based habits, etc.)
5. **User’s habits/goals are updated** (optional, stored in Supabase)

### 🔹 Example API Request to GPT-4 Turbo
```python
import openai

def chat_with_ai(user_query, context):
    prompt = f"""
    You are James Clear, author of Atomic Habits. The user is asking:
    "{user_query}"
    Based on the book, provide a structured, actionable response.

    Relevant book sections:
    {context}
    """
    
    response = openai.ChatCompletion.create(
        model="gpt-4-turbo",
        messages=[{"role": "system", "content": prompt}]
    )
    
    return response["choices"][0]["message"]["content"]
```

---

## 5️⃣ Persistent Memory (Supabase for Long-Term Storage)
### 🔹 How We Store User Memory
- **User’s Goals & Habits** → Stored in Supabase (`user_memory` table)
- **Recent Conversations** → Stored in Supabase (`chat_history` table)
- **Long-Term Memory** → Stored in Pinecone as embeddings (retrieved on demand)

### 🔹 Example Supabase Table Schema
```sql
CREATE TABLE user_memory (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    goals TEXT[],
    habits TEXT[],
    last_updated TIMESTAMP DEFAULT NOW()
);

CREATE TABLE chat_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    message TEXT,
    ai_response TEXT,
    timestamp TIMESTAMP DEFAULT NOW()
);
```

---