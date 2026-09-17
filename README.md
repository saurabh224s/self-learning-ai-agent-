

# Complete Guide: Mem0 Memory Chatbot Using Google Gemini's FREE API

**Zero cost. No credit card. Uses Google's free Gemini API only.**

---

## WHAT YOU NEED FIRST — 2 API Keys (Both Free)

---

## STEP 1 — Get Your FREE Google Gemini API Key

Go to **https://aistudio.google.com/app/apikey**, sign in with your Google account, and click **"Create API key"**. The free tier works immediately — no billing required. [Apideck](https://www.apideck.com/blog/how-to-get-your-gemini-api-key)

Here's the exact click-by-click:

1. Open your browser → go to **https://aistudio.google.com**
2. Sign in with your **Google/Gmail account**
3. On the first visit, accept Google's terms of service → click **"I agree"**
4. In the left sidebar, click **"Get API key"**
5. Click the blue button **"Create API key"**
6. It will say **"Create API key in new project"** → click **"Create API key in new project"**
7. Your key appears — it starts with `AIza` followed by many characters
8. Click the **copy icon** next to the key
9. Open **Notepad** on your PC (press Windows key → type Notepad → press Enter) and paste the key there. Save that file.

> Unlike OpenAI, Google lets you view your key again later from the AI Studio dashboard — so you can always go back to https://aistudio.google.com/app/apikey to see it again if you lose it. [Apideck](https://www.apideck.com/blog/how-to-get-your-gemini-api-key)

