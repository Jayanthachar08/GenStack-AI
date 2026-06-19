# AGENT.md — GenStack AI

> **System Design & Agent Instruction Document**  
> This file defines the architecture, product model, and development rules for the GenStack AI presentation SaaS. Every agent or contributor working on this codebase should read this before making changes.

---

## 1. Project Identity

| Field | Value |
|-------|-------|
| **Name** | GenStack AI |
| **Tagline** | From blank page to boardroom deck. |
| **Type** | AI-powered presentation SaaS (web application) |
| **MVP Goal** | Prompt → Outline → Slides → Edit → Export (PPTX/PDF) |
| **Target Users** | Students, founders, marketers, sales teams, consultants, agencies, HR/training |
| **Differentiation** | Structure-first generation, slide-level editing, brand kit support, professional export |

---

## 2. High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      CLIENT (Next.js 14+)                     │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   Prompt     │  │   Editor     │  │   Export /        │  │
│  │   Input      │  │   Workspace  │  │   Preview         │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
│  Tailwind CSS • HeroUI • Thesys/Crayon • React Aria           │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     API LAYER (tRPC / REST)                   │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   Auth       │  │   Deck CRUD  │  │   AI Orchestration  │  │
│  │   (Clerk)    │  │   (Prisma)   │  │   (GPT-4 / Claude)  │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   Brand Kit  │  │   Collaboration│  │   Export Engine     │  │
│  │   Service    │  │   (WebSockets) │  │   (PPTX / PDF)      │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
└────────────────────────────┬──────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     DATA & AI SERVICES                        │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   PostgreSQL │  │   AI Models  │  │   Object Storage    │  │
│  │   (Prisma)   │  │   (OpenAI)   │  │   (S3 / R2)         │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────────────┐  │
│  │   Redis      │  │   Queue      │  │   Analytics         │  │
│  │   (Cache)    │  │   (BullMQ)   │  │   (PostHog)         │  │
│  └──────────────┘  └──────────────┘  └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Core Data Model

### 3.1 User
```
User
├── id (UUID)
├── email
├── name
├── avatarUrl
├── plan (free | pro | team | enterprise)
├── brandKitId (optional)
├── createdAt
└── updatedAt
```

### 3.2 Workspace (Team)
```
Workspace
├── id (UUID)
├── name
├── ownerId (User)
├── members[] (User + role)
├── templates[] (Template)
├── brandKit (BrandKit)
├── createdAt
└── updatedAt
```

### 3.3 Deck
```
Deck
├── id (UUID)
├── title
├── objective (AI-generated deck goal)
├── outline (JSON array of slide summaries)
├── type (pitch | sales | marketing | training | report | academic | product_launch | investor_update)
├── tone (formal | casual | persuasive | technical | playful)
├── audience (string)
├── status (draft | published | archived)
├── slides[] (Slide)
├── brandKit (BrandKit snapshot)
├── versionHistory[] (Version)
├── collaborators[] (User + permission)
├── createdAt
└── updatedAt
```

### 3.4 Slide
```
Slide
├── id (UUID)
├── deckId (Deck)
├── order (integer)
├── layout (title | title_content | two_column | image_text | chart | quote | timeline | comparison | data_table | closing)
├── title
├── content (JSON — bullets, paragraphs, etc.)
├── visualSuggestion (image | chart | icon | diagram | none)
├── speakerNotes (text)
├── isLocked (boolean — prevents AI regen)
├── isGenerated (boolean)
├── createdAt
└── updatedAt
```

### 3.5 BrandKit
```
BrandKit
├── id (UUID)
├── name
├── ownerId / workspaceId
├── colors
│   ├── primary
│   ├── secondary
│   ├── accent
│   ├── background
│   └── text
├── fonts
│   ├── heading
│   └── body
├── logoUrl
├── logoPosition (top-left | top-right | bottom-left | bottom-right | none)
├── templateDefaults (JSON)
├── createdAt
└── updatedAt
```

### 3.6 Template
```
Template
├── id (UUID)
├── name
├── category (startup | sales | marketing | academic | training | report | minimalist | bold | creative)
├── thumbnailUrl
├── colorPalette (JSON)
├── fontPair (JSON)
├── slideLayouts[] (Layout)
├── isPremium (boolean)
├── createdAt
└── updatedAt
```

---

## 4. AI Orchestration Pipeline

