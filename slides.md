---
theme: default
background: https://cover.sli.dev
title: "AI Frontend Workflow 2026 — Cursor, Claude Code, Figma MCP, Next.js, Tailwind, shadcn"
info: |
  ## AI Frontend Workflow 2026
  Build production UI with Cursor / Claude Code, Figma MCP, Next.js 16, Tailwind v4 and shadcn/ui — using rules, skills, design tokens, registries and agentic workflows.
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
overviewSnapshots: true
---

# AI Frontend Workflow 2026

From "chat writes a button" to **agent ships a screen** — Cursor, Claude Code, Figma MCP, Next.js, Tailwind v4 and shadcn/ui

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    <carbon:arrow-right class="inline"/>
  </span>
</div>

---
layout: image-left
image: /vyacheslav-koldovskyy.png
---

# Vyacheslav Koldovskyy

- Ph.D, Associate Professor, 20+ years in IT
- Competence Manager, SoftServe
- Certified Google Cloud Professional Architect
- Certified NVIDIA Generative AI with LLMs
- Head of Generative AI Center at IT STEP University
- YouTube: [programmingmentorua](https://www.youtube.com/@programmingmentorua)
- LinkedIn: [koldovsky](https://www.linkedin.com/in/koldovsky/)

---

# Workshop Goals

<v-clicks>

- See where **AI frontend** is in 2026 — agents, MCP, design tokens, registries
- Compare the **landscape**: v0, Figma Make, Stitch, Lovable / Bolt vs. Cursor + Claude Code + MCP
- Learn the **canonical agentic workflow** for production UI
- Wire up **Figma MCP**, **shadcn MCP**, **DESIGN.md**, and an **agent-readable Tailwind v4 token system**
- Walk through a real Figma → Next.js implementation (the `is-05-frontend` Figmaland demo)
- Bonus: install the **caveman** skill and feel a 3× faster agent loop
- **Result: you can ship a real UI from a Figma frame end-to-end with one autonomous agent.**

</v-clicks>

---

# Agenda

| Block | Duration | Description |
|-------|----------|-------------|
| Theory | ~70 min | AI frontend landscape: agents, Figma MCP, DESIGN.md, design tokens, Next.js 16, Tailwind v4, shadcn registry, v0, fallow.tools, caveman, security |
| Workshop | ~90 min | Walk through `is-05-frontend` (Figma → Next.js + Tailwind + shadcn). Install caveman. Wire shadcn + Figma MCPs. |
| Assignment | ~15 min | Build your own Figma-driven Next.js page in `is-05-frontend-task` and open a PR |

---
src: ./pages/01-theory.md
---

---
src: ./pages/02-practice.md
---

---
src: ./pages/03-assignment.md
---

---
layout: end
---

# Thank you!

Questions? Let's discuss.
