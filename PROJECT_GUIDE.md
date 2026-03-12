# Polaris IDE - Project Guide & Learning Path

## Project Overview

**Polaris** is a cloud-based IDE inspired by Cursor AI, built as part of a YouTube tutorial series. It provides:

- Real-time collaborative code editing
- AI-powered suggestions and quick edit (Cmd+K)
- Conversation-based AI assistant with file management tools
- In-browser code execution via WebContainer
- GitHub import/export
- Multi-file project management

---

## Tech Stack Summary

| Layer | Technologies |
|-------|-------------|
| **Framework** | Next.js 16 (App Router), React 19 |
| **Language** | TypeScript |
| **Styling** | Tailwind CSS 4, shadcn/ui, Radix UI |
| **Database** | Convex (real-time, serverless) |
| **Auth** | Clerk (GitHub OAuth) |
| **Background Jobs** | Inngest |
| **AI** | Vercel AI SDK, Claude (Anthropic) / Gemini (Google) |
| **Code Editor** | CodeMirror 6 |
| **Execution** | WebContainer API, xterm.js |
| **State** | Zustand |
| **Error Tracking** | Sentry |
| **Web Scraping** | Firecrawl |

---

## Learning Path (Recommended Order)

### Phase 1: Foundation (Chapters 1–7)

1. **Project Setup & UI**
   - `package.json`, `next.config.ts`, `tailwind.config.ts`
   - `src/app/layout.tsx`, `src/app/globals.css`
   - `src/components/ui/*` (shadcn components)
   - `components.json` (shadcn config)

2. **Authentication**
   - `convex/auth.config.ts`, `convex/auth.ts`
   - `src/features/auth/*`
   - `src/components/providers.tsx` (Clerk + Convex wiring)

3. **Database**
   - `convex/schema.ts`
   - `convex/projects.ts`, `convex/files.ts`, `convex/conversations.ts`
   - `convex/system.ts` (internal API for Inngest)

4. **Background Jobs**
   - `src/inngest/client.ts`, `src/inngest/functions.ts`
   - `src/app/api/inngest/route.ts`

5. **Firecrawl & Sentry**
   - `src/lib/firecrawl.ts`
   - `sentry.*.config.ts`, `instrumentation*.ts`

6. **Projects Dashboard**
   - `src/app/page.tsx`
   - `src/features/projects/components/*`

### Phase 2: File System & Editor (Chapters 8–10)

7. **IDE Layout**
   - `src/app/projects/[projectId]/layout.tsx`
   - `src/features/projects/components/project-id-layout.tsx`
   - `allotment` (resizable panes)

8. **File Explorer**
   - `src/features/projects/components/file-explorer/*`
   - `src/features/projects/hooks/use-files.ts`
   - `convex/files.ts`

9. **Code Editor**
   - `src/features/editor/*`
   - `src/features/editor/extensions/*`
   - `src/features/editor/store/use-editor-store.ts`

### Phase 3: AI Features (Chapters 11–12)

10. **AI Suggestions & Quick Edit**
    - `src/app/api/suggestion/route.ts`
    - `src/app/api/quick-edit/route.ts`
    - `src/features/editor/extensions/suggestion/*`
    - `src/features/editor/extensions/quick-edit/*`

11. **Conversation System**
    - `src/app/api/messages/route.ts`
    - `src/features/conversations/*`
    - `src/features/conversations/inngest/process-message.ts`
    - `src/features/conversations/inngest/tools/*`

### Phase 4: Advanced (Chapters 13–16)

12. **AI Agent Tools** – file read/write/create/delete via Inngest
13. **WebContainer & Preview** – `src/features/preview/*`
14. **GitHub Import/Export** – `src/app/api/github/*`, `src/features/projects/inngest/*`
15. **AI Project Creation** – `src/app/api/projects/create-with-prompt/route.ts`

---

## File-by-File Reference

### Root & Config

| File | Purpose |
|------|---------|
| `package.json` | Dependencies, scripts |
| `next.config.ts` | Next.js config (Sentry, transpilation) |
| `postcss.config.mjs` | PostCSS for Tailwind |
| `components.json` | shadcn/ui configuration |
| `eslint.config.mjs` | ESLint rules |
| `.env.example` | Environment variable template |

### App Router (`src/app/`)

