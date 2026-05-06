---
layout: section
---

# Assignment

Build your own Figma-driven Next.js page and open a Pull Request

---

# What We Did in the Workshop

<v-clicks>

- Wired the **five frontend context layers** (product / engineering / design / components / verification) into a real repo
- Translated `DESIGN.md` into Tailwind v4 tokens and semantic component classes
- Installed Vercel skills (`vercel-react-best-practices`, `web-design-guidelines`)
- Connected Figma MCP (and optionally shadcn / context7 / filesystem MCPs)
- Installed **caveman** for terse agent output and `caveman-shrink` for compressed MCP descriptions
- Walked through the **`is-05-frontend`** Figmaland implementation end-to-end.

</v-clicks>

---

# Starter Repository

For the homework we use a clean starter:

**[koldovsky/is-05-frontend-task](https://github.com/koldovsky/is-05-frontend-task)**

```bash
# Fork via GitHub UI, then:
git clone https://github.com/<your-username>/is-05-frontend-task.git
cd is-05-frontend-task
npm install
npm run dev
```

<v-click>

The starter ships with:

- Empty Next.js 16 + React 19 + Tailwind v4 + TypeScript scaffold
- `AGENTS.md` (with the "this is NOT the Next.js you know" block) and `CLAUDE.md`
- `docs/DESIGN.md` template — fill in your tokens
- `docs/prd.md` template — drop in your Figma URLs
- `.cursor/mcp.json.example` with Figma + shadcn + context7 + filesystem entries
- `.agents/skills/` pre-loaded with `vercel-react-best-practices` and `web-design-guidelines`
- `skills-lock.json` for reproducible skill versions
- README with the full setup checklist.

</v-click>

---

# Assignment

<v-clicks>

1. **Pick a Figma design** to implement:
   - Use the suggested community Figma file (link in starter README), **or**
   - Bring your own — landing page, dashboard, settings screen, anything with ≥ 5 sections / states
2. **Fill `docs/prd.md`** with the Figma URL(s) and a short product brief (who / what / constraints)
3. **Fill `docs/DESIGN.md`** with your tokens (colors, typography, spacing, radius) extracted from Figma
4. **Translate `DESIGN.md`** into Tailwind v4 CSS variables in `app/globals.css` (`:root` + `@theme inline`)
5. **Implement at least 3 sections** as RSC components in `components/sections/` composed from primitives in `components/ui/`
6. **Add at least 1 client component** (`"use client"`) with proper accessibility (focus, aria, keyboard) — e.g. burger menu, dialog, tabs
7. **Run** `npm run lint`, `npx tsc --noEmit`, `npm run build` — all green
8. **Open a PR** to `koldovsky/is-05-frontend-task` from a branch named `frontend/<your-github-username>`.

</v-clicks>

---

# Bonus Tasks

<v-clicks>

- **(Bonus 1)** Connect the **Figma MCP** properly with a real `.cursor/mcp.json` and demo a "Figma URL → page section" generation in the PR description
- **(Bonus 2)** Install **caveman** in your Cursor / Claude Code and screenshot a `/caveman-stats` after the session
- **(Bonus 3)** Run **`/caveman:compress AGENTS.md`** and commit both `AGENTS.md` (compressed) and `AGENTS.original.md` (backup)
- **(Bonus 4)** Use **shadcn MCP** to install at least one block and re-theme it through your tokens
- **(Bonus 5)** Add a **Playwright MCP** screenshot QA step in the README
- **(Bonus 6)** Deploy to **Vercel** and put the preview URL in the PR
- **(Bonus 7)** Add a **`docs/frontend-testing/<your-page>.md`** A/B comparison: same prompt with vs without DESIGN.md / Figma MCP — show the difference.

</v-clicks>

---

# Acceptance Criteria

| # | Criterion | How to verify |
|---|-----------|---------------|
| 1 | `docs/prd.md` filled with Figma URLs and product brief | Real Figma links open; brief has audience + constraints |
| 2 | `docs/DESIGN.md` filled with real tokens | Colors / typography / spacing / radius all defined |
| 3 | `app/globals.css` exposes those tokens via `:root` and `@theme inline` | `bg-<token>`, `rounded-<token>`, `text-<token>` work in JSX |
| 4 | ≥ 3 RSC sections in `components/sections/` | Each section uses tokens, not raw hex |
| 5 | ≥ 1 client component with a11y | `"use client"`, `aria-*`, focus-visible styling |
| 6 | `npm run lint && npx tsc --noEmit && npm run build` all green | CI / local |
| 7 | PR opened against `koldovsky/is-05-frontend-task`, branch `frontend/<your-username>` | GitHub UI |
| 8 | PR description lists which MCPs / skills you used | Visible in description |

---

# Suggested Project Ideas

| Name | Why it's a good fit |
|------|---------------------|
| **Marketing landing page** | Plenty of sections, lots of design tokens, easy a11y wins |
| **Pricing page (Free / Pro / Enterprise)** | Variant work, comparison tables, CTA discipline |
| **Auth screens (sign-in / sign-up / forgot password)** | Forms, validation states, focus management |
| **SaaS dashboard shell** | Sidebar, nav, cards, data table primitives — perfect for shadcn MCP |
| **Settings page** | Tabs, sections, form components, modal/sheet patterns |
| **Internal tool / admin panel** | Table-heavy, filtering, RSC streaming opportunities |

<v-click>

Pick whatever your **day job** would benefit from — the point is to wire up a real workflow, not to ship a portfolio piece.

</v-click>

---

# Deliverables Checklist

<v-clicks>

- **Fork** [koldovsky/is-05-frontend-task](https://github.com/koldovsky/is-05-frontend-task)
- Create a branch: `frontend/<your-github-username>`
- Push:
  - `app/page.tsx` composing your sections
  - `components/sections/*.tsx` (≥ 3) and `components/ui/*.tsx` (primitives)
  - `app/globals.css` with tokens (`:root` + `@theme inline`)
  - `docs/prd.md` and `docs/DESIGN.md` filled in
  - `AGENTS.md` (kept or compressed via caveman)
  - `.cursor/mcp.json.example` with the MCPs you used
  - `public/...` with any Figma-extracted assets
- Open a **Pull Request** back to the original repo
- Be ready to demo your Figma → page flow and your token system in 5 minutes.

</v-clicks>

---

# Expected Result

> Your starter repo turns into a **production-ready frontend codebase** that any agent — Cursor, Claude Code, v0, Codex — can reason about correctly.

<v-clicks>

- Same `DESIGN.md` works in any host
- Same `AGENTS.md` rules work in Cursor and Claude Code
- Same `.cursor/mcp.json.example` works in Cursor / Claude Code / VS Code
- Same Tailwind v4 tokens work for shadcn, raw HTML, and any future framework swap
- Your team — and your AI — share **one design system, one set of rules, one verification loop**.

</v-clicks>

---
layout: center
---

# Questions?

Good luck with your frontend assignment — see you in PRs.
