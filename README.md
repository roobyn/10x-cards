# 10xCards

## Table of Contents
- [Project description](#project-description)
- [Tech stack](#tech-stack)
- [Getting started locally](#getting-started-locally)
- [Available scripts](#available-scripts)
- [Project scope](#project-scope)
- [Project status](#project-status)
- [License](#license)

## Project description
10xCards is a web-based flashcard assistant that accelerates spaced-repetition learning. Users paste long-form text (1,000–10,000 characters), have AI propose up to 20 cards with enforced field-length limits, review each suggestion for edits/approval/rejection, or build cards manually. The MVP prioritizes fast setup, AI-assisted and manual workflows, list management (search/sort/pagination), learning sessions powered by an external spaced-repetition library, and detailed logging for analytics. Planned boundaries include no mobile apps, password recovery, cross-account sharing, or imports beyond plain text.

## Tech stack
- **Frontend:** Astro 5 with React 19 for interactive components, TypeScript 5, Tailwind 4, Shadcn/ui, `lucide-react`, `clsx`, `class-variance-authority`, `tailwind-merge`.
- **Backend/infra:** Supabase for PostgreSQL storage, authentication, and logging; GitHub Actions + DigitalOcean for CI/CD and hosting.
- **AI integration:** Openrouter.ai to reach OpenAI/Anthropic/Google models with configurable spending limits.
- **Tools:** Node.js 22.14.0 (`.nvmrc`), Astro CLI, ESLint/Prettier with lint-staged and Husky hooks.

## Getting started locally
1. **Prerequisites**
   - Install [Node.js 22.14.0](https://nodejs.org/) (see `.nvmrc`).
   - Ensure npm (or another compatible package manager) is available.
   - Obtain Supabase credentials and Openrouter.ai API keys.
2. **Clone and install**
   ```bash
   git clone <repository-url>
   cd 10x-cards
   npm install
   ```
3. **Configure the environment**
   - Copy `.env.example` (if available) or create `.env`, supplying Supabase URL/keys, AI endpoint/token, and any other secrets required by the app.
4. **Run the development server**
   ```bash
   npm run dev
   ```
   Astro will hot reload the project as you work on components.

## Available scripts
- `npm run dev` – start Astro in development mode.
- `npm run build` – build the production bundle.
- `npm run preview` – preview the production build locally.
- `npm run astro` – proxy commands to the Astro CLI.
- `npm run lint` – run ESLint over the codebase.
- `npm run lint:fix` – run ESLint with automatic fixes.
- `npm run format` – run Prettier over supported files.

## Project scope
- **AI generation:** Paste source text, specify up to 20 cards, validate text/card lengths, and submit normalized payload to AI. Review the generated proposals with edit/accept/reject controls.
- **Manual creation/edit:** Build cards manually with 200-character front and 500-character back limits and edit details via the dedicated view, which also tracks metadata and history.
- **List management:** Browse every card with search/filtering on front/back fields, sorting by creation/modification date, pagination, and bulk deletion. Listing links to detail views for edits.
- **Learning sessions:** Choose card sets (all, filtered, status-based), hand off to an external spaced-repetition library, display current card + remaining count, and provide rating buttons/navigational controls with logging of each session outcome.
- **Logging & analytics:** Record all accept/reject/create/edit events with user ID, source (AI/manual), timestamps, and source text to support manual SQL-based reporting.
- **Boundaries:** MVP avoids mobile apps, password resets/recovery, cross-account sharing, advanced AI moderation, and imports beyond plain text.

## Project status
- **MVP focus:** Deliver an AI-assisted flashcard generator plus curation/reporting workflows for students, professionals, and language learners.
- **Success metrics:** ≥75% of AI-generated cards accepted, ≥75% of new cards created via AI, average generation time <5 minutes, ≥80% of sessions marked complete (including interrupted ones). Logs capture acceptance/source data and session evaluations.

## License
Not specified yet (TBD). Replace with the desired license when ready.