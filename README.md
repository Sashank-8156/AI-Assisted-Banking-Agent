# Vault Intelligence — AI-Powered Banking Assistant

> A production-grade AI banking assistant that answers financial questions with **document-grounded responses** using the Claude API (Anthropic). Built with React + Vite.

![Vault Intelligence Screenshot](./public/vault-icon.svg)

---

## Features

- **Document-Grounded AI** — All responses are strictly grounded in loaded financial documents. No hallucination.
- **5 Pre-loaded Financial Documents** — Earnings report, loan policy manual, AML compliance guide, rate sheet, and investment portfolio policy.
- **Source Citation** — Every AI response shows which documents were referenced.
- **Focused Context** — Sidebar lets you narrow the AI's context to a single document.
- **Conversation Memory** — Full multi-turn chat with history passed on every request.
- **Suggested Questions** — Chip buttons for common banking queries.
- **Dark Mode** — Automatic via `prefers-color-scheme`.
- **Responsive** — Works on desktop and mobile.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18 + Vite 5 |
| AI Model | Claude Sonnet (Anthropic API) |
| Styling | CSS Modules |
| Fonts | DM Serif Display, Instrument Sans, DM Mono |
| Icons | Tabler Icons |

---

## Getting Started

### Prerequisites

- Node.js 18+
- An [Anthropic API key](https://console.anthropic.com)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/vault-intelligence.git
cd vault-intelligence

# 2. Install dependencies
npm install

# 3. Set up environment variables
cp .env.example .env
# Edit .env and add your Anthropic API key:
# VITE_ANTHROPIC_API_KEY=sk-ant-...

# 4. Start the development server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

### Build for Production

```bash
npm run build
npm run preview
```

---

## Project Structure

```
vault-intelligence/
├── public/
│   └── vault-icon.svg          # Favicon
├── src/
│   ├── components/
│   │   ├── Header.jsx           # Top navigation bar
│   │   ├── Sidebar.jsx          # Document vault sidebar
│   │   ├── Message.jsx          # Chat message bubble
│   │   ├── ThinkingIndicator.jsx # Loading animation
│   │   ├── WelcomeCard.jsx      # Initial welcome state
│   │   ├── SuggestionChips.jsx  # Quick-question buttons
│   │   ├── InputBar.jsx         # Message input + send
│   │   └── *.module.css        # Scoped component styles
│   ├── data/
│   │   └── documents.js         # Financial document content
│   ├── hooks/
│   │   └── useChat.js           # Anthropic API + chat state
│   ├── styles/
│   │   └── global.css           # CSS variables + resets
│   ├── App.jsx                  # Root component
│   ├── App.module.css           # App-level styles
│   └── main.jsx                 # React entry point
├── index.html
├── vite.config.js
├── package.json
├── .env.example
└── README.md
```

---

## How It Works

### Document-Grounded Responses

The system prompt passed to Claude includes the **full content of the selected financial documents**. Claude is instructed to:

1. Only answer based on the provided documents
2. Always cite specific figures, thresholds, and policy names
3. Clearly state when information isn't available in the documents

```js
// src/hooks/useChat.js
const systemPrompt = `You are Vault Intelligence, a precise AI banking assistant.
You ONLY answer questions based on the financial documents provided below...

FINANCIAL DOCUMENTS:
${docContext}  // ← actual document text injected here
`
```

### Context Filtering

Clicking a document in the sidebar narrows the context:

```js
const getDocContext = () => {
  if (activeDocId === 'all') return ALL_DOCS_COMBINED
  return DOCUMENTS[activeDocId]?.content ?? ALL_DOCS_COMBINED
}
```

### Adding Your Own Documents

Edit `src/data/documents.js` to add real documents:

```js
export const DOCUMENTS = {
  myDoc: {
    id: 'myDoc',
    name: 'My Custom Policy',
    meta: 'Internal use',
    iconClass: 'ti-file-text',
    colorClass: 'blue',
    content: `PASTE YOUR DOCUMENT TEXT HERE`
  },
  // ... existing docs
}
```

For production use, replace static document text with a **vector database** (Pinecone, Weaviate) and RAG pipeline to handle large document sets.

---

## Architecture Diagram

```
User Input
    │
    ▼
React UI (Vite)
    │
    ├── Sidebar: doc context selector
    ├── Chat: message history display
    └── InputBar: user query
         │
         ▼
   useChat Hook
         │
    ┌────┴────────────────────────┐
    │  Build prompt:              │
    │  system = instructions      │
    │         + doc context       │
    │  messages = chat history    │
    └────┬────────────────────────┘
         │
         ▼
  Anthropic API
  (claude-sonnet-4)
         │
         ▼
  Parse response
  + detect source docs
         │
         ▼
  Render message
  + source citation tags
```

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `VITE_ANTHROPIC_API_KEY` | Your Anthropic API key from [console.anthropic.com](https://console.anthropic.com) |

> **Security note**: This project calls the Anthropic API directly from the browser (using `anthropic-dangerous-direct-browser-access`). For production deployments, proxy API calls through your own backend server to keep the API key private.

---

## Extending the Project

### Add a Backend Proxy (Recommended for Production)

```
Frontend → Your Express/FastAPI server → Anthropic API
```

This keeps your API key secure and allows:
- Rate limiting per user
- Request logging and analytics
- User authentication

### Add PDF Upload Support

Use a PDF parsing library (e.g., `pdf-parse` on Node.js) to let users upload real documents:

```js
// Parse uploaded PDF → extract text → inject into document context
```

### Add Vector Search (RAG)

For large document sets, use embeddings + vector search:

1. Embed document chunks with `text-embedding-3-small`
2. Store vectors in Pinecone or Weaviate
3. At query time: embed user question → retrieve top-k chunks → send to Claude

---

## License

MIT — feel free to fork, adapt, and build on this.

---

## Author

**Sai Sashank Sudunagunta**  
Senior Software Engineer · AI/ML Systems  
[LinkedIn](https://www.linkedin.com/in/sai-sashank-4272091b6/?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_contact_details%3BnIpsIrwgQLC%2Fz37otwdrqA%3D%3D)
