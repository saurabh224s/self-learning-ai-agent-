# self-learning-ai-agent-



# Complete Beginner's Guide: Running the Mem0 AI Memory Chatbot on Your PC

**What this project does:** It builds a chatbot that *remembers* things you tell it across multiple conversations — powered by OpenAI (ChatGPT) for intelligence and Mem0 for memory storage. When you close and reopen it, it still knows who you are.

---

## BEFORE YOU START — What You Need to Get First

You will need **3 things** before touching any code:

1. **An OpenAI API Key** (costs a small amount of money — $5 credit lasts a long time)
2. **A Mem0 API Key** (free tier gives you 10,000 memories/month — more than enough)
3. **A computer running Windows** (this guide assumes Windows; say if you use Mac)

---

## PHASE 1 — Get Your API Keys

### Step 1: Get Your OpenAI API Key

1. Open your browser. Go to: **https://platform.openai.com**
2. Click **"Sign up"** (top right). Create an account with your email.
3. After logging in, click your profile icon (top right) → click **"API keys"**
4. Click the button **"+ Create new secret key"**
5. Give it a name (type anything, like `my-chatbot`) → click **"Create secret key"**
6. You will see a long code starting with `sk-`. **COPY IT NOW** and paste it somewhere safe (Notepad, Notes app). You will NEVER see it again after closing this window.

> **Cost note:** You need to add a small payment method. Go to **Billing** → **Add payment method** → add a card. Add $5 in credits. This project will cost pennies per conversation.

---

### Step 2: Get Your Mem0 API Key

1. Go to: **https://app.mem0.ai**
2. Click **"Sign up"** → create an account with Google or email
3. After logging in, look at the left sidebar → click **"API Keys"**
4. Click **"Create API Key"** or **"Generate Key"**
5. **Copy the key** — it starts with `m0-`. Paste it in that same Notepad file.

> **Free tier:** 10,000 memory add requests + 1,000 retrieval calls per month. Plenty for personal use.

---

## PHASE 2 — Install Python

### Step 3: Install Python on Your PC

Python is the programming language this project runs in. You need it installed first.

