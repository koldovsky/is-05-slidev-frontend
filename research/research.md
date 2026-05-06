Я працюю над воркшопом по використанню AI у фронтенді (Cursor, Claude Code).
Треба зібрати інфу про те що є зараз саме нове/модне, як воно працює.
design.md
робота з figma
ще щось
мені найбільш цікаво використовувати Next.js + Tailwind + shadcn
пошукай, дай мені контекст для подальшого використання

Нижче – контекст для воркшопу саме під **Next.js + Tailwind + shadcn + Cursor / Claude Code + Figma**.

## 1. Головна ідея воркшопу

У 2026 фронтенд із AI рухається від “чат напише компонент” до **agentic workflow**:

**людина задає ціль і обмеження → агент читає репозиторій → бере дизайн-контекст із Figma / DESIGN.md / shadcn registry → планує → змінює кілька файлів → запускає lint/typecheck/tests → ітеративно виправляє.**

Тобто тема не “як згенерувати кнопку”, а **як дати агенту достатньо контексту, щоб він генерував UI у стилі вашого продукту, вашими компонентами і без хаосу в коді**.

## 2. Що зараз найсвіжіше й модне

### Cursor: IDE перетворюється на agent platform

Cursor уже не тільки редактор із autocomplete. У квітні 2026 він активно рухається в бік флоту агентів: `/multitask` розбиває великі задачі на async subagents, worktrees дозволяють запускати ізольовані задачі в бекграунді на різних гілках, а multi-root workspaces дають одному агенту працювати по кількох репозиторіях – наприклад frontend, backend і shared UI library. ([Cursor][1])

Ще один великий крок – **Cursor SDK**. Cursor відкрив SDK, щоб запускати тих самих агентів, які працюють у desktop app, CLI та web app, із TypeScript-коду – локально або в Cursor cloud VM. Це важливо для воркшопу, бо показує перехід від “відкрив чат в IDE” до “вбудував coding agent у власний pipeline”. ([Cursor][2])

Для enterprise/production контексту варто згадати **Cursor Security Review**: beta для Teams/Enterprise, два типи always-on агентів – Security Reviewer для PR і Vulnerability Scanner для scheduled scans. Вони дивляться на vulnerabilities, auth regressions, privacy/data risks, prompt injection та auto-approved agent tools. ([Cursor][3])

**Як подати на воркшопі:** Cursor – найзручніший live-demo інструмент для фронтенду: показати Agent mode, rules, shadcn MCP, Figma link, потім попросити реалізувати екран у Next.js.

---

### Claude Code: terminal-first агент для “серйозної” роботи з репою

Claude Code описується Anthropic як agentic coding tool, який читає codebase, редагує файли, запускає команди й інтегрується з dev tools; він доступний у terminal, IDE, desktop app і browser. ([Claude API Docs][4])

Сильна сторона Claude Code – **структурованість агентного середовища**: `CLAUDE.md` для інструкцій, Skills для повторюваних workflows, Hooks для автоматичних дій до/після tool calls, MCP для підключення Figma/Jira/Slack/DB, Subagents для ізоляції дослідження або великих задач. Anthropic прямо радить додавати конфігурацію поступово: якщо Claude двічі помилився з convention – додати в `CLAUDE.md`; якщо ви повторюєте prompt – зробити Skill; якщо постійно копіюєте дані з іншого сервісу – підключити MCP. ([Claude][5])

Hooks – окрема хороша тема для воркшопу: це shell commands, HTTP endpoints або LLM prompts, які автоматично спрацьовують у життєвому циклі Claude Code, наприклад після редагування файлу або перед commit. ([Claude][6])

**Як подати:** Claude Code – це “agent in terminal”, добре підходить для частини про production discipline: plan-first, run tests, no destructive commands, subagents for research.

---

### MCP – головна “шина контексту”

MCP зараз фактично став стандартним способом дати агентам доступ до зовнішніх систем: Figma, shadcn registry, GitHub, docs, Jira, Slack, DB, browser automation. Anthropic формулює це як open-source standard для AI-tool integrations: MCP server дає Claude Code доступ до tools, databases і APIs, щоб агент міг читати й діяти напряму, а не працювати з copy-paste. ([Claude API Docs][7])

Для фронтенду головна комбінація:

```text
Figma MCP       → дизайн-контекст
shadcn MCP      → компоненти, blocks, registries
Next.js DevTools MCP → runtime/log/debug context
GitHub MCP      → issues, PRs
Playwright MCP  → browser QA / visual check
```

Важлива частина воркшопу – безпека. Anthropic прямо попереджає: third-party MCP servers треба використовувати на власний ризик, бо вони можуть нести prompt injection risk, особливо коли fetch-ать untrusted content. ([Claude API Docs][7])