| File | Purpose |
|------|---------|
| `layout.tsx` | Root layout, fonts (Inter, IBM Plex Mono), providers |
| `page.tsx` | Home page → `ProjectsView` |
| `globals.css` | Global styles, Tailwind |
| `global-error.tsx` | Error boundary UI |
| `projects/[projectId]/layout.tsx` | Project layout with resizable panes |
| `projects/[projectId]/page.tsx` | Project IDE page |

### API Routes (`src/app/api/`)

| File | Purpose |
|------|---------|
| `messages/route.ts` | Send message, trigger Inngest `message/sent` |
| `messages/cancel/route.ts` | Cancel message (Inngest cancel event) |
| `suggestion/route.ts` | AI ghost-text suggestions |
| `quick-edit/route.ts` | Cmd+K quick edit |
| `inngest/route.ts` | Inngest webhook handler |
| `github/import/route.ts` | Import repo from GitHub |
| `github/export/route.ts` | Export project to GitHub |
| `github/export/cancel/route.ts` | Cancel export |
| `github/export/reset/route.ts` | Reset export status |
| `projects/create-with-prompt/route.ts` | Create project from AI prompt |

### Convex (`convex/`)

| File | Purpose |
|------|---------|
| `schema.ts` | Tables: projects, files, conversations, messages |
| `auth.config.ts` | Clerk provider config for Convex |
| `auth.ts` | `verifyAuth` helper |
| `projects.ts` | CRUD for projects |
| `files.ts` | CRUD for files (tree structure) |
| `conversations.ts` | CRUD for conversations |
| `system.ts` | Internal mutations for Inngest (no user auth) |

### Inngest (`src/inngest/`)

| File | Purpose |
|------|---------|
| `client.ts` | Inngest client instance |
| `functions.ts` | Demo functions (demo-generate, demo-error) |

### Features

#### Auth (`src/features/auth/`)

| File | Purpose |
|------|---------|
| `unauthenticated-view.tsx` | Login/signup UI |
| `auth-loading-view.tsx` | Loading state |

#### Projects (`src/features/projects/`)

| File | Purpose |
|------|---------|
| `components/projects-view.tsx` | Main projects dashboard |
| `components/projects-list.tsx` | List of projects |
| `components/projects-command-dialog.tsx` | Cmd+K project switcher |
| `components/new-project-dialog.tsx` | Create project modal |
| `components/navbar.tsx` | Top navbar |
| `components/project-id-layout.tsx` | IDE layout shell |
| `components/project-id-view.tsx` | Full project view |
| `components/file-explorer/index.tsx` | File explorer container |
| `components/file-explorer/tree.tsx` | File tree component |
| `components/file-explorer/tree-item-wrapper.tsx` | Single file/folder row |
| `components/file-explorer/create-input.tsx` | Inline create file/folder |
| `components/file-explorer/rename-input.tsx` | Inline rename |
| `components/file-explorer/loading-row.tsx` | Skeleton row |
| `components/file-explorer/constants.ts` | Icons, file types |
| `components/import-github-dialog.tsx` | Import from GitHub |
| `components/export-popover.tsx` | Export to GitHub |
| `components/preview-view.tsx` | WebContainer preview area |
| `hooks/use-projects.ts` | Project queries/mutations |
| `hooks/use-files.ts` | File CRUD, tree building |
| `inngest/import-github-repo.ts` | Import job |
| `inngest/export-to-github.ts` | Export job |

#### Editor (`src/features/editor/`)

| File | Purpose |
|------|---------|
| `components/code-editor.tsx` | CodeMirror wrapper |
| `components/editor-view.tsx` | Editor + tabs + breadcrumbs |
| `components/file-breadcrumbs.tsx` | Path breadcrumbs |
| `components/top-navigation.tsx` | Editor toolbar |
| `extensions/custom-setup.ts` | Base CodeMirror config |
| `extensions/language-extension.ts` | JS, TS, CSS, HTML, etc. |
| `extensions/theme.ts` | One Dark theme |
| `extensions/minimap.ts` | Minimap |
| `extensions/selection-tooltip.ts` | Selection actions |
| `extensions/suggestion/index.ts` | Ghost text extension |
| `extensions/suggestion/fetcher.ts` | Suggestion API call |
| `extensions/quick-edit/index.ts` | Cmd+K extension |
| `extensions/quick-edit/fetcher.ts` | Quick edit API call |
| `hooks/use-editor.ts` | Editor instance, open files |
| `store/use-editor-store.ts` | Zustand store (tabs, active file) |

