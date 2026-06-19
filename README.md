# GenStack AI

> **From blank page to boardroom deck.**
> The AI presentation workspace that turns ideas, docs, and notes into polished decks instantly.

---

## Overview

**GenStack AI** is a production-grade AI-powered presentation SaaS that transforms rough ideas into professional, export-ready PowerPoint decks in minutes. Unlike basic AI slide generators, GenStack is a full **presentation creation workspace** — handling content structure, intelligent design, slide-level editing, brand consistency, speaker notes, and team collaboration.

---

## Core Product Promise

Users can:

- **Create** a full presentation from a single prompt, notes, PDF, URL, or bullet points
- **Refine** the deck slide by slide with AI assistance
- **Adapt** tone, length, style, and audience on demand
- **Export** to PowerPoint (`.pptx`) and PDF with professional quality
- **Collaborate** with teammates in real time
- **Maintain** brand consistency across every slide via Brand Kits

---

## The Full Presentation Workflow

### 1. Idea Intake
Users can start from:
- A free-text prompt (e.g., *"Pitch deck for an AI wardrobe startup for investors"*)
- Meeting notes or bullet points
- Uploaded PDF or DOCX documents
- A website URL
- A topic + audience, duration, and goal

### 2. Smart Deck Planning
Before generating a single slide, the AI produces:
- **Deck title** and objective
- **Slide outline** with section structure
- **Estimated slide count** and recommended story flow
- **Audience-tailored narrative arc**

This makes the output feel **intentional**, not random.

### 3. Slide Generation Engine
Each slide is generated with:
- **Headline** and supporting bullet points
- **Visual suggestion** (chart, image, icon, table placeholder)
- **Layout selection** optimized for content type
- **Speaker notes** for presenter confidence
- **Adaptive intelligence** for deck type:
  - Startup pitch decks
  - Sales & client decks
  - Marketing proposals
  - Investor updates
  - Training & HR decks
  - Product launch decks
  - Report summaries
  - College & academic projects

### 4. AI Design Layer
Users don’t need to know design. The system automatically applies:
- **Theme consistency** across all slides
- **Typography hierarchy** (H1, H2, body, caption)
- **Spacing, alignment, and grid discipline**
- **Color system** (primary, secondary, accent, neutrals)
- **Icon and image suggestions**
- **Slide balance and visual rhythm**

Think of it as a **design engine with taste**, not a text printer in a tuxedo.

### 5. Edit Like a Real Workspace
- Direct text editing on slides
- Regenerate only one slide at a time
- Change tone (formal → casual → persuasive)
- Shorten or expand content per slide
- Replace visuals and layouts
- Reorder, duplicate, and lock slides
- Lock branded slides so AI won't alter them

### 6. Collaboration for Teams
- Shared workspaces
- Comments on individual slides
- Version history and rollback
- Approval workflows
- Team templates and shared asset libraries
- Brand kits with logo, colors, and fonts

---

## Target User Segments

| Segment | Use Case |
|---------|----------|
| **Students** | Project presentations, thesis defenses, academic posters |
| **Founders** | Investor pitch decks, demo day slides, partner proposals |
| **Marketers** | Campaign decks, content strategy presentations, brand guides |
| **Sales Teams** | Client proposals, product demos, ROI calculators |
| **Consultants** | Strategy reports, workshop decks, executive summaries |
| **Agencies** | Client presentations, creative pitches, campaign reviews |
| **HR / Training** | Onboarding decks, training materials, policy summaries |

---

## MVP Scope (First Production Version)

- [ ] Prompt-to-presentation generation
- [ ] Upload notes / PDF → convert to slides
- [ ] Slide-by-slide editing and regeneration
- [ ] 10–20 professional templates
- [ ] Export to `.pptx` and `.pdf`
- [ ] Brand kit support (logo, colors, fonts)
- [ ] Simple collaboration (comments, share link)
- [ ] History and per-slide re-generation

---

## Premium Features (Post-MVP Roadmap)

- [ ] **Voice-to-deck** — speak your idea, get a deck
- [ ] **URL-to-deck** — paste a link, extract and structure content
- [ ] **Auto chart generation** — upload CSV/Excel, generate data visualizations
- [ ] **AI image generation** — create custom visuals for each slide
- [ ] **Speaker notes & presenter mode** — teleprompter-style delivery
- [ ] **Multilingual deck generation** — generate in one language, translate to many
- [ ] **Team workspace analytics** — usage, engagement, brand compliance insights
- [ ] **Brand compliance checker** — flag off-brand colors, fonts, or layouts
- [ ] **Presentation scoring** — clarity, design quality, and persuasiveness ratings

---

## Differentiation

GenStack stands out from basic AI slide tools by delivering:

| Capability | GenStack AI | Basic AI Tools |
|------------|-------------|----------------|
| Structure before generation | **Deep outline + story flow** | Raw text → slides |
| Slide-level editing | **Granular control + regeneration** | All-or-nothing regen |
| Design consistency | **Theme engine with hierarchy** | Static templates |
| Export quality | **Production-ready PPTX/PDF** | Low-fidelity or image-only |
| Brand themes | **Reusable, lockable Brand Kits** | Manual color picking |
| Deck intelligence | **Type-aware (pitch, sales, report)** | One-size-fits-all |

---

## Monetization Model

| Plan | Target | Features |
|------|--------|----------|
| **Free** | Individual explorers | Limited decks, watermarked exports, basic templates |
| **Pro** | Power users | Full exports, premium templates, Brand Kit, advanced AI |
| **Team** | Small teams | Collaboration, shared assets, admin controls, templates |
| **Enterprise** | Organizations | SSO, custom branding, usage controls, security audits, SLAs |

---

## Example User Flow

1. **User enters prompt:** *"Create a pitch deck for an AI wardrobe startup for investors."*
2. **App generates** outline and story flow with 12 recommended slides.
3. **User selects style:** Modern, Premium, Startup.
4. **AI generates** all slides with titles, bullets, visuals, and speaker notes.
5. **User edits** Slide 3 directly and regenerates Slide 5 only.
6. **User applies** brand colors and uploads a logo via Brand Kit.
7. **User exports** to `.pptx` and `.pdf` for boardroom delivery.

---

## Product Vibe

> **Fast. Premium. Simple. Smart. Collaborative. Export-ready.**

Not a toy generator. A real deck factory with brains. 🧠📊

---

## Tech Stack (Planned)

- **Frontend:** Next.js 14+ (App Router), React, TypeScript, Tailwind CSS
- **UI Components:** HeroUI, Thesys / Crayon generative UI SDK, React Aria
- **State & Backend:** tRPC, Prisma, PostgreSQL
- **AI Layer:** OpenAI GPT-4 / Claude / Gemini for content generation
- **Export Engine:** Python (python-pptx) + headless Chrome (PDF)
- **Auth:** NextAuth.js / Clerk
- **Storage:** AWS S3 / Cloudflare R2 for assets and exports
- **Realtime:** WebSockets or PartyKit for collaboration
- **Deployment:** Vercel (frontend), Railway / Fly.io (backend workers)

---

## Repository

- **GitHub:** [github.com/Nandan-D14/GenStack-AI](https://github.com/Nandan-D14/GenStack-AI)
- **License:** MIT (planned)
- **Status:** Active development — MVP in progress

---

## Contact & Contribution

Built by [Nandan D](https://github.com/Nandan-D14). Open to contributors, feedback, and design partners.

---

*Last updated: June 2026*