---

## 3. Figma workflow: що показувати

### Figma MCP

Figma remote MCP server дозволяє підключити Figma Design / Figma Make / FigJam до AI dev tools, включно з Claude Code, Cursor, VS Code, Codex і Gemini CLI. Практично це працює так: у Figma копіюєш link to selection або frame URL, вставляєш у Cursor/Claude Code, і агент отримує scoped дизайн-контекст саме по цьому node. ([Figma Help Center][8])

У Figma MCP guide є дуже корисна для воркшопу деталь: для Cursor рекомендований шлях – встановити Figma plugin командою `/add-plugin figma`; для Claude Code – `claude plugin install figma@claude-plugins-official`. Плагін включає MCP config, skills для implementation, Code Connect і design-system rules. ([GitHub][9])

### Figma Code Connect

Code Connect – це спосіб сказати агенту: “цей Figma Button відповідає ось цьому React Button, а variant/size/disabled у Figma мапляться на такі props у коді”. Figma MCP із Code Connect може передавати agent-у import statements, component snippets, prop mappings і source paths – тобто не просто “як виглядає дизайн”, а “як це правильно реалізувати в нашій codebase”. ([Figma Developer Docs][10])

Для воркшопу це золота тема: показати, чому “Figma → screenshot → AI” гірше, ніж “Figma MCP + Code Connect + real shadcn/custom components”.

**Ідеальний demo prompt:**

```text
Use the Figma link below as the source of truth.
Inspect our existing components before writing code.
Map the design to existing shadcn/ui components where possible.
If a Figma component has Code Connect mapping, use the mapped React component.
First produce an implementation plan. Do not edit files yet.
```

---

## 4. DESIGN.md: що це і як пояснити

**DESIGN.md** – це markdown-файл із design-system правилами, який AI agents можуть читати як джерело візуальної правди: brand, tokens, typography, spacing, radius, shadows, motion, accessibility, component behavior, do/don’t rules.

Google Stitch у квітні 2026 відкрив draft specification для DESIGN.md. Google пояснює, що DESIGN.md дозволяє export/import design rules між проєктами, щоб Stitch і AI agents не “вгадували intent”, а знали, для чого колір використовується, і могли перевіряти рішення проти WCAG accessibility rules. ([blog.google][11])

Практичне місце DESIGN.md у твоєму воркшопі:

```text
AGENTS.md / CLAUDE.md / Cursor rules → як будувати
DESIGN.md                         → як має виглядати
Figma link / MCP                  → конкретний екран
shadcn registry                   → з яких компонентів складати
```

Мінімальний `DESIGN.md` для Next + Tailwind + shadcn:

```md
# DESIGN.md

## Product
- Product type: SaaS dashboard
- Audience: technical users
- Tone: precise, calm, high-signal

## Visual direction
- Style: clean, dense, professional, minimal
- Avoid: generic gradient SaaS look, oversized cards, random glassmorphism

## Tokens
- Radius: use existing shadcn radius tokens
- Spacing: compact, 8px rhythm
- Typography: use semantic hierarchy, no arbitrary font sizes
- Color: use CSS variables from globals.css, never hardcoded hex in components

## Components
- Prefer shadcn/ui primitives.
- Compose screens from Card, Button, Dialog, Sheet, Tabs, Table, Badge, DropdownMenu.
- Do not create one-off components if an existing primitive fits.

## Interaction states
- Every interactive element must define hover, focus-visible, disabled, loading and error states.
- Keyboard navigation is required.

## Accessibility
- Target WCAG 2.2 AA.
- Preserve visible focus rings.
- Do not use low-contrast muted text for primary content.

## Responsive behavior
- Mobile-first.
- Tables must degrade into cards or horizontal scroll with clear affordance.
- Sidebars collapse to Sheet on mobile.
```

**Важливий нюанс:** DESIGN.md не замінює Figma для команди з реальним дизайн-процесом. Краще формулювання: DESIGN.md – це “agent-readable style guide”. Якщо є Figma design system, краще підключити Figma MCP + Code Connect; якщо дизайну немає або потрібен швидкий brand-consistent прототип – DESIGN.md дуже корисний.

---

## 5. Чому Next.js + Tailwind + shadcn зараз ідеальна база для AI frontend

### Next.js 16

Next.js 16 приніс Cache Components, Next.js DevTools MCP, `proxy.ts` замість `middleware.ts`, стабільний Turbopack, React Compiler support, покращене routing/prefetching і React 19.2 support. Для AI frontend особливо цікаві дві речі: **Turbopack як default bundler** і **Next.js DevTools MCP**, який дає AI agents контекст про routing, caching, rendering behavior, browser/server logs, stack traces і active route. ([nextjs.org][12])