#### Conversations (`src/features/conversations/`)

| File | Purpose |
|------|---------|
| `components/conversation-sidebar.tsx` | Chat sidebar |
| `components/past-conversations-dialog.tsx` | History dialog |
| `hooks/use-conversations.ts` | Conversation + message hooks |
| `constants.ts` | Default titles, etc. |
| `inngest/process-message.ts` | Main AI processing function |
| `inngest/constants.ts` | System prompts |
| `inngest/tools/read-files.ts` | Read file tool |
| `inngest/tools/list-files.ts` | List files tool |
| `inngest/tools/update-file.ts` | Update file tool |
| `inngest/tools/create-files.ts` | Create files tool |
| `inngest/tools/create-folder.ts` | Create folder tool |
| `inngest/tools/rename-file.ts` | Rename file tool |
| `inngest/tools/delete-files.ts` | Delete files tool |
| `inngest/tools/scrape-urls.ts` | Firecrawl scrape tool |

#### Preview (`src/features/preview/`)

| File | Purpose |
|------|---------|
| `hooks/use-webcontainer.ts` | WebContainer boot, run commands |
| `components/preview-terminal.tsx` | xterm.js terminal |
| `components/preview-settings-popover.tsx` | Install/dev command settings |
| `utils/file-tree.ts` | Build WebContainer file tree from Convex |

### Components

#### UI (`src/components/ui/`)

shadcn/ui primitives: `button`, `input`, `dialog`, `dropdown-menu`, `tabs`, `scroll-area`, `tooltip`, etc. Built on Radix UI.

#### AI Elements (`src/components/ai-elements/`)

| File | Purpose |
|------|---------|
| `conversation.tsx` | Chat container |
| `message.tsx` | Single message (user/assistant) |
| `prompt-input.tsx` | Chat input |
| `code-block.tsx` | Syntax-highlighted code |
| `artifact.tsx` | Code artifact display |
| `model-selector.tsx` | AI model picker |
| `loader.tsx`, `shimmer.tsx` | Loading states |
| `suggestion.tsx` | Suggestion chip |
| `tool.tsx` | Tool call display |
| `plan.tsx`, `task.tsx`, `reasoning.tsx` | Agent UI elements |
| `web-preview.tsx` | Embedded preview iframe |

### Lib & Hooks

| File | Purpose |
|------|---------|
| `src/lib/utils.ts` | `cn()` (clsx + tailwind-merge) |
| `src/lib/firecrawl.ts` | Firecrawl client |
| `src/lib/convex-client.ts` | Convex client for server-side (Inngest) |
| `src/hooks/use-mobile.ts` | Mobile breakpoint hook |

### Instrumentation

| File | Purpose |
|------|---------|
| `instrumentation.ts` | Sentry server init |
| `instrumentation-client.ts` | Sentry client init |
| `sentry.server.config.ts` | Sentry server config |
| `sentry.edge.config.ts` | Sentry edge config |

---

## Data Flow Overview

```
User Action
    ↓
Next.js API Route or Convex Mutation
    ↓
Inngest (for long-running: messages, import, export)
    ↓
Convex (read/write DB)
    ↓
Real-time subscription → UI update
```

### Message Flow

1. User sends message → `POST /api/messages`
2. API creates message in Convex, sends `message/sent` to Inngest
3. Inngest `process-message` runs AgentKit with tools
4. Tools call Convex via `convex-client` (internal key)
5. Assistant response streamed back, stored in Convex
6. UI subscribes to Convex and updates in real time

---

## Key Concepts

- **Convex**: Real-time DB; queries auto-update when data changes
- **Inngest**: Event-driven background jobs; retries, cancellation, steps
- **Clerk + Convex**: `ConvexProviderWithClerk` syncs auth to Convex
- **AgentKit**: Inngest’s agent framework; tools = Convex file operations
- **CodeMirror**: Extensions for suggestions, quick edit, minimap, themes

---

## Running the Project

```bash
# 1. Install
npm install

# 2. Env
cp .env.example .env.local
# Fill in keys for Clerk, Convex, AI, etc.

# 3. Convex
npx convex dev

# 4. Next.js
npm run dev

# 5. Inngest (separate terminal)
npx inngest-cli@latest dev
```

Open http://localhost:3000