### Step 1: Intake Parsing
```
Input → Parse → Structured Brief
```
- Raw prompt → extract topic, audience, goal, duration
- PDF/DOCX → extract text, headings, bullet points
- URL → fetch and summarize key content
- Notes → structure into logical flow

### Step 2: Outline Generation
```
Structured Brief → AI Outline Engine → Deck Outline
```
- Generate title and objective
- Create section structure (3–5 sections)
- Recommend slide count (8–20)
- Define story flow (hook → problem → solution → proof → ask)
- Output: JSON array of slide objects with `type`, `title`, `content_summary`

### Step 3: Slide Generation
```
Deck Outline → Per-Slide AI → Slide Content + Design
```
- For each outline item:
  - Select optimal layout based on content type
  - Generate headline (concise, punchy)
  - Generate supporting points (3–5 bullets max)
  - Suggest visual (chart, image, icon, diagram)
  - Write speaker notes (30–60 seconds of speaking time)
- Apply template colors and typography
- Respect brand kit if present

### Step 4: Export
```
Slide Objects → Export Engine → .pptx / .pdf
```
- Render slides to structured PPTX (python-pptx)
- Embed fonts, images, charts
- Generate PDF via headless browser or direct conversion
- Apply brand colors and logo positioning

---

## 5. API Contract (tRPC Routers)

### 5.1 `deck.create` — Create Deck from Prompt
```ts
deck.create({
  input: {
    source: "prompt" | "pdf" | "docx" | "url" | "notes",
    content: string,
    deckType: "pitch" | "sales" | "marketing" | ...,
    tone: "formal" | "casual" | "persuasive" | ...,
    audience?: string,
    duration?: number, // minutes
    templateId?: string,
    brandKitId?: string,
  }
}) → { deckId, outline, slides[] }
```

### 5.2 `slide.regenerate` — Regenerate Single Slide
```ts
slide.regenerate({
  input: {
    slideId: string,
    tone?: string,
    length?: "shorter" | "longer" | "same",
    layout?: string,
    visualType?: string,
  }
}) → { updatedSlide }
```

### 5.3 `deck.export` — Export Deck
```ts
deck.export({
  input: {
    deckId: string,
    format: "pptx" | "pdf",
    includeSpeakerNotes?: boolean,
  }
}) → { downloadUrl }
```

### 5.4 `brandKit.create` — Create Brand Kit
```ts
brandKit.create({
  input: {
    name: string,
    colors: { primary, secondary, accent, background, text },
    fonts: { heading, body },
    logoUrl?: string,
  }
}) → { brandKitId }
```

### 5.5 `collaboration.comment` — Add Comment
```ts
collaboration.comment({
  input: {
    slideId: string,
    text: string,
    parentId?: string, // thread reply
  }
}) → { commentId }
```

---

## 6. Frontend Architecture

### 6.1 Route Structure
```
/                          → Landing page
/auth/signin               → Authentication
/dashboard                 → User dashboard (all decks)
/deck/new                  → New deck creation (prompt input)
/deck/[id]/outline         → Review & edit outline before generation
/deck/[id]/editor          → Slide editor workspace
/deck/[id]/export          → Export preview & download
/brand-kit                 → Brand kit management
/templates                 → Template gallery
/settings                  → Account & workspace settings
```

### 6.2 Editor Workspace Layout
```
┌────────────────────────────────────────────────────────────────┐
│  Toolbar: Title | Undo/Redo | Export | Share | Brand Kit       │
├────────────────────┬──────────────────────┬──────────────────────┤
│  Slide Thumbnails │   Slide Canvas      │   Properties Panel  │
│  (left sidebar)    │   (center stage)    │   (right sidebar)    │
│  - reorderable     │   - WYSIWYG editor  │   - Text edit        │
│  - regenerate btn  │   - click to edit   │   - Layout picker    │
│  - lock toggle     │   - visual preview  │   - Visual options   │
│  - comments badge  │                     │   - Speaker notes    │
│                    │                     │   - AI suggestions   │
└────────────────────┴──────────────────────┴──────────────────────┘
```