App Router лишається базовою архітектурою: file-system router на React Server Components, Suspense і Server Functions. ([nextjs.org][13])

### Tailwind v4

Tailwind v4 перейшов на CSS-first configuration: замість `tailwind.config.js` більшість токенів можна тримати прямо в CSS через `@theme`, а design tokens стають CSS variables. Це дуже добре для AI, бо агент бачить `globals.css` і може використовувати реальні tokens, а не вигадувати hex/radius/spacing. ([tailwindcss.com][14])

Tailwind v4.1 додав нові utilities, variants і DX покращення, включно з text-shadow та masks – це можна згадати як “Tailwind став ще більш expressible для AI-generated UI”. ([tailwindcss.com][15])

### shadcn/ui

shadcn/ui зараз важливий не тільки як component set, а як **distribution model для AI-native design system**. Офіційний shadcn MCP server дозволяє AI assistants browse/search/install components з registries через natural language; він підтримує public, private і namespaced registries. ([ui.shadcn.com][16])

У 2026 shadcn також активно розвиває blocks і registry mechanics: усі blocks доступні для Radix і Base UI variants, а CLI підтягує правильний variant залежно від project config. ([ui.shadcn.com][17]) У травні 2026 shadcn додав підтримку `package.json#imports` і target aliases у `shadcn@4.7.0`, що корисно для monorepos і internal UI packages. ([ui.shadcn.com][18])

---

## 6. v0 / Stitch / Figma Make / Lovable / Bolt – як позиціонувати

Для твого воркшопу я б не робив їх головними, але дав би як landscape.

**v0** – найрелевантніший для Next + Tailwind + shadcn. Vercel Academy описує v0 як agent, що перетворює natural language prompts у deployable apps / production-ready React code, тренований на React, Tailwind і shadcn/ui best practices. ([Vercel][19])

Vercel також прямо просуває ідею **AI-native design system**: tokens + shadcn/ui components + blocks + registry. Registry може працювати через MCP і заземлювати generation у Cursor/Windsurf/v0. ([Vercel][20])

**Google Stitch** – дизайн-first UI generation. У березні 2026 Google описав новий Stitch як “vibe design” із voice canvas, design critique, real-time updates, Stitch MCP server, SDK і exports у dev tools. ([blog.google][21]) Його зручно згадати в частині про DESIGN.md.

**Figma Make** – prompt-to-prototype / app builder усередині Figma. Для воркшопу корисний кут: Figma Make може тримати component docs ближче до artifact і генерувати props, variants, states, hover/focus/keyboard behavior, validation і test cases у тому ж workspace. ([Figma][22])

**Lovable/Bolt/Replit** – гарні для non-dev або rapid MVP, але для твоєї цілі краще позиціонувати як “швидкий прототип”, не як production frontend workflow. Для production-grade Next/shadcn codebase сильніші Cursor/Claude Code + MCP + repo rules.

---

## 7. Рекомендована структура воркшопу

### Блок 1 – “AI frontend у 2026: від autocomplete до agents”

Пояснити різницю:

```text
Autocomplete       → дописує рядок
Chat               → генерує шматок коду
Agent              → планує, редагує файли, запускає команди
MCP-enabled agent  → працює з Figma, registry, GitHub, docs, browser
Team workflow      → rules, skills, hooks, CI, code review, security review
```

### Блок 2 – setup для демо

```bash
pnpm create next-app@latest ai-frontend-workshop
cd ai-frontend-workshop

pnpm dlx shadcn@latest init
pnpm dlx shadcn@latest add button card dialog sheet tabs table badge dropdown-menu
```

Далі додати:

```text
CLAUDE.md або AGENTS.md
DESIGN.md
.cursor/rules/*
.cursor/mcp.json або .mcp.json
components.json
```

### Блок 3 – shadcn MCP

Показати natural language workflow:

```text
Show me available dashboard blocks from the shadcn registry.
Install a sidebar layout and data table components.
Create a responsive admin dashboard using existing shadcn components.
```

### Блок 4 – Figma MCP

Показати:

1. Вибрати frame у Figma.
2. Copy link to selection.
3. Дати Cursor/Claude Code prompt.
4. Попросити спочатку plan.
5. Потім implement.
6. Запустити `pnpm lint`, `pnpm typecheck`, browser check.

### Блок 5 – DESIGN.md

Показати два однакові prompts:

```text
Build a pricing page.
```

і

