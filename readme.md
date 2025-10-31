# AI-Powered Telegram Bot for Google Drive Management & RAG

This project features an intelligent Telegram Bot, built entirely on the low-code platform **n8n**, designed to solve the common problem of managing and retrieving documents shared in chat apps. It automates file organization in Google Drive and provides an AI-powered chat interface to query the contents of your documents.

### Main Router Workflow (`main.json`)

This workflow acts as the central "brain" of the bot. It's triggered by all text messages (`message:text`) and uses a Switch node to route user commands like `/index`, `/ask`, and `/help`. Its primary job is to handle synchronous requests and present the user with interactive button choices.

<img width="1169" height="757" alt="main-workflow" src="https://github.com/user-attachments/assets/f7575f5f-df62-4434-af4f-641de67b7f29" />

### Indexing Callback Workflow (`indexing.json`)

This specialized workflow acts as a "listener." It is triggered _only_ by button clicks (`callback_query`) from the main workflow. This allows it to handle slow, asynchronous tasks like indexing a document without blocking the main chat interface.

<img width="1444" height="674" alt="indexing" src="https://github.com/user-attachments/assets/7edbd146-89fd-48b3-ad82-95a3278f2b1f" />

## 🎯 The Problem Solved

In college and professional environments, important documents (notes, reports, articles) are often shared in chat apps like WhatsApp or Telegram. Over time, these files become buried in the chat history, making them nearly impossible to find when you need them most—especially during exams or tight deadlines.

This bot solves that problem by creating a centralized, organized, and searchable knowledge base in your Google Drive, accessible directly from Telegram.

## ✨ Features

- **📤 Smart Upload & Organization:**
  - **Direct Upload:** Send any file to the bot, and it's instantly saved to your main Google Drive.
  - **Automatic Folder Creation:** Send a file with a **caption**, and the bot creates a folder with that name (if it doesn't exist) and saves the file inside.
- **🔍 Intelligent Search:**
  - **Default Search:** Simply type any text, and the bot searches your entire Google Drive for files with that name.
  - **ID-Based Search:** Use the `/id <file name>` command for the same functionality.
- **🧠 AI-Powered Chat (RAG):**
  - **/index <file name>:** Prepares a document for AI chat. The bot searches your Drive and presents an interactive list of matching files for you to confirm, ensuring you index the correct one.
  - **/ask <file name> <question>:** Ask a question about an _already indexed_ document. The bot uses a Retrieval-Augmented Generation (RAG) pipeline to find the relevant context within the file and generate a precise answer.
- **🤖 User-Friendly Interface:**
  - A comprehensive `/help` command to guide users.
  - Interactive buttons for selecting files, making the experience seamless.

## 🛠️ Tech Stack

- **Automation Platform:** [**n8n**](https://n8n.io/)
- **Messaging Interface:** [**Telegram Bot API**](https://core.telegram.org/bots/api)
- **File Storage:** [**Google Drive**](https://www.google.com/drive/)
- **Vector Database:** [**Pinecone**](https://www.pinecone.io/)
- **AI Models:** [**Google Gemini**](https://ai.google.dev/) (for Embeddings and Chat Generation)

## 🏗️ Architecture Overview

This project uses an event-driven, two-workflow architecture to handle different user interactions efficiently:

1.  **Workflow 1: Main Router (`main_workflow.json`)**

    - **Trigger:** `message:text`
    - **Responsibility:** Handles all text-based commands (`/index`, `/ask`, `/id`, `/help`) and default text searches. It's the main "router" that directs user intent. It's also responsible for sending messages with interactive buttons.

2.  **Workflow 2: Callback Catcher (`indexing.json`)**
    - **Trigger:** `callback_query`
    - **Responsibility:** This workflow acts as a "listener." It wakes up _only_ when a user clicks an inline button (e.g., choosing a file to index or chat with). It then performs the corresponding action, like running the expensive indexing pipeline or starting a chat session.

This separation ensures that the bot remains responsive and that different functionalities don't interfere with each other.

## ⚙️ Setup & Installation Guide

Follow these steps to get your own instance of the bot running.

### Prerequisites

- An **n8n** instance (Cloud or self-hosted).
- A **Telegram Bot Token** from [@BotFather](https://t.me/BotFather).
- A **Google Cloud Project** with the Google Drive API enabled and OAuth 2.0 credentials (Client ID & Secret).
- A **Pinecone** account with an API key and an index created (ensure the dimension matches your embedding model, e.g., 768 for Google Gemini).
- A **Google AI API Key** from [Google AI Studio](https://aistudio.google.com/app/apikey).

### Step-by-Step Instructions

1.  **Import Workflows:**

    - In your n8n dashboard, create two new blank workflows.
    - For each one, go to `Workflow > Import from File` and import `main.json` and `indexing.json`.

2.  **Configure Credentials:**

    - In n8n, go to the **Credentials** section.
    - Create new credentials for:
      - **Telegram Bot API:** Paste your bot token.
      - **Google Drive API:** Use your OAuth 2.0 Client ID and Secret.
      - **Pinecone:** Paste your Pinecone API key.
      - **Google AI:** Paste your Google AI API key.

3.  **Link Credentials in Workflows:**

    - Open each workflow.
    - Go through every node that requires a credential (Telegram Trigger, Google Drive, Pinecone, Google AI) and select the credential you just created from the dropdown menu.

4.  **Activate Both Workflows:**
    - For **both** workflows, click the **`Inactive`** toggle at the top right to switch it to **`Active`**. Your bot is now live!

## 📖 Usage Guide

Interact with your bot in Telegram:

- **Upload:** Send a file.
- **Upload to Folder:** Send a file with a caption.
- **Search:** Type part of a filename.
- **Index a File:** `/index <file name>`
- **Ask a Question (in a session):** `/ask What is the main point of this document?`
- **Get Help:** `/help`