### 6.3 Component Hierarchy
```
App
├── Layout (auth guard, nav, sidebar)
│   ├── LandingPage
│   ├── DashboardPage
│   ├── NewDeckPage
│   │   └── PromptInput → UploadZone → SourceSelector
│   ├── OutlinePage
│   │   └── OutlineEditor → SectionList → SlidePreview
│   └── EditorPage
│       ├── EditorToolbar
│       ├── SlideSidebar
│       │   └── SlideThumbnailList
│       ├── SlideCanvas
│       │   └── SlideRenderer (layout-aware)
│       └── PropertiesPanel
│           ├── TextEditor
│           ├── LayoutPicker
│           ├── VisualOptions
│           └── SpeakerNotes
├── ExportPage
│   └── ExportPreview → DownloadButton
└── BrandKitPage
    └── BrandKitEditor → ColorPicker → FontSelector → LogoUploader
```

---

## 7. State Management

### 7.1 Global State (Zustand)
```ts
interface AppState {
  user: User | null;
  currentDeck: Deck | null;
  currentSlide: Slide | null;
  selectedTemplate: Template | null;
  brandKit: BrandKit | null;
  isGenerating: boolean;
  editorMode: "edit" | "preview" | "present";
}
```

### 7.2 Editor State (Zustand + Immer)
```ts
interface EditorState {
  slides: Slide[];
  selectedSlideId: string | null;
  history: Slide[][]; // undo stack
  historyIndex: number;
  isDirty: boolean;
  collaborators: User[];
}
```

### 7.3 Server State (TanStack Query / tRPC)
- Deck CRUD operations
- AI generation calls
- Export jobs
- Collaboration events

---

## 8. Design System (Planned)

### 8.1 Tokens
```css
/* Colors */
--color-primary: #0F172A;     /* Slate 900 */
--color-secondary: #3B82F6;   /* Blue 500 */
--color-accent: #8B5CF6;      /* Violet 500 */
--color-background: #F8FAFC;  /* Slate 50 */
--color-surface: #FFFFFF;
--color-text: #1E293B;        /* Slate 800 */
--color-text-muted: #64748B;  /* Slate 500 */

/* Typography */
--font-heading: "Inter", system-ui, sans-serif;
--font-body: "Inter", system-ui, sans-serif;
--font-mono: "JetBrains Mono", monospace;

/* Spacing */
--space-xs: 0.25rem;
--space-sm: 0.5rem;
--space-md: 1rem;
--space-lg: 1.5rem;
--space-xl: 2rem;
--space-2xl: 3rem;

/* Radii */
--radius-sm: 0.375rem;
--radius-md: 0.75rem;
--radius-lg: 1rem;
--radius-xl: 1.5rem;
```

### 8.2 Component Library
- **HeroUI** — base components (Button, Input, Modal, Dropdown, etc.)
- **Thesys / Crayon** — generative UI components for AI-driven interfaces
- **React Aria** — accessibility primitives
- **Custom components:** SlideCanvas, SlideThumbnail, LayoutPicker, BrandKitEditor, ExportPreview

---

## 9. AI Prompt Engineering Rules

### 9.1 Outline Generation Prompt
```
You are a presentation strategist. Given a user brief, create a deck outline.

Rules:
- Always generate a compelling title.
- Define a clear objective (1 sentence).
- Structure into 3–5 sections.
- Each section has 2–5 slides.
- Total slide count: 8–20 (default 12).
- Follow story arc: Hook → Problem → Solution → Proof → Call to Action.
- Adapt structure to deck type (pitch, sales, training, etc.).
- Return JSON only. No markdown outside JSON.
```

### 9.2 Slide Content Prompt
```
You are a slide copywriter. Given a slide outline item, generate slide content.

Rules:
- Headline: max 8 words, punchy, no jargon.
- Supporting points: 3–5 bullets, max 12 words each.
- Visual suggestion: one of [image, chart, icon, diagram, table, none].
- Speaker notes: 30–60 seconds of speaking content.
- Match tone to deck brief.
- Return JSON: { headline, bullets[], visualSuggestion, speakerNotes }
```

### 9.3 Tone Adaptation
```
- formal: professional, structured, authoritative
- casual: conversational, friendly, accessible
- persuasive: benefit-driven, emotional hooks, urgency
- technical: precise, data-rich, jargon-appropriate
- playful: witty, emoji-allowed, unconventional
```

---

## 10. Export Engine Specification

### 10.1 PPTX Export (python-pptx)
- Parse slide objects into python-pptx XML
- Apply template color scheme to slide masters
- Embed fonts and external images
- Position logo per brand kit settings
- Speaker notes in notes_slide
- Chart placeholders → actual charts if data provided