```text
Read DESIGN.md first. Build a pricing page that follows the design system exactly.
Use existing shadcn components and CSS variables. Do not hardcode colors.
```

Це добре демонструє, чому agents потребують design context.

### Блок 6 – guardrails

Обов’язково включити safety:

```text
- Немає доступу до production secrets.
- Немає destructive commands без explicit human approval.
- DB credentials тільки read-only у dev.
- Agent не робить deploy самостійно.
- Кожна зміна проходить lint/typecheck/test.
- Великі зміни – тільки через plan-first.
- MCP servers – тільки trusted.
```

Це не формальність: у 2026 security тема стала центральною, тому що agents отримують реальні permissions, а не просто пишуть текст.

---

## 8. Моя рекомендована “канонічна” workflow-схема

```text
1. Product intent
   → короткий brief: що будуємо, для кого, constraints

2. Design context
   → Figma frame URL або DESIGN.md

3. Engineering context
   → CLAUDE.md / AGENTS.md / .cursor/rules

4. Component context
   → shadcn/ui + internal registry + Code Connect

5. Agent plan
   → агент читає repo і пропонує implementation plan

6. Human review
   → людина підтверджує scope

7. Implementation
   → агент змінює файли

8. Verification
   → lint, typecheck, tests, Playwright/browser check

9. Design QA
   → screenshot compare або manual visual review

10. PR review
   → human + AI security/code review
```

## 9. Найкращий angle для твого воркшопу

Я б назвав це приблизно так:

**“AI Frontend Workflow 2026: building production UI with Cursor, Claude Code, Figma MCP, Next.js, Tailwind v4 and shadcn/ui”**

Ключова думка для аудиторії:

> AI добре пише UI тільки тоді, коли має не просто prompt, а систему: repo rules, design rules, component registry, Figma context і автоматичну перевірку.

Саме це відрізняє “vibe coding demo” від workflow, який реально можна принести в команду.

[1]: https://cursor.com/changelog/04-24-26 "Multitask, Worktrees, and Multi-root Workspaces · Cursor"
[2]: https://cursor.com/changelog/sdk-release "Build programmatic agents with the Cursor SDK · Cursor"
[3]: https://cursor.com/changelog/04-30-26 "Cursor Security Review · Cursor"
[4]: https://docs.anthropic.com/en/docs/claude-code/overview "Claude Code overview - Claude Code Docs"
[5]: https://code.claude.com/docs/en/features-overview "Extend Claude Code - Claude Code Docs"
[6]: https://code.claude.com/docs/en/hooks "Hooks reference - Claude Code Docs"
[7]: https://docs.anthropic.com/en/docs/claude-code/mcp "Connect Claude Code to tools via MCP - Claude Code Docs"
[8]: https://help.figma.com/hc/en-us/articles/35281350665623-Figma-MCP-collection-How-to-set-up-the-Figma-remote-MCP-server-preferred "Figma MCP collection: How to set up the Figma remote MCP server (preferred) – Figma Learn - Help Center"
[9]: https://github.com/figma/mcp-server-guide "GitHub - figma/mcp-server-guide: A guide on how to use the Figma MCP server · GitHub"
[10]: https://developers.figma.com/docs/figma-mcp-server/code-connect-integration/ "Code Connect integration | Developer Docs"
[11]: https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-design-md/ "Stitch app’s DESIGN.md format is now open-source for designers"
[12]: https://nextjs.org/blog/next-16 "Next.js 16 | Next.js"
[13]: https://nextjs.org/docs/app "Next.js Docs: App Router | Next.js"
[14]: https://tailwindcss.com/blog/tailwindcss-v4 "Tailwind CSS v4.0 - Tailwind CSS"
[15]: https://tailwindcss.com/blog/tailwindcss-v4-1 "Tailwind CSS v4.1: Text shadows, masks, and tons more - Tailwind CSS"
[16]: https://ui.shadcn.com/docs/mcp "MCP Server - shadcn/ui"
[17]: https://ui.shadcn.com/docs/changelog/2026-02-blocks "February 2026 - Blocks for Radix and Base UI - shadcn/ui"
[18]: https://ui.shadcn.com/docs/changelog "Changelog - shadcn/ui"
[19]: https://vercel.com/academy/ai-sdk/ui-with-v0 "UI with v0 | Vercel Academy"
[20]: https://vercel.com/blog/ai-powered-prototyping-with-design-systems "AI-powered prototyping with design systems - Vercel"
[21]: https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-ai-ui-design/ "Design UI using AI with Stitch from Google Labs"
[22]: https://www.figma.com/solutions/ai-component-documentation-generator/ "AI Component Documentation Generator | Figma Make"
