## IDE

This is a **cloud-based, AI‑powered IDE** you run in the browser.  
It lets you create projects, edit files with a modern code editor, chat with an AI assistant that understands your codebase, and (optionally) run and preview your app – all from a single interface.

### Key Features

- **Projects dashboard** – create and manage multiple projects
- **File explorer** – VSCode‑style tree, create/rename/delete files & folders
- **Code editor** – CodeMirror 6 with syntax highlighting, minimap, and tabs
- **AI suggestions** – inline ghost‑text suggestions while you type
- **Quick Edit (Cmd/Ctrl+K)** – select code and ask AI to refactor/modify it
- **Conversation assistant** – chat UI with knowledge of your project files
- **Background jobs** – long‑running work (AI, GitHub import/export) offloaded to workers
- **(Optional) Preview** – run the project in a WebContainer with a terminal and preview pane

---

## Tech Stack

| Layer | Technologies |
| ----- | ------------ |
| **Framework** | Next.js 16 (App Router), React 19, TypeScript |
| **Styling & UI** | Tailwind CSS 4, shadcn/ui, Radix UI, motion |
| **Editor** | CodeMirror 6 + custom extensions (minimap, quick edit, suggestions) |
| **Database & Realtime** | Convex (schema + queries/mutations + subscriptions) |
| **Auth** | Clerk (JWT + GitHub OAuth) |
| **Background Jobs / Agent** | Inngest + Inngest AgentKit |
| **AI Models** | Vercel AI SDK with Claude (Anthropic) or Gemini (Google) |
| **Preview / Runtime** | WebContainer API, xterm.js (terminal) |
| **State Management** | Zustand, React hooks |
| **Error & Telemetry** | Sentry (server & edge instrumentation) |

---

## High‑Level Architecture

- **Next.js (App Router)** renders the UI and exposes HTTP APIs under `src/app/api/*`.
- **Convex** stores projects, files, conversations, and messages; clients subscribe to updates so edits appear in real time.
- **Inngest** listens to events (e.g. `"message/sent"`, `"github/import"`) and runs background workflows, including the AI agent.
- **Agent tools** (under `src/features/conversations/inngest/tools/*`) let the AI read, create, update, rename, and delete files through Convex.
- **WebContainer** (under `src/features/preview/*`) mirrors the Convex file tree to an in‑browser Linux environment to run dev commands and show output.

Data typically flows like this:

1. User interacts with the UI (open file, type, send message, click quick edit).
2. UI calls a **Next.js API route** or a **Convex mutation/query**.
3. For long‑running or AI work, an **Inngest event** is emitted.
4. Inngest functions call **Convex** and external APIs (AI models, GitHub, Firecrawl).
5. Convex updates broadcast back to all connected clients, updating the editor, explorer, and conversations in real time.

---

## Project Structure

```text
src/
  app/                 # Next.js App Router (pages, layouts, API routes)
  components/          # Shared UI + AI components
    ui/                # shadcn/ui primitives
    ai-elements/       # Chat, code blocks, agent UI
  features/
    auth/              # Auth views (loading, unauthenticated)
    projects/          # Project dashboard, file explorer, GitHub import/export
    editor/            # Code editor, extensions, editor store
    conversations/     # Chat sidebar, hooks, Inngest workflows + tools
    preview/           # WebContainer file tree, terminal, preview UI
  inngest/             # Inngest client and demo functions
  lib/                 # Utilities (Convex client, Firecrawl, helpers)

convex/
  schema.ts            # Convex tables and indexes
  auth.config.ts       # Convex+Clerk integration
  auth.ts              # Auth verification helper
  projects.ts          # Project queries/mutations
  files.ts             # File & folder operations
  conversations.ts     # Conversations & messages
  system.ts            # Internal mutations for Inngest/agent
```

For a deeper, file‑by‑file explanation and learning path, see `PROJECT_GUIDE.md`.

---

## Getting Started

### Prerequisites

- Node.js **20.9+**
- npm or pnpm
- Accounts/keys for:
  - Clerk (auth)
  - Convex (database)
  - One AI provider: Anthropic **or** Google AI Studio
  - (Optional) Firecrawl API key
  - (Optional) Sentry DSN

### Setup

1. **Install dependencies**

   ```bash
   npm install
   ```

2. **Environment variables**

   Create `.env.local` (or copy from `.env.example`) and fill:

   ```env
   # Clerk
   NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
   CLERK_SECRET_KEY=

   # Convex
   NEXT_PUBLIC_CONVEX_URL=
   CONVEX_DEPLOYMENT=
   POLARIS_CONVEX_INTERNAL_KEY=  # random string, used by Inngest internal calls

   # AI Provider (choose one)
   ANTHROPIC_API_KEY=            # Claude
   GOOGLE_GENERATIVE_AI_API_KEY= # Gemini

   # Optional
   FIRECRAWL_API_KEY=
   SENTRY_DSN=
   ```

3. **Run services locally**

   In three separate terminals:

   ```bash
   # 1. Convex dev server
   npx convex dev

   # 2. Next.js dev server
   npm run dev

   # 3. Inngest dev server
   npx inngest-cli@latest dev
   ```

4. **Open the app**

   Visit `http://localhost:3000`, sign in with Clerk, then create your first project.

---

## Core Features in More Detail

- **Projects & Files**
  - Projects are stored in `convex/projects.ts`.
  - Files and folders form a tree in `convex/files.ts`, queried in the file explorer.
  - UI under `src/features/projects/components/file-explorer/*`.

- **Editor**
  - `src/features/editor/components/code-editor.tsx` wraps CodeMirror.
  - `src/features/editor/extensions/*` provide language support, theme, minimap, selection tooltip, AI suggestions, and quick edit.
  - Editor state (open tabs, active file) lives in `use-editor-store`.

- **AI**
  - Suggestion and quick edit APIs: `src/app/api/suggestion/route.ts`, `src/app/api/quick-edit/route.ts`.
  - Conversation API: `src/app/api/messages/*`.
  - Inngest `process-message` uses AgentKit tools to interact with Convex.

- **Preview (WebContainer)**
  - `src/features/preview/hooks/use-webcontainer.ts` mirrors Convex files into WebContainer and runs dev commands.
  - `preview-terminal` and `preview-settings-popover` provide terminal and command configuration UI.

---

## Scripts

```bash
npm run dev      # Start Next.js in development
npm run build    # Build for production
npm run start    # Start production server
npm run lint     # Run ESLint
```

---

## License

This project is provided for educational and experimental use.  
Review the original upstream license if you are using this as a base for commercial work.
