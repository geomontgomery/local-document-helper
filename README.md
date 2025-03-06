# Setting Up Your Own Document AI Helper (Ollama with RAG)

## What This Is
A simple guide to set up an AI helper on your computer that can answer questions about your documents (PDFs, Excel, Word) without sending them to the internet.

## Step 1: Install Ollama
1. Go to [ollama.ai](https://ollama.ai/) and click the big download button
2. Choose Windows and download the installer
3. Run the installer and follow the on-screen instructions
4. When finished, you'll see the Ollama app in your system tray (bottom right corner)

## Step 2: Get a Good Model
1. Open Command Prompt (search for "cmd" in the Start menu)
2. Type: `ollama pull llama3` and press Enter
3. Wait for it to download (this might take a few minutes)

## Step 3: Set Up Document Helper Tools
1. Install Python from [python.org](https://python.org) (click "Download" and select the Windows installer)
2. Open Command Prompt again
3. Install the document helper tools:
   ```
   pip install llama-index pypdf langchain-community
   ```

## Step 4: Create Your Document System
1. Create a new folder called "DocumentAI" on your desktop
2. Inside that folder, create three more folders: "documents", "database", and "scripts"
3. Create a file called "setup.py" in the "scripts" folder with this content:

```python
from llama_index import VectorStoreIndex, SimpleDirectoryReader
import os

# Set up Ollama as our AI
os.environ["OPENAI_API_BASE"] = "http://localhost:11434/v1"
os.environ["OPENAI_API_KEY"] = "ollama"
os.environ["MODEL"] = "llama3"

# Load all documents from the documents folder
documents = SimpleDirectoryReader("../documents").load_data()

# Create a searchable database from the documents
index = VectorStoreIndex.from_documents(documents)

# Save the database so we can use it later
index.storage_context.persist("../database")

print("✅ Your documents are ready to be searched!")
```

## Step 5: Add Your Documents
1. Copy your PDF, Excel, and Word files into the "documents" folder
   - For best results, make sure PDFs have actual text (not just scanned images)
   - Excel and Word documents should work as-is

## Step 6: Set Up the Chat Interface
1. Create a file called "chat.py" in the "scripts" folder with this content:

```python
from llama_index import StorageContext, load_index_from_storage
import os

# Set up Ollama as our AI
os.environ["OPENAI_API_BASE"] = "http://localhost:11434/v1"
os.environ["OPENAI_API_KEY"] = "ollama"
os.environ["MODEL"] = "llama3"

# Load our document database
storage_context = StorageContext.from_defaults(persist_dir="../database")
index = load_index_from_storage(storage_context)

# Create a chat engine that can answer questions
chat_engine = index.as_chat_engine()

print("💬 Ask me anything about your documents! (type 'exit' to quit)")

# Simple chat loop
while True:
    question = input("Question: ")
    if question.lower() == "exit":
        break
    response = chat_engine.chat(question)
    print(f"Answer: {response.response}")
```

## Step 7: Process Your Documents
1. Open Command Prompt
2. Navigate to your scripts folder:
   ```
   cd Desktop\DocumentAI\scripts
   ```
3. Run the setup script:
   ```
   python setup.py
   ```
4. Wait for it to finish (this might take a while for many documents)

## Step 8: Start Chatting With Your Documents
1. In the same Command Prompt window, run:
   ```
   python chat.py
   ```
2. Type your questions about the documents
3. The AI will answer based on what's in your documents!

## Tips
- Add more documents anytime by copying them to the documents folder and running setup.py again
- For better results with large Excel files, consider saving important sheets as CSV files
- If answers seem wrong, try asking in different ways
- Remember, everything runs on your computer - no internet needed!

## Need Help?
If you get stuck, the Ollama community can help at [github.com/ollama/ollama/discussions](https://github.com/ollama/ollama/discussions)