### 10.2 PDF Export
- Render PPTX to HTML (or render canvas)
- Use headless Chrome (Puppeteer/Playwright) to generate PDF
- Maintain vector quality for text and shapes
- 16:9 aspect ratio (13.333" x 7.5")
- Include speaker notes as optional appendix

---

## 11. Collaboration & Realtime

### 11.1 Features
- Presence indicators (who's viewing/editing)
- Live cursor tracking (optional v2)
- Slide-level comments (threads)
- @mentions in comments
- Version history (snapshot every save)
- Approval workflow (submit → review → approve → publish)

### 11.2 Implementation
- WebSocket server (PartyKit or custom WS)
- Operational Transform (OT) or Yjs for conflict resolution
- Presence via Redis Pub/Sub
- Notification system (in-app + email)

---

## 12. Security & Compliance

| Concern | Mitigation |
|---------|------------|
| Auth | Clerk / NextAuth with SSO support |
| Data isolation | Row-level security (RLS) in PostgreSQL |
| File uploads | Virus scan, size limits, type validation |
| AI content | No PII in prompts, sanitize outputs |
| Exports | Watermark for free tier, DRM optional |
| Enterprise | SOC 2 Type II, GDPR compliance, audit logs |

---

## 13. Development Rules for Agents

1. **Never modify locked slides.** Check `slide.isLocked` before any AI regeneration.
2. **Preserve brand consistency.** Always apply active BrandKit colors/fonts to generated content.
3. **Outline before generation.** Never generate slides without a user-approved outline.
4. **Slide count limits.** Respect plan limits (free: 10 decks, pro: 100, team: unlimited).
5. **Export quality first.** PPTX must be editable in PowerPoint, Google Slides, and Keynote.
6. **No AI-initiated file changes without explicit direction.** Per user preference, all changes must be user-approved.
7. **Template system is extensible.** New templates must define: colors, fonts, layouts, and category.
8. **Version everything.** Every save creates a version snapshot. Users can rollback.
9. **Collaboration is opt-in.** Only workspace members can access shared decks.
10. **Accessibility first.** All UI components must pass WCAG 2.1 AA.

---

## 14. File Structure (Planned)

```
genstack-ai/
├── apps/
│   └── web/                    # Next.js frontend
│       ├── src/
│       │   ├── app/            # App Router pages
│       │   ├── components/      # React components
│       │   ├── hooks/           # Custom hooks
│       │   ├── lib/             # Utils, constants
│       │   ├── server/          # tRPC routers
│       │   ├── styles/          # Global CSS, Tailwind
│       │   └── types/           # TypeScript types
│       ├── public/              # Static assets
│       └── package.json
├── packages/
│   ├── ui/                      # Shared UI component library
│   ├── ai-engine/               # AI prompt builders, content gen
│   ├── export-engine/           # PPTX/PDF generation (Python)
│   ├── db/                      # Prisma schema, migrations
│   └── types/                   # Shared TypeScript types
├── ai-prompts/                  # System prompts for AI models
├── templates/                   # Template JSON definitions
├── docs/                        # Documentation
├── README.md
├── AGENT.md
└── package.json                 # Root workspace (Turborepo)
```

---

## 15. Roadmap & Milestones

| Phase | Milestone | Target |
|-------|-----------|--------|
| **Phase 0** | Repo setup, CI/CD, design system | Week 1 |
| **Phase 1** | Prompt → Outline → Basic slides | Week 2–3 |
| **Phase 2** | Slide editor, templates, brand kit | Week 4–5 |
| **Phase 3** | PPTX/PDF export, export quality | Week 6–7 |
| **Phase 4** | Collaboration, comments, sharing | Week 8–9 |
| **Phase 5** | Polish, performance, onboarding | Week 10 |
| **Phase 6** | Launch, analytics, feedback loop | Week 11–12 |

---

## 16. Agent Notes

- **Owner preference:** Never make file changes without explicit direction. Always ask before modifying existing code.
- **Stack:** Next.js, React, TypeScript, Tailwind, HeroUI, Thesys/Crayon, tRPC, Prisma, PostgreSQL.
- **Product:** AI presentation SaaS — not a toy, a real workspace.
- **Vibe:** Fast, premium, simple, smart, collaborative, export-ready.
- **MVP focus:** Prompt → Outline → Slides → Edit → Export. Everything else is v2.

---

*Document version: 1.0*  
*Last updated: June 2026*  
*Maintainer: Agent + Nandan D*
