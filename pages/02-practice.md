---
layout: section
---

# Part 2: Workshop

Walk through the **`is-05-frontend`** Figmaland demo step-by-step, install the **caveman** skill, and wire up Figma + shadcn MCPs

---

# What We're Building (Reference Implementation)

The reference project lives at **[koldovsky/is-05-frontend](https://github.com/koldovsky/is-05-frontend)** — a real Figma → Next.js implementation of the **Figmaland** community landing page.

<v-clicks>

```
is-05-frontend/
├── .agents/skills/                 ← Vercel skills installed via `npx skills add ...`
│   ├── vercel-react-best-practices/
│   └── web-design-guidelines/
├── app/
│   ├── globals.css                 ← Tailwind v4 + tokens (the "agent CSS")
│   ├── layout.tsx
│   └── page.tsx                    ← Composes all sections
├── components/
│   ├── sections/                   ← Hero, Features, Organize, Pricing, ...
│   └── ui/                         ← Button, Input, Logo, BurgerMenu, SocialIcons
├── docs/
│   ├── DESIGN.md                   ← The agent-readable style guide
│   └── prd.md                      ← Product brief + Figma URLs
├── public/figmaland/               ← Figma-extracted assets
├── AGENTS.md                       ← Engineering rules + reference to DESIGN.md
├── CLAUDE.md                       ← `@AGENTS.md` (single source of truth)
├── skills-lock.json                ← Pinned skill versions
└── package.json                    ← Next.js 16 + React 19 + Tailwind v4
```

</v-clicks>

---
layout: center
---

# Step 1: Bootstrap the Project (~5 min)

---

# 1.1 Create Next.js + Tailwind v4

```bash
npx create-next-app@latest is-05-frontend
cd is-05-frontend
```

```text
✔ Would you like to use TypeScript?            Yes
✔ Would you like to use ESLint?                Yes
✔ Would you like to use Tailwind CSS?          Yes
✔ Would you like your code inside a `src/`?    No
✔ Would you like to use App Router?            Yes
✔ Would you like to use Turbopack for `next dev`?  Yes
✔ Would you like to customize the import alias? `@/*`
```

<v-clicks>

- This gives you Next.js 16 + React 19 + Tailwind v4 + TypeScript out of the box
- `app/globals.css` already imports Tailwind via `@import "tailwindcss"`
- `app/page.tsx` is your landing page entry — we'll replace it with composed sections.

</v-clicks>

---

# 1.2 Verify the Stack

```bash
npm run dev
# → http://localhost:3000  (Next.js logo)

cat package.json | grep -E "next|react|tailwind"
#   "next": "16.x",
#   "react": "19.x",
#   "react-dom": "19.x",
#   "@tailwindcss/postcss": "^4",
#   "tailwindcss": "^4",
```

<v-click>

> If anything older shows up, your scaffold pre-dates the Tailwind v4 + Next 16 era. Re-create with `--example` pointed at the latest template, or update `next` and `tailwindcss` manually.

</v-click>

---
layout: center
---

# Step 2: Wire the Agent Context (~10 min)

---

# 2.1 Add `AGENTS.md`

```md
<!-- AGENTS.md -->
<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all
differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/`
before writing any code. Heed deprecation notices.

<!-- END:nextjs-agent-rules -->

Reference @docs/prd.md
```

<v-clicks>

- The "this is NOT the Next.js you know" block is **mandatory** for Next 16
  (Cache Components, `proxy.ts`, Turbopack defaults are all post-cutoff for most models)
- The `@docs/prd.md` reference auto-injects the product brief into the agent's context
- `CLAUDE.md` is just `@AGENTS.md` — single source of truth for both Cursor and Claude Code.

</v-clicks>

---

# 2.2 Add `docs/prd.md` (Product Intent)

```md
<!-- docs/prd.md -->

we are implementing Figmaland responsive landing page
https://www.figma.com/design/tBwJlpl5o5mWdQHEQqAaa5/Figmaland--Business-Landing-page--Community-

Mobile layout:
https://www.figma.com/design/tBwJlpl5o5mWdQHEQqAaa5/.../?node-id=65-94

Desktop layout:
https://www.figma.com/design/tBwJlpl5o5mWdQHEQqAaa5/.../?node-id=65-122
```

<v-clicks>

- Three lines, three Figma links — the agent now knows what page, what mobile frame and what desktop frame to implement
- This is the **product intent layer** from the canonical workflow
- For your own assignment, drop your own PRD here and your own Figma URLs.

</v-clicks>

---

# 2.3 Add `docs/DESIGN.md` (Visual Source of Truth)

The Figmaland demo extracts the design system into a real `DESIGN.md` (excerpt):

```md
## Color Tokens
| Token                       | Hex       | Usage                                    |
| --- | --- | --- |
| `color.primary`             | `#2091F9` | Primary buttons, CTA highlights          |
| `color.text.default`        | `#252B42` | Headings, navigation links               |
| `color.text.secondary`      | `#374754` | Paragraphs, descriptions                 |
| `color.background.dark`     | `#18171D` | Dark surfaces                            |
| `color.border.default`      | `#E8E8E8` | Input borders, dividers                  |

## Typography
| Style          | Font    | Weight | Size  | Line Height |
| --- | --- | --- | --- | --- |
| `type.h1`      | Graphik | 700    | 74px  | 84px        |
| `type.h2`      | Graphik | 400    | 48px  | 55px        |
| `type.button`  | Graphik | 400    | 20px  | 20px        |

## Spacing And Radius
| Token              | Value | Usage                       |
| --- | --- | --- |
| `radius.button`    | 35px  | Primary button pill radius  |
| `space.control.x`  | 38px  | Horizontal button padding   |
```

<v-click>

This file is what the agent reads when you say _"make a hero matching DESIGN.md"_ — no more guessed hex codes.

</v-click>

---
layout: center
---

# Step 3: Turn DESIGN.md into Real Tokens (~10 min)

---

# 3.1 Tailwind v4 — `app/globals.css`

```css
@import "tailwindcss";

:root {
  --figmaland-color-primary:        #2091f9;
  --figmaland-color-text-default:   #252b42;
  --figmaland-color-text-secondary: #374754;
  --figmaland-color-background-dark:#18171d;
  --figmaland-color-border-default: #e8e8e8;

  --figmaland-radius-button: 35px;
  --figmaland-radius-input:  39px;

  --figmaland-space-control-x: 38px;
  --figmaland-space-control-y: 18px;
}
```

<v-clicks>

- Each `DESIGN.md` token becomes a CSS custom property
- They live on `:root` so they are visible to **the browser**, **Tailwind**, **shadcn components**, and **the agent reading the file**.

</v-clicks>

---

# 3.2 Bridge Tokens into Tailwind v4 — `@theme inline`

```css
@theme inline {
  --color-figmaland-primary: var(--figmaland-color-primary);
  --color-figmaland-text:    var(--figmaland-color-text-default);
  --color-figmaland-muted:   var(--figmaland-color-text-secondary);
  --color-figmaland-dark:    var(--figmaland-color-background-dark);
  --color-figmaland-border:  var(--figmaland-color-border-default);

  --radius-figmaland-button: var(--figmaland-radius-button);
  --radius-figmaland-input:  var(--figmaland-radius-input);

  --spacing-figmaland-control-x: var(--figmaland-space-control-x);
  --spacing-figmaland-control-y: var(--figmaland-space-control-y);

  --text-figmaland-h1: 74px;
  --text-figmaland-h1--line-height: 84px;
  --text-figmaland-h1--font-weight: 700;
}
```

<v-click>

The agent can now write `bg-figmaland-primary`, `rounded-figmaland-button`, `text-figmaland-h1` — and **Tailwind generates real classes** from your tokens.

</v-click>

---

# 3.3 Optional: Semantic Component Classes

```css
@layer components {
  .figmaland-h1 {
    color: var(--figmaland-color-text-default);
    font: 700 74px / 84px var(--figmaland-font-sans);
    letter-spacing: 0.2px;
  }

  .figmaland-button {
    background: var(--figmaland-color-primary);
    border-radius: var(--figmaland-radius-button);
    color: var(--figmaland-color-text-inverse);
    padding: var(--figmaland-space-control-y) var(--figmaland-space-control-x);
  }
}
```

<v-click>

These shortcut classes give the agent a **semantic name** to reach for (`figmaland-h1`) when raw utilities would clutter the JSX.

</v-click>

---
layout: center
---

# Step 4: Install Vercel Frontend Skills (~5 min)

---

# 4.1 Two Skills Worth Installing First

```bash
npx skills add vercel-labs/agent-skills \
    --path skills/react-best-practices/SKILL.md \
    --as vercel-react-best-practices

npx skills add vercel-labs/agent-skills \
    --path skills/web-design-guidelines/SKILL.md \
    --as web-design-guidelines
```

<v-clicks>

- `vercel-react-best-practices` — 70 rules across 8 categories (waterfalls, bundle size, server performance, re-renders, ...) tagged for AI agents
- `web-design-guidelines` — review files for compliance with Vercel's Web Interface Guidelines (accessibility, focus rings, contrast, ...)
- Both land in `.agents/skills/` and get pinned in `skills-lock.json` — reproducible across machines.

</v-clicks>

---

# 4.2 Use the Skills

```text
Use the vercel-react-best-practices skill.
Refactor components/sections/Pricing.tsx for re-render and bundle hygiene.
Apply only CRITICAL and HIGH-priority rules.
```

```text
Use the web-design-guidelines skill on app/page.tsx and components/sections/*.tsx.
Output findings in file:line format.
```

<v-clicks>

- Skills become **slash-commands or named references** — the agent loads them on demand
- Pin versions via `skills-lock.json` so the same skill produces the same review on CI
- Add more skills later (`accessibility-audit`, `cls-fixer`, your own `is-05-component-rules`, ...).

</v-clicks>

---
layout: center
---

# Step 5: Connect Figma MCP (~10 min)

---

# 5.1 Install the Figma Plugin

```bash
# In Cursor:
/add-plugin figma

# In Claude Code:
claude plugin install figma@claude-plugins-official
```

<v-clicks>

- The official plugin ships:
  - Figma **MCP config** (no manual JSON editing)
  - **Skills**: `figma-implement-design`, `figma-generate-design`, `figma-use`, `figma-use-figjam`, `figma-code-connect`
  - **Design-system rules** for variants, tokens, accessibility
- Works for Figma Design, Figma Make, FigJam.

</v-clicks>

---

# 5.2 Drive It With a Figma URL

```text
Use figma-implement-design.

Source of truth:
https://www.figma.com/design/tBwJlpl5o5mWdQHEQqAaa5/.../?node-id=65-122

Constraints:
- Read DESIGN.md before any edit.
- Use existing components in components/sections and components/ui first.
- Tailwind v4 tokens only — no inline hex colors.
- Plan first. Do not edit files until I say "go".
```

<v-clicks>

- The agent calls Figma MCP to fetch the **scoped frame**
- Cross-references `DESIGN.md` and the existing components
- Returns a plan of "I will create / edit these files, with these props"
- Only after you say "go" does it edit anything.

</v-clicks>

---
layout: center
---

# Step 6: Walk the Reference Code (~15 min)

---

# 6.1 `app/page.tsx` — Just a Composition

```tsx
import { Hero } from "@/components/sections/Hero";
import { Features } from "@/components/sections/Features";
import { Organize } from "@/components/sections/Organize";
import { Newsletter } from "@/components/sections/Newsletter";
import { Partners } from "@/components/sections/Partners";
import { Testimonials } from "@/components/sections/Testimonials";
import { Pricing } from "@/components/sections/Pricing";
import { Contact } from "@/components/sections/Contact";
import { Footer } from "@/components/sections/Footer";

export default function Home() {
  return (
    <>
      <Hero /> <Features /> <Organize /> <Newsletter />
      <Partners /> <Testimonials /> <Pricing /> <Contact /> <Footer />
    </>
  );
}
```

<v-click>

> The page is just a stack. Every section is independent → easy for the agent to add / remove / reorder.

</v-click>

---

# 6.2 `components/ui/Button.tsx` — Token-Driven Primitive

```tsx
const baseClasses =
  "inline-flex items-center justify-center font-figmaland text-figmaland-button " +
  "rounded-figmaland-button px-figmaland-control-x py-figmaland-control-y " +
  "transition-opacity hover:opacity-90 focus-visible:outline-none " +
  "focus-visible:ring-2 focus-visible:ring-offset-2 focus-visible:ring-figmaland-primary " +
  "disabled:opacity-50";

const variantClasses: Record<Variant, string> = {
  primary: "bg-figmaland-primary text-figmaland-inverse",
  "primary-on-dark": "bg-figmaland-inverse text-figmaland-primary",
  "outline-light":
    "bg-transparent border border-figmaland-inverse text-figmaland-inverse",
};
```

<v-clicks>

- Every class in `baseClasses` references a **token** from `globals.css`
- Variants are a **typed enum** — Code Connect can map Figma variants 1:1
- Focus ring is built in → `web-design-guidelines` passes.

</v-clicks>

---

# 6.3 `components/sections/Hero.tsx` — Tokens + Responsive

```tsx
export function Hero() {
  return (
    <section
      className="relative w-full bg-figmaland-dark text-white"
      style={{ backgroundImage: "url('/figmaland/hero-bg.jpg')", ... }}
    >
      <div className="absolute inset-0 bg-figmaland-dark/40" aria-hidden="true" />
      <div className="relative">
        <Header />
        <div className="mx-auto flex w-full max-w-[1100px] flex-col items-center
                        gap-10 px-5 pb-32 pt-12 text-center md:pb-56 md:pt-20">
          <h1 className="figmaland-h1 text-white text-[44px] leading-[1.05]
                         sm:text-[56px] md:text-figmaland-h1">
            The best products<br />start with Figma
          </h1>
          ...
          <Button className="w-[236px] py-4">Try For Free</Button>
        </div>
      </div>
    </section>
  );
}
```

<v-click>

Mobile sizes (`text-[44px]`) gracefully scale up to the desktop token (`md:text-figmaland-h1` = 74px).

</v-click>

---

# 6.4 `components/sections/Pricing.tsx` — Highlighted Variant

```tsx
const isHighlighted = plan.highlighted;
const cardBg          = isHighlighted ? "bg-figmaland-primary" : "bg-white";
const titleColor      = isHighlighted ? "text-white" : "text-figmaland-text";
const descriptionColor= isHighlighted ? "text-white" : "text-figmaland-muted";
```

<v-clicks>

- The "highlighted" variant in Figma maps to `plan.highlighted` in TypeScript
- Tokens (`bg-figmaland-primary`, `text-figmaland-text`) make swaps trivial
- Easy for the agent to add a fourth plan: just append a `Plan` object — no new CSS.

</v-clicks>

---

# 6.5 `components/sections/Newsletter.tsx` — Inline SVG Illustration

```tsx
function NewsletterIllustration({ className = "" }) {
  return (
    <svg viewBox="0 0 535 406" aria-hidden="true" className={className}>
      <ellipse cx="200" cy="380" rx="190" ry="14" fill="#E8E9F2" />
      <rect    x="220"  y="60"  width="260" height="190" rx="8" fill="#F4F5FB" />
      <rect    x="220"  y="60"  width="260" height="60"  rx="8" fill="#2091F9" />
      ...
    </svg>
  );
}
```

<v-clicks>

- Figma → SVG export via Figma MCP, then inlined for **zero requests** and CSS-controllable color
- Note: this is one of the few places hex literals are acceptable — they belong to a **fixed illustration**, not the design system
- Future improvement: replace literal `#2091F9` with `var(--figmaland-color-primary)` for theme support.

</v-clicks>

---

# 6.6 `components/ui/BurgerMenu.tsx` — A Real Client Component

```tsx
"use client";

import { useId, useState } from "react";

export function BurgerMenu({ links, ... }: Props) {
  const [isOpen, setIsOpen] = useState(false);
  const menuId = useId();
  return (
    <div className={`relative ${className}`.trim()}>
      <button aria-expanded={isOpen} aria-controls={menuId}
              onClick={() => setIsOpen((c) => !c)}>...</button>
      <nav id={menuId} aria-label="Mobile navigation"
           className={`... transition ${isOpen ? "opacity-100" : "opacity-0"}`}>
        ...
      </nav>
    </div>
  );
}
```

<v-clicks>

- `"use client"` only where state is needed — the rest of the page is RSC
- Accessibility built in: `aria-expanded`, `aria-controls`, `useId()` for unique ids
- `web-design-guidelines` skill loves this; agent learns the pattern from this single file.

</v-clicks>

---
layout: center
---

# Step 7: Install caveman (~5 min)

---

# 7.1 One-Line Install

```bash
# macOS / Linux / WSL / Git Bash
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.sh | bash

# Windows (PowerShell)
irm https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.ps1 | iex
```

<v-clicks>

- Detects 30+ agents — installs the appropriate skill / plugin / extension for each
- Defaults: Claude Code hooks + statusline + `caveman-shrink` MCP proxy
- Add `--all` to also drop per-repo `.cursor/rules/caveman.mdc`, `.github/copilot-instructions.md`, `AGENTS.md` rules
- Add `--minimal` to skip the extras and just install the plugin/extension.

</v-clicks>

---

# 7.2 Trigger and Levels

```text
/caveman           # default = Full
/caveman lite      # drop filler, keep grammar
/caveman ultra     # telegraphic, abbreviate everything
/caveman wenyan    # 文言文 — Classical Chinese, the most token-efficient written language

# Stop:
"normal mode"  or  "stop caveman"
```

<v-clicks>

- Caveman only affects **output** — thinking/reasoning tokens are untouched
- Code, URLs, paths, identifiers stay byte-for-byte identical
- For a frontend session: try `/caveman lite` for paired programming, `/caveman ultra` for batch refactors.

</v-clicks>

---

# 7.3 Caveman Skills Worth Knowing

| Skill | What |
|-------|------|
| `/caveman-commit` | Conventional Commits, ≤50-char subject. Why over what. |
| `/caveman-review` | One-line PR comments: `L42: 🔴 bug: user null. Add guard.` |
| `/caveman:compress <file>` | Rewrites a memory file (e.g. `CLAUDE.md`) into caveman-speak. ~46% input savings every session. |
| `/caveman-stats` | Real session token usage + estimated savings + USD. |
| `caveman-shrink` MCP | Wraps any MCP server, compresses `tools/list` descriptions on the fly. |

<v-click>

Try this on `AGENTS.md` of the assignment repo: `/caveman:compress AGENTS.md` → backup as `AGENTS.original.md`, ~46% smaller.

</v-click>

---
layout: center
---

# Step 8: Wire shadcn MCP (Optional, ~10 min)

---

# 8.1 Add to `.cursor/mcp.json`

```json
{
  "mcpServers": {
    "shadcn": {
      "command": "npx",
      "args": ["-y", "shadcn@4.7.0", "mcp"]
    },
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem@2025.10.20",
        "./app", "./components", "./public", "./docs"
      ]
    }
  }
}
```

<v-click>

Filesystem MCP is **scoped** — agent cannot wander outside the four directories that matter for the page.

</v-click>

---

# 8.2 Use shadcn From Chat

```text
Use shadcn MCP.
Show me available landing-page hero blocks.
Install the one closest to a centered hero with a single CTA.
Adapt it to use figmaland-primary tokens and Graphik typography from DESIGN.md.
```

<v-clicks>

- The agent fetches block metadata from the registry
- Picks a candidate, installs it into `components/`
- Re-themes it through your tokens — no inline hex
- You review the diff, accept or refine.

</v-clicks>

---
layout: center
---

# Step 9: Verify Everything (~5 min)

---

# 9.1 Local Smoke Test

```bash
npm run dev           # → http://localhost:3000
npm run lint          # → eslint clean
npx tsc --noEmit      # → typecheck clean
npm run build         # → next build (Turbopack)
```

<v-clicks>

- All four must pass before opening a PR
- If you wired the Vercel MCP — preview URL pops up automatically when you push
- For static-analysis grounding, point [fallow.tools](https://fallow.tools) at the repo and ask:
  _"Which sections still hardcode colors?"_

</v-clicks>

---

# 9.2 Visual QA Loop

```text
Use Playwright MCP.
1. Open http://localhost:3000.
2. Take screenshots at 375px and 1440px.
3. Compare against the Figma frames in docs/prd.md.
4. List any obvious mismatches with file:line references.
```

<v-clicks>

- Screenshot diffing as the **last** verification step
- Combined with the agentic plan-first flow, you have full coverage:
  product intent → design → engineering → component → verification.

</v-clicks>

---
layout: center
---

# Workshop Summary

<v-clicks>

- **Bootstrapped** Next.js 16 + Tailwind v4 + TypeScript with `create-next-app`
- **Wrote** `AGENTS.md`, `docs/prd.md`, `docs/DESIGN.md` — the three context layers
- **Translated** `DESIGN.md` into real CSS variables + `@theme inline` Tailwind tokens
- **Installed** `vercel-react-best-practices` and `web-design-guidelines` skills
- **Connected** Figma MCP, optionally shadcn + filesystem + context7
- **Walked through** Hero, Pricing, Newsletter, BurgerMenu — token-first, accessible, RSC-by-default
- **Installed** `caveman` for ~75% smaller agent output and `caveman-shrink` for compressed MCP descriptions
- **Verified** with lint, typecheck, build, and a Playwright visual QA loop
- The agent now has a **complete frontend system** — not just a prompt.

</v-clicks>
