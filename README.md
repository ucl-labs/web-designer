# web-designer

> A Claude Code plugin by [UpClick Labs](https://github.com/upclicklabs)

Figma-to-code guardrails for Next.js + Tailwind v4 + shadcn/ui + Vercel workflows.

Prevents the frustrating cycle of implementing a design only to find it looks nothing like Figma. Enforces best practices at every step — from how you structure your Figma file to how the code is generated and deployed.

---

## Install

In Claude Code: **Settings → Plugins → Add Plugin** → paste this URL:

```
https://github.com/upclicklabs/web-designer
```

---

## Commands

| Command | What It Does |
|---------|-------------|
| `/design-preflight` | Pre-implementation checklist — catches missing fonts, tokens, and Figma issues before you start |

---

## Skills

| Skill | Auto-Triggers When |
|-------|-------------------|
| `figma-to-code` | You mention Figma designs, implementing a design, or ask about design-to-code workflows |

---

## The Workflow (7 Steps)

```
1. /design-preflight              ← Check your project is ready
2. /create-design-system-rules    ← Teach Claude your conventions (once per project)
3. Break your page into components ← Navbar, Hero, Cards, Footer, etc.
4. /implement-design [figma-url]  ← One component at a time
5. Review side-by-side            ← Browser next to Figma
6. Tell Claude what's off         ← "The heading should be 32px not 24px"
7. /deploy                        ← Push to Vercel
```

---

## Figma Cheat Sheet (for beginners)

| Rule | Why | Figma Shortcut |
|------|-----|---------------|
| Use Auto Layout on everything | Maps to CSS flexbox (responsive) | `Shift + A` |
| Spacing in multiples of 4px | Maps to Tailwind's spacing scale | — |
| Name layers semantically | Helps Claude understand your design | Double-click layer name |
| Design at 375px, 768px, 1440px | Matches Tailwind breakpoints | Frame tool → set width |
| Use consistent colors | Maps to your CSS design tokens | Figma Styles panel |
| Create components for repeated elements | Maps to React components | `Cmd/Ctrl + Alt + K` |

---

## Stack

- Next.js 16+ (App Router)
- TypeScript
- Tailwind CSS v4 (CSS-first config)
- shadcn/ui (New York style)
- Lucide React icons
- Framer Motion
- Vercel deployment
