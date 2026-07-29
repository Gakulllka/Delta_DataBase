# AGENTS.md — Delta Ecosystem AI Prompt

> **READ THIS FILE BEFORE EVERY ACTION.** This is your mandatory startup prompt.

---

## Rules

### 1. Language

**All communication, products, and content must be in Russian (Русский язык).**

- Chat responses: Russian
- Product interfaces: Russian
- Documentation: Russian
- Code comments: Russian (where appropriate)
- Variable names, file names, CSS classes: English (technical standard)

### 2. Git Push — STRICTLY FORBIDDEN without user command

**NEVER push to any git remote without explicit user permission.**

- Do NOT run `git push` unless user says "запуши" or "сделай пуш" or similar
- Do NOT run `git push` after commits even if you think it's helpful
- Always wait for user to confirm before pushing
- This rule applies to ALL repositories in the ecosystem

### 3. Visual Style — Delta Design DNA

Delta ecosystem has a **unified visual style** called "Graphite and Paper" (Графит и бумага).

**This style is mandatory for ALL projects.** No exceptions.

Key rules:
- **Monochrome by default.** Accent = ink (dark in light theme, light in dark theme)
- **Color only for meaning.** Status, progress, danger — nothing else gets color
- **Graphite rail** (`#17181C`) — the brand "door" in every product with sidebar
- **Fonts:** Geist + Geist Mono everywhere
- **Exact hex values** — `#FAFAF8`, `#17181C`, `#131418`, `#F5F5F2` (no oklch deviations)
- **Micro-interactions:** `scale(0.97)` on press, `translateY(-1px)` on hover
- **Easing:** `cubic-bezier(0.16, 1, 0.3, 1)` everywhere
- **`prefers-reduced-motion`** — always respected

**CSS tokens must match exactly across all projects.** See `01-Дизайн/Delta_Design_DNA.md` §4.5.

### 4. Research Order

**Always study DataBase FIRST, then projects — only if strictly necessary.**

1. Read `01-Дизайн/Delta_Design_DNA.md` — the design system
2. Read relevant product documentation in `02-Продукты/`
3. Read this file (`03-ИИ/AGENTS.md`) — your rules
4. **Only then** explore project source code if needed

**Do NOT skip DataBase.** It is the single source of truth.

### 5. This File is Your Prompt

**AGENTS.md is a large prompt for AI before any work.**

- Read it at the start of every session
- Follow all rules strictly
- If you're unsure about something — check DataBase first
- When in doubt, ask the user (in Russian)

### 6. Sync Changes to DataBase

**When new functionality appears or old functionality changes — you MUST update DataBase.**

Rules:
- Add corresponding links and references
- If the change contradicts existing information — **unify under the common style**
- Update `Delta_Design_DNA.md` if visual style changes
- Update product documentation in `02-Продукты/`
- Update this file if AI workflow changes

**DataBase is never stale.** If you changed code, DataBase must reflect it.

### 7. Error Reporting

**Track ALL errors in DataBase to prevent them in the future.**

When you encounter an error:
1. **Fix it first** — resolve the issue before documenting
2. **Log it** — add to `03-ИИ/Ошибки.md` with:
   - Date
   - Product where error occurred
   - Error message (exact)
   - Root cause
   - Solution
3. **Update AGENTS.md** — if the error reveals a new rule or pattern, add it
4. **Prevention** — document how to avoid similar errors in future projects

**Error log is mandatory.** Every error teaches us something. Never repeat the same mistake twice.

See `03-ИИ/Ошибки.md` for the full error log.

### 8. Tailwind CSS v4 Rules

**When creating new projects with Tailwind CSS v4, follow these rules:**

1. **PostCSS config:** Use `@tailwindcss/postcss` instead of `tailwindcss`:
```js
module.exports = {
  plugins: {
    '@tailwindcss/postcss': {},
    autoprefixer: {},
  },
}
```

2. **CSS import:** Use `@import "tailwindcss"` instead of `@tailwind` directives:
```css
@import "tailwindcss";
```

3. **Never copy** CSS or config from v3 projects without updating to v4 syntax.

4. **Always check** Tailwind CSS documentation for migration changes.

### 9. Vite + TypeScript Rules

**When creating Vite projects with TypeScript:**

1. **Always create** `src/vite-env.d.ts` with:
```ts
/// <reference types="vite/client" />
```

2. This file provides type declarations for CSS imports, asset imports, and other Vite-specific features.

3. Without this file, TypeScript will throw `Cannot find module './index.css'` errors during build.

---

## Ecosystem Structure

```
C:\Delta\
├── DataBase/           ← Obsidian vault (AI knowledge base)
│   ├── 00-Вход/        ← Main page (MOC)
│   ├── 01-Дизайн/      ← Design DNA + differences doc
│   ├── 02-Продукты/    ← Product documentation
│   ├── 03-ИИ/          ← AI prompts (this file)
│   └── 04-Шаблоны/     ← Templates
│
├── Delta-hub/          ← Central launcher (React/Vite, port 3000)
├── Delta-BA/           ← Business analyst assistant (React/Vite, port 5173)
└── Delta-tasker/       ← Task tracker (Next.js, port 3001)
```

## Products

| Product | Purpose | Stack | Dev Port | Production |
|---------|---------|-------|----------|------------|
| **Delta-hub** | Central navigation hub | React 19, Vite 8, Tailwind 4 | :3000 | https://delta-hub-alpha.vercel.app/ |
| **Delta-BA** | AI-powered analyst tools | React 19, Vite 8, Tailwind 4 | :5173 | https://delta-ba.vercel.app |
| **Delta-tasker** | Task tracker & presentations | Next.js 16, shadcn/ui, Prisma | :3001 | https://delta-tasker.vercel.app |
| **Delta-finance** | Personal finance tracker | Next.js 16, shadcn/ui, Prisma | :3002 | — |
| **DataBase** | Knowledge base (Obsidian) | Markdown vault | — | — |

**Backup deployments:**
- Delta-BA: https://delta-ba.netlify.app
- Delta-tasker: https://delta-tasker.netlify.app

## Design Tokens (Quick Reference)

```
Light:  bg #FAFAF8  card #FFFFFF  text #17181C  border #DEDDD6
Dark:   bg #131418  card #1A1B20  text #F5F5F2  border #34353C
Rail:   bg #17181C  text #FAFAF8  active bg #FAFAF8 active text #17181C
```

---

*Last updated: see `01-Дизайн/Delta_Design_DNA.md` version 1.1*