> **Free tier limits:** The free (Hobby) tier gives you real usage at no cost. Rate limits on the free tier are approximately 10 requests per minute on Gemini 2.5 Flash — more than enough for personal use. [The AI Agent Index](https://theaiagentindex.com/agents/mem0) [Apideck](https://www.apideck.com/blog/how-to-get-your-gemini-api-key)

---

## STEP 2 — Get Your FREE Mem0 API Key

1. Go to: **https://app.mem0.ai**
2. Click **"Sign up"** → sign up with your Google account or email
3. After logging in, look at the left sidebar → click **"API Keys"**
4. Click **"Create API Key"** or **"Generate Key"**
5. Copy the key — it starts with `m0-`
6. Paste it into that same Notepad file next to your Gemini key

> The free Hobby tier gives you 10,000 memory add requests and 1,000 retrieval calls per month — enough for thousands of personal conversations. [The AI Agent Index](https://theaiagentindex.com/agents/mem0)

---

## STEP 3 — Install Python

1. Go to: **https://www.python.org/downloads/**
2. Click the big yellow **"Download Python 3.x.x"** button
3. The installer downloads. **Double-click it** to run it.
4. ⚠️ **CRITICAL:** At the bottom of the installer, check the box that says **"Add Python to PATH"**. If you miss this, nothing works.
5. Click **"Install Now"**
6. Click **"Close"** when done

**Verify it worked:**
1. Press **Windows key** → type `cmd` → press **Enter**
2. In the black window, type: `python --version` → press **Enter**
3. You should see: `Python 3.x.x` ✅

---

## STEP 4 — Install VS Code

1. Go to: **https://code.visualstudio.com/**
2. Click **"Download for Windows"**
3. Run the installer → keep clicking **Next**
4. On the options screen, **check all the boxes** you see (especially "Add to PATH")
5. Click **Install** → click **Finish**
6. VS Code opens automatically

**Install the Python extension inside VS Code:**
1. Click the **grid/squares icon** on the left sidebar (Extensions)
2. In the search box, type: `Python`
3. Click **Install** on the first result (by Microsoft)

---

## STEP 5 — Create Your Project Folder

1. Press **Windows + E** to open File Explorer
2. Go to your **Desktop**
3. Right-click → **New** → **Folder**
4. Name it: `gemini-memory-chat` → press **Enter**

---

## STEP 6 — Open the Folder in VS Code

1. In VS Code → click **File** → **Open Folder**
2. Click on `gemini-memory-chat` → click **"Select Folder"**
3. If it asks "Do you trust the authors?" → click **"Yes, I trust the authors"**

---

## STEP 7 — Create the `.env` File (Your Keys)

1. In VS Code's left panel, click the **New File icon** (page with a + sign)
2. Type: `.env` → press **Enter**
3. In the file, type this — replacing the placeholder text with your actual keys:

```
GEMINI_API_KEY=AIza-your-actual-gemini-key-here
MEM0_API_KEY=m0-your-actual-mem0-key-here
```

**Example of what it looks like with real keys:**
```
GEMINI_API_KEY=AIzaSyBxyz1234abcdefghij5678KLMN
MEM0_API_KEY=m0-abc123def456ghi789xyz
```

4. Press **Ctrl + S** to save

---

## STEP 8 — Create the `requirements.txt` File

1. Click **New File icon** again
2. Name it: `requirements.txt` → press **Enter**
3. Paste this:

```
google-genai
mem0ai
python-dotenv
```

4. Press **Ctrl + S** to save

---

## STEP 9 — Create the Main Chatbot File

1. Click **New File icon** again
2. Name it: `chatbot.py` → press **Enter**
3. Paste ALL of this code into it:

```python
import os
from google import genai
from mem0 import Memory
from dotenv import load_dotenv

# Load API keys from .env file
load_dotenv()

# Set Gemini API key as environment variable (required by Mem0's Gemini provider)
os.environ["GEMINI_API_KEY"] = os.getenv("GEMINI_API_KEY")

# Initialize Google Gemini client
gemini_client = genai.Client(api_key=os.getenv("GEMINI_API_KEY"))

# Configure Mem0 to use Gemini for BOTH the LLM and the embedding model
# This means you need ZERO other API keys — Gemini handles everything
config = {
    "embedder": {
        "provider": "gemini",
        "config": {
            "model": "models/text-embedding-004",
        }
    },
    "llm": {
        "provider": "gemini",
        "config": {
            "model": "gemini-2.5-flash",
            "temperature": 0.0,
            "max_tokens": 2000,
        }
    },
    "vector_store": {
        "config": {
            "embedding_model_dims": 768,
        }
    }
}

# Initialize memory with Gemini config
memory = Memory.from_config(config)

# Your user ID — this is how Mem0 knows whose memories to retrieve
USER_ID = "my_user"

SYSTEM_PROMPT = (
    "You are a helpful and friendly AI assistant with long-term memory. "
    "You remember things the user has told you across previous conversations "
    "and use that context to give more personal and relevant answers."
)


def chat_with_memory(history: list, user_message: str) -> str:
    """Send a message, fetch relevant memories, get Gemini response, save memory."""

    # Step 1: Search Mem0 for memories relevant to this message
    relevant_memories = memory.search(query=user_message, user_id=USER_ID, limit=5)
    memories_str = ""
    if relevant_memories and relevant_memories.get("results"):
        memories_str = "\n".join(
            f"- {entry['memory']}" for entry in relevant_memories["results"]
        )

    # Step 2: Build system prompt — inject memories if any exist
    if memories_str:
        full_system_prompt = (
            f"{SYSTEM_PROMPT}\n\n"
            f"Relevant memories about this user:\n{memories_str}"
        )
    else:
        full_system_prompt = SYSTEM_PROMPT

    # Step 3: Add user message to history (Gemini format uses 'user' and 'model')
    history.append({"role": "user", "parts": [{"text": user_message}]})

    # Step 4: Call Gemini with full conversation history
    response = gemini_client.models.generate_content(
        model="gemini-2.5-flash",
        contents=history,
        config={"system_instruction": full_system_prompt}
    )

    assistant_reply = response.text

    # Step 5: Add Gemini's reply to history
    history.append({"role": "model", "parts": [{"text": assistant_reply}]})

    # Step 6: Save this exchange to Mem0 for future sessions
    # Convert history to Mem0's expected format
    messages_for_mem0 = []
    for i, turn in enumerate(history):
        role = "user" if turn["role"] == "user" else "assistant"
        messages_for_mem0.append({
            "role": role,
            "content": turn["parts"][0]["text"]
        })
    memory.add(messages_for_mem0, user_id=USER_ID)

    return assistant_reply, history


def show_all_memories():
    """Print everything Mem0 has stored about the user."""
    print("\n--- Everything I Remember About You ---")
    all_memories = memory.get_all(user_id=USER_ID)
    if all_memories and all_memories.get("results"):
        for i, mem in enumerate(all_memories["results"], 1):
            print(f"{i}. {mem['memory']}")
    else:
        print("Nothing stored yet.")
    print("---------------------------------------\n")


def main():
    print("=" * 52)
    print("   Gemini AI Chatbot with Persistent Memory")
    print("         (Powered by Gemini + Mem0 - FREE)")
    print("=" * 52)
    print("Commands:")
    print("  'memories'  → See what I remember about you")
    print("  'quit'      → Exit the chatbot")
    print("-" * 52)

    history = []

    while True:
        user_input = input("\nYou: ").strip()

        if not user_input:
            continue

        if user_input.lower() in ["quit", "exit", "bye"]:
            print("\nGemini: Goodbye! I'll remember our conversation next time.")
            break

        if user_input.lower() == "memories":
            show_all_memories()
            continue

        print("\nGemini: ", end="", flush=True)
        reply, history = chat_with_memory(history, user_input)
        print(reply)


if __name__ == "__main__":
    main()
```

4. Press **Ctrl + S** to save

**Your folder now has 3 files:**
```
gemini-memory-chat/
├── .env
├── chatbot.py
└── requirements.txt
```

---

## STEP 10 — Open the Terminal in VS Code

1. In VS Code, click **Terminal** in the top menu → click **New Terminal**
2. A dark panel opens at the **bottom** of VS Code
3. You should see your folder path there, like: `PS C:\Users\YourName\Desktop\gemini-memory-chat>`

---

## STEP 11 — Install the Libraries

In the terminal at the bottom, type this **exactly** and press **Enter**:

```
pip install -r requirements.txt
```

You will see a lot of text scrolling. This is normal — it's downloading everything needed. Wait until it stops and you see `Successfully installed ...`

This takes 2–4 minutes.

> If you see `pip is not recognized`, type this instead:
> `python -m pip install -r requirements.txt`

---

## STEP 12 — Run the Chatbot

In the same terminal, type:

```
python chatbot.py
```

Press **Enter**. You will see:

```
====================================================
   Gemini AI Chatbot with Persistent Memory
         (Powered by Gemini + Mem0 - FREE)
====================================================
Commands:
  'memories'  → See what I remember about you
  'quit'      → Exit the chatbot
----------------------------------------------------

You:
```

**It is working.** ✅

---

## HOW TO USE IT — Real Example with Memory

### Session 1 — First conversation:

```
You: Hi! My name is Arjun and I live in Bangalore.

Gemini: Hello Arjun! Great to meet you! Bangalore is a wonderful 
        city — the tech hub of India with great weather. What 
        can I help you with today?

You: I am a software engineer and my favorite food is dosa.

Gemini: That's a great combination! Software engineering and dosas 
        — both require precision and skill! Do you prefer your 
        dosa crispy or soft?

You: Crispy. And I have a pet cat named Mochi.

Gemini: Crispy dosa is the best! And Mochi is such a cute name 
        for a cat. How old is Mochi?

You: quit

Gemini: Goodbye! I'll remember our conversation next time.
```

Now **close VS Code entirely**. The program has stopped.

---

### Session 2 — Reopen VS Code, open the terminal, type `python chatbot.py` again:

```
You: Do you remember anything about me?

Gemini: Yes! You're Arjun, a software engineer living in Bangalore. 
        You love crispy dosas, and you have a cat named Mochi. 
        Is there something specific you'd like to talk about today?
```

**Gemini remembered everything — even after you closed the program.** That is Mem0 working.

---

### Type `memories` at any time to see the full list:

```
You: memories

--- Everything I Remember About You ---
1. User's name is Arjun
2. Lives in Bangalore
3. Works as a software engineer
4. Favorite food is dosa (crispy)
5. Has a cat named Mochi
---------------------------------------
```

---

## Where Are Memories Stored and How Much?

**Where:** Mem0 uses a dual storage architecture that combines vector embeddings with graph databases for comprehensive memory, stored on **Mem0's cloud servers** — not on your PC. You can also see them visually by logging into **https://app.mem0.ai** → your dashboard. [mem0](https://docs.mem0.ai/llms.txt)

**How it stores:** Mem0 does not save your entire conversation word-for-word. It uses Gemini itself to extract only the important facts — so "Hi, my name is Arjun and I like crispy dosas" becomes stored as the compact fact: `User's name is Arjun`, `Likes crispy dosas`. Very space-efficient.

**How much is free:** The free Hobby tier gives you 10,000 memory add requests and 1,000 retrieval calls per month. For a personal chatbot used daily, you would need to have hundreds of conversations every single day to come close to that limit. [The AI Agent Index](https://theaiagentindex.com/agents/mem0)

---

## Common Errors and Fixes

**`ModuleNotFoundError: No module named 'google'`**
→ Run `pip install -r requirements.txt` again in the terminal.

**`API_KEY_INVALID` or `AuthenticationError`**
→ Your key in `.env` is wrong. Go back to https://aistudio.google.com/app/apikey, copy the key again, and paste it carefully into `.env`. Make sure there are no spaces before or after the key.

**`python is not recognized`**
→ Python wasn't added to PATH. Reinstall Python from python.org and check the "Add Python to PATH" box.

**`pip is not recognized`**
→ Use `python -m pip install -r requirements.txt` instead.

**The program runs but gives an error about Mem0 key**
→ Check your `.env` file. The Mem0 key line must start with `MEM0_API_KEY=` exactly.

---

## Quick Reference

| What you want to do | What to type |
|---|---|
| Start the chatbot | `python chatbot.py` |
| See all stored memories | Type `memories` while chatbot is running |
| Stop the chatbot | Type `quit` |
| Reinstall libraries | `pip install -r requirements.txt` |
| Get your Gemini key again | Visit https://aistudio.google.com/app/apikey |
