# Agent Automation Demos - January 2026

Demo resources for the Agent Hooks workshop covering AI-powered automation for flashcard generation, visual diagrams, and job search workflows.

Demo Youtube playlist: [Youtube Playlist](https://www.youtube.com/playlist?list=PLtkF7MTgS0eERVwQ-666sjACZ7tcMRcmq)
## 🎬 Topics Covered

- **Automating Flashcard Generation** — Let the agent do the heavy lifting for your study sessions
- **Agent Hooks Deep Dive** — How they work + how to supercharge automation with custom hooks
- **Visual Diagrams with Mermaid MCP** — Turn your study notes into beautiful diagrams
- **Agentic AI for Job Search**:
  - Search jobs from multiple sites in one place
  - Auto-customize your resume to PDF for each application
  - Autofill job applications

---

## 🔧 MCP Configuration

Add this to your `~/.kiro/settings/mcp.json` (or `.kiro/settings/mcp.json` in your workspace):

```json
{
  "mcpServers": {
    "anki-mcp": {
      "command": "npx",
      "args": [
        "-y",
        "@ankimcp/anki-mcp-server",
        "--stdio"
      ],
      "env": {
        "ANKI_CONNECT_URL": "http://localhost:8765"
      },
      "autoApprove": [
        "addNote",
        "list_decks",
        "findNotes",
        "create_deck",
        "sync",
        "modelNames",
        "mediaActions"
      ]
    },
    "pdf-reader": {
      "command": "npx",
      "args": [
        "-y",
        "@sylphx/pdf-reader-mcp"
      ],
      "disabled": false,
      "autoApprove": [
        "read_pdf"
      ]
    },
    "mermaid": {
      "command": "npx",
      "args": [
        "-y",
        "@peng-shawn/mermaid-mcp-server"
      ],
      "env": {
        "CONTENT_IMAGE_SUPPORTED": "true"
      },
      "disabled": false,
      "autoApprove": [
        "generate"
      ]
    }
  }
}
```

**Prerequisites:**
- Node.js / npm installed

---

## ⚡ Agent Hooks

Place these in `.kiro/hooks/` in your workspace.

### Generate Flashcards

`generate-flashcards.kiro.hook`

```json
{
  "enabled": true,
  "name": "Generate Flashcards",
  "description": "Generate Anki-compatible flashcards from the currently open file following best practices",
  "version": "1",
  "when": {
    "type": "userTriggered"
  },
  "then": {
    "type": "askAgent",
    "prompt": "Generate high-quality Anki-compatible flashcards from the currently open/active file in the editor and use the Anki mcp to generate cards. Below are some best practices for flashcards you can consider. \n\n## CARD RULES (NON-NEGOTIABLE)\n\n1. **ONE fact per card** — if you're testing two things, make two cards\n2. **Answers: 1-5 words** — if longer, you're testing too much\n3. **No yes/no questions** — rephrase as \"What/Why/How/When\"\n4. **No lists in answers** — split into separate cards or use cloze overlaps\n\n## CARD TYPES TO CONSIDER\n\n- DEFINITION → \"What is X?\" / \"X\"\n- TERM → \"What is the term for [description]?\" / \"Term\"\n- FACT → \"What/When/Where/Who [specific question]?\" / \"[brief answer]\"\n- CAUSE → \"Why does X happen?\" / \"Because Y\"\n- EFFECT → \"What happens when X?\" / \"Y occurs\"\n- PROCESS → \"What is the [first/next] step in X?\" / \"Step\"\n- COMPARISON → \"How does X differ from Y?\" / \"[key difference]\"\n- APPLICATION → \"When would you use X?\" / \"[situation]\"\n\n## QUALITY FILTER\n\nBefore outputting, verify each card:\n- Could someone answer this with ONLY the knowledge being tested?\n- Is the answer SHORT? (1-5 words)\n- Is there exactly ONE correct answer?\n- Would this card be useful 6 months from now?\n\nDelete any card that fails these checks.\n\nNow read the currently active file and generate flashcards from its content."
  }
}
```

### Generate Visual Diagrams

Place these in your project folder's .kiro/hooks

`generate-visual-diagrams.kiro.hook`

```json
{
  "enabled": true,
  "name": "Generate Visual Diagrams",
  "description": "Analyzes the currently open file and generates helpful visual learning diagrams using Mermaid",
  "version": "1",
  "when": {
    "type": "userTriggered"
  },
  "then": {
    "type": "askAgent",
    "prompt": "Analyze the content of the currently active/open file in the editor. Your task is to create helpful visual learning diagrams for studying the topics covered in that file.\n\nBEFORE generating any diagrams:\n1. First, ask the user where they want the images saved (get the full folder path)\n2. Wait for their response before proceeding\n\nONCE you have the save location:\n1. Read and analyze the file content to identify key concepts, relationships, and topics\n2. Determine which types of diagrams would be most helpful for learning (flowcharts, comparison diagrams, decision trees, process flows, hierarchy charts, etc.)\n3. Use the Mermaid MCP tool to generate PNG diagrams with:\n   - Clear, descriptive names\n   - White background\n   - Color-coded elements for visual distinction\n   - Emojis where helpful for quick recognition\n4. Save all diagrams to the folder path the user specified\n5. Provide a brief summary of what diagrams were created and why they're useful for studying the material\n\nFocus on creating diagrams that:\n- Highlight key relationships and comparisons\n- Provide decision trees for choosing between options\n- Show process flows or sequences\n- Summarize complex topics visually\n- Would be useful for exam preparation or quick review"
  }
}
```

### Explain Like I'm a Beginner

`explain-like-beginner.kiro.hook`

```json
{
  "enabled": true,
  "name": "Explain Like I'm a Beginner",
  "description": "Ask for a concept and get a simple, beginner-friendly explanation",
  "version": "1",
  "when": {
    "type": "userTriggered"
  },
  "then": {
    "type": "askAgent",
    "prompt": "Ask the user: 'What concept or term would you like me to explain?'\n\nOnce they provide something, explain it in the simplest possible terms as if they have zero background knowledge. Use:\n- Everyday analogies and comparisons\n- Short sentences\n- No jargon (or define any technical terms immediately)\n- Concrete examples from daily life\n- A friendly, encouraging tone\n\nKeep the explanation concise but complete enough that a total beginner would walk away understanding the core idea."
  }
}
```

---

## 📂 Supporting Files

- `flashcard-generation-prompt.md` — Detailed prompt with flashcard best practices
- `flashcard-workflow-prompt.md` — Streamlined workflow prompt for quick card generation

---

## ❓ Questions?

Drop your questions in **#Agent-automation-demos-January-2026** on Discord!