1. Go to: **https://www.python.org/downloads/**
2. Click the big yellow button that says **"Download Python 3.x.x"** (whatever the latest 3.x version is)
3. The file downloads (it's about 25MB). Double-click it to open the installer.
4. **CRITICAL — Before clicking Install:** At the bottom of the installer window, you will see a checkbox that says **"Add Python to PATH"**. **CHECK THAT BOX.** This is the #1 mistake beginners make. If you miss this, nothing will work.
5. Click **"Install Now"**
6. Wait for it to finish. Click **"Close"**

**Verify Python installed correctly:**
1. Press the **Windows key** on your keyboard
2. Type `cmd` → press **Enter** (this opens a black Command Prompt window)
3. In the black window, type exactly:
   ```
   python --version
   ```
4. Press **Enter**. You should see something like `Python 3.12.3`. If you see that, Python is installed. ✅

---

## PHASE 3 — Install VS Code

### Step 4: Download and Install VS Code

VS Code is a free code editor where you will write and run the code.

1. Go to: **https://code.visualstudio.com/**
2. Click the big blue **"Download for Windows"** button
3. The installer downloads (about 90MB). Double-click it.
4. Accept the agreement → click **Next** → keep clicking **Next** until you reach a screen with checkboxes
5. **Check the box** that says **"Add to PATH"** and **"Open with Code"** (check all options you see)
6. Click **Install** → wait → click **Finish**
7. VS Code will open automatically

---

### Step 5: Set Up VS Code (One-Time Setup)

When VS Code opens for the first time:

1. You'll see a welcome screen. Close the welcome tab by clicking the **X** on the "Welcome" tab at the top.
2. On the left side, you see a column of icons. Click the one that looks like **4 squares** (Extensions icon — it's the bottom icon in the top group, looks like a grid).
3. In the search box that appears, type: `Python`
4. The first result says **"Python"** by **Microsoft**. Click the blue **"Install"** button next to it.
5. Wait for it to install (30 seconds). Done. ✅

---

## PHASE 4 — Create Your Project Folder

### Step 6: Create a Folder for the Project

1. Open **File Explorer** (the folder icon in your taskbar, or press **Windows + E**)
2. Navigate to your **Desktop** (or Documents — wherever you want)
3. Right-click in an empty area → **New** → **Folder**
4. Name the folder: `mem0-chatbot`
5. Press **Enter**

---

### Step 7: Open the Folder in VS Code

1. Go back to VS Code
2. Click **File** (top left menu) → **Open Folder**
3. Navigate to the `mem0-chatbot` folder you just created on your Desktop
4. Click on it once to select it → click **"Select Folder"**
5. VS Code may ask **"Do you trust the authors of the files in this folder?"** — Click **"Yes, I trust the authors"**
6. You will now see your empty folder in the left panel of VS Code

---

## PHASE 5 — Create the Project Files

You need to create 2 files. Here's exactly what to do.

### Step 8: Create the `.env` File (Your Secret Keys File)

1. In VS Code, look at the left panel — you should see `MEM0-CHATBOT` written at the top
2. Click the **New File** icon (it looks like a page with a + sign, next to the folder name)
3. Type exactly: `.env` (with a dot at the beginning) → press **Enter**
4. A blank file opens in the main editor area. Type the following, replacing the placeholder text with your actual keys:

```
OPENAI_API_KEY=AQ.Ab8RN6LzjfD5e8a31eE2OKhZS0FKrbqmsiv3V9pLUfxVJNA0mw
MEM0_API_KEY=m0-your-actual-mem0-key-here
```

> **Example of what it looks like with real keys:**
> ```
> OPENAI_API_KEY=sk-proj-abc123def456ghi789...
> MEM0_API_KEY=m0-xyz987uvw654rst321...
> ```

5. Press **Ctrl + S** to save the file.

> ⚠️ **This file contains your secret keys. Never share it with anyone. Never upload it to the internet.**

---

### Step 9: Create the Main Python File

1. Click the **New File** icon again in the left panel
2. Type: `chatbot.py` → press **Enter**
3. A blank file opens. Copy and paste ALL of the following code into it:

```python
import os
from openai import OpenAI
from mem0 import MemoryClient
from dotenv import load_dotenv

# Load API keys from .env file
load_dotenv()

# Initialize clients
openai_client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
mem0_client = MemoryClient(api_key=os.getenv("MEM0_API_KEY"))

# Set a user ID — this is how Mem0 identifies who the memories belong to
USER_ID = "my_user"

def chat(user_message, conversation_history):
    """Send a message, retrieve memories, get a response, and save the memory."""

    # Step 1: Search Mem0 for relevant past memories about this topic
    memories = mem0_client.search(user_message, user_id=USER_ID)
    
    # Step 2: Format memories into a string to include in the prompt
    memory_context = ""
    if memories and memories.get("results"):
        memory_list = [m["memory"] for m in memories["results"]]
        memory_context = "\n".join(f"- {m}" for m in memory_list)
    
    # Step 3: Build the system prompt with memory context
    system_prompt = "You are a helpful and friendly AI assistant with memory. You remember things users tell you across conversations."
    
    if memory_context:
        system_prompt += f"\n\nRelevant memories about this user:\n{memory_context}"
    
    # Step 4: Add user message to conversation history
    conversation_history.append({"role": "user", "content": user_message})
    
    # Step 5: Call OpenAI with full conversation + memory context
    messages = [{"role": "system", "content": system_prompt}] + conversation_history
    
    response = openai_client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages,
        temperature=0.7,
    )
    
    assistant_reply = response.choices[0].message.content
    
    # Step 6: Add assistant reply to conversation history
    conversation_history.append({"role": "assistant", "content": assistant_reply})
    
    # Step 7: Save this exchange to Mem0 memory for future sessions
    mem0_client.add(
        [
            {"role": "user", "content": user_message},
            {"role": "assistant", "content": assistant_reply}
        ],
        user_id=USER_ID
    )
    
    return assistant_reply, conversation_history


def main():
    """Main chatbot loop."""
    print("=" * 50)
    print("   AI Chatbot with Persistent Memory (Mem0)")
    print("=" * 50)
    print("This chatbot remembers you across sessions!")
    print("Type 'quit' or 'exit' to stop.")
    print("Type 'memories' to see what the bot remembers about you.")
    print("-" * 50)
    
    conversation_history = []
    
    while True:
        user_input = input("\nYou: ").strip()
        
        if not user_input:
            continue
            
        if user_input.lower() in ["quit", "exit", "bye"]:
            print("\nBot: Goodbye! I'll remember our conversation for next time.")
            break
        
        if user_input.lower() == "memories":
            print("\n--- Your Stored Memories ---")
            all_memories = mem0_client.get_all(user_id=USER_ID)
            if all_memories and all_memories.get("results"):
                for i, mem in enumerate(all_memories["results"], 1):
                    print(f"{i}. {mem['memory']}")
            else:
                print("No memories stored yet.")
            print("----------------------------")
            continue
        
        print("\nBot: ", end="", flush=True)
        reply, conversation_history = chat(user_input, conversation_history)
        print(reply)


if __name__ == "__main__":
    main()
```

4. Press **Ctrl + S** to save.

---

### Step 10: Create the Requirements File

1. Click the **New File** icon again
2. Type: `requirements.txt` → press **Enter**
3. Paste this into it:

```
openai
mem0ai
python-dotenv
```

4. Press **Ctrl + S** to save.

**Your folder should now have 3 files:**
```
mem0-chatbot/
├── .env
├── chatbot.py
└── requirements.txt
```

---

## PHASE 6 — Install the Libraries

### Step 11: Open the Terminal in VS Code

1. In VS Code, click **Terminal** in the top menu bar → click **New Terminal**
2. A black/dark panel opens at the **bottom** of VS Code. This is your terminal.
3. It should show your folder path, something like: `PS C:\Users\YourName\Desktop\mem0-chatbot>`

---

### Step 12: Install the Required Libraries

In the terminal at the bottom of VS Code, type this command **exactly** and press **Enter**:

```
pip install -r requirements.txt
```

You will see a lot of text scrolling — this is normal. It's downloading and installing the libraries. Wait until you see something like `Successfully installed ...` and the cursor stops.

This takes 1–3 minutes depending on your internet speed.

> **If you see an error saying `pip is not recognized`:** Type `python -m pip install -r requirements.txt` instead and press Enter.

---

## PHASE 7 — Run the Chatbot

### Step 13: Run the Program

In the same terminal at the bottom of VS Code, type:

```
python chatbot.py
```

Press **Enter**.

You should see:

```
==================================================
   AI Chatbot with Persistent Memory (Mem0)
==================================================
This chatbot remembers you across conversations!
Type 'quit' or 'exit' to stop.
Type 'memories' to see what the bot remembers about you.
--------------------------------------------------

You: 
```

**It's working.** ✅

---

## PHASE 8 — How to Use It (With a Real Example)

Here is an actual example conversation showing how the memory works:

---

**SESSION 1 — First time you run it:**

```
You: Hi! My name is Priya and I live in Mumbai.

Bot: Hi Priya! Great to meet you! Mumbai is such a vibrant city. 
     How can I help you today?

You: I am a software engineer and I love cooking Indian food.

Bot: That's wonderful! Software engineering and cooking are both 
     creative in their own ways. Do you have a favorite dish you 
     like to make?

You: My favorite dish is butter chicken. I also have a dog named Bruno.

Bot: Butter chicken is a classic! And Bruno sounds adorable. 
     What breed is he?

You: quit

Bot: Goodbye! I'll remember our conversation for next time.
```

Now **close the terminal**. The program stopped.

---

**Run it again** by typing `python chatbot.py` and pressing Enter:

**SESSION 2 — Next time you run it (new session, but with memory):**

```
You: What do you know about me?

Bot: I remember a few things about you, Priya! You're a software 
     engineer living in Mumbai, you love cooking Indian food — 
     especially butter chicken — and you have a dog named Bruno. 
     Is there anything else you'd like to share or talk about?
```

**The bot remembered everything from the previous session** — even though you closed and reopened the program. That's the Mem0 memory layer working.

---

**To see all stored memories at any time**, type:

```
You: memories
```

Output:
```
--- Your Stored Memories ---
1. User's name is Priya
2. Lives in Mumbai
3. Works as a software engineer
4. Loves cooking Indian food
5. Favorite dish is butter chicken
6. Has a dog named Bruno
----------------------------
```

---

## Where Is Memory Stored? How Much?

The free (Hobby) tier gives you **10,000 memory add requests** and **1,000 retrieval calls per month**. For personal use, this is enormous — you would need to have thousands of conversations per month to hit that limit. [The AI Agent Index](https://theaiagentindex.com/agents/mem0)

**Where the memories physically live:** They are stored **in Mem0's cloud servers** (not on your PC). Mem0 combines vector embeddings with database storage for comprehensive memory. You can see all your stored memories by logging into **https://app.mem0.ai** → your dashboard → you'll see a visual list of everything the bot has learned about you. [mem0](https://docs.mem0.ai/llms.txt)

**How much memory is stored per conversation:** Mem0 doesn't store the entire conversation word-for-word. It uses an LLM to extract key facts and preferences, storing them as compressed memory entries. So "Hi, I'm Priya, I'm a software engineer in Mumbai with a dog named Bruno who loves butter chicken" gets broken down into compact facts like: `name: Priya`, `location: Mumbai`, `profession: software engineer`, `pet: dog named Bruno`, `food preference: butter chicken`. Each fact is a separate memory entry — very space-efficient. [GitHub](https://github.com/mem0ai/mem0)

---

## Common Errors and How to Fix Them

**Error: `ModuleNotFoundError: No module named 'openai'`**
→ You skipped Step 12. Run `pip install -r requirements.txt` again.

**Error: `AuthenticationError` or `Invalid API key`**
→ Your API key in `.env` is wrong. Open `.env`, double-check you copied the full key with no spaces.

**Error: `openai.RateLimitError`**
→ You haven't added billing/credits to your OpenAI account. Go to https://platform.openai.com/settings/billing and add $5.

**Error: `.env` file not loading / keys not found**
→ Make sure the `.env` file is in the same folder as `chatbot.py`. Open the terminal and verify with: `dir` (Windows) — you should see both files listed.

**The terminal says `python is not recognized`**
→ Python wasn't added to PATH. Reinstall Python from python.org and make sure you check the "Add Python to PATH" box during installation.

---

## Quick Reference Card

| Action | Command in Terminal |
|--------|-------------------|
| Start the chatbot | `python chatbot.py` |
| See stored memories | Type `memories` while chatbot is running |
| Stop the chatbot | Type `quit` or `exit` |
| Reinstall libraries | `pip install -r requirements.txt` |
