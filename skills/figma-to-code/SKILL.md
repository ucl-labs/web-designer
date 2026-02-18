---
name: figma-to-code
description: Enforces Figma-to-code best practices for Next.js + Tailwind v4 + shadcn/ui + Vercel projects. Auto-triggers when the user works with Figma designs, mentions implementing a design, or asks about design-to-code workflows. Prevents common mismatches and guides beginners through the correct implementation order.
---

# Figma-to-Code Guardrails

Enforce best practices every time a Figma design is translated into production code. Prevent the painful back-and-forth of designs that don't match.

## Your Stack

All implementations MUST use this stack unless the user explicitly says otherwise:

| Layer | Technology | Version |
|-------|-----------|---------|
| Framework | Next.js (App Router) | 16+ |
| Language | TypeScript | 5+ |
| Styling | Tailwind CSS | v4 (CSS-first config) |
| UI Library | shadcn/ui (New York style) | Latest |
| Icons | Lucide React | Latest |
| Animations | Framer Motion | 12+ |
| Deployment | Vercel | Latest |

**Conventions:**
- Use `@/` path aliases for all imports
- React Server Components by default — add `"use client"` only when needed (state, effects, event handlers)
- Fonts via `next/font/google` — NEVER use CDN `<link>` tags
- Components: `@/components/ui/` for shadcn primitives, `@/components/` for composites
- Utilities: `@/lib/utils.ts` for `cn()` helper (clsx + tailwind-merge)

## Tailwind v4 Rules

**Critical:** Tailwind v4 does NOT use `tailwind.config.ts`. Never generate one.

- Design tokens live in `app/globals.css` using the `@theme` directive
- Colors use CSS custom properties: `--color-primary: oklch(0.7 0.15 200);`
- Reference tokens with: `bg-primary`, `text-primary`, etc.
- Import Tailwind in CSS: `@import "tailwindcss";`
- PostCSS plugin: `@tailwindcss/postcss` (not the old `tailwindcss` plugin)

**Tip:** If a project has a `tailwind.config.ts`, it's using Tailwind v3 — adapt accordingly. Check `package.json` to confirm the version.

## Figma Design Rules

These rules ensure Figma designs translate cleanly to code. When reviewing a Figma file or giving design advice, enforce these:

### 1. Auto Layout is Mandatory
Every frame MUST use Auto Layout. Without it, Figma exports absolute coordinates that break responsive layouts.

- Auto Layout → `flex` / `grid` in CSS (clean, responsive)
- No Auto Layout → `position: absolute` with pixel values (breaks on every screen size)

**Tip:** In Figma, select a frame and press `Shift + A` to add Auto Layout.

### 2. Use the 4px Spacing Scale
All spacing (padding, gaps, margins) must be multiples of 4px. This maps directly to Tailwind's spacing scale:

| Figma (px) | Tailwind Class | Use For |
|------------|---------------|---------|
| 4 | `p-1`, `gap-1` | Tight inner spacing |
| 8 | `p-2`, `gap-2` | Small spacing |
| 12 | `p-3`, `gap-3` | Compact elements |
| 16 | `p-4`, `gap-4` | Standard spacing |
| 20 | `p-5`, `gap-5` | Comfortable spacing |
| 24 | `p-6`, `gap-6` | Section padding |
| 32 | `p-8`, `gap-8` | Large spacing |
| 40 | `p-10`, `gap-10` | Section breaks |
| 48 | `p-12`, `gap-12` | Hero padding |
| 64 | `p-16`, `gap-16` | Page-level spacing |

**If you see odd values** like 13px, 7px, 22px — flag it. Say: "This spacing (Xpx) doesn't align with Tailwind's 4px scale. The closest options are [Y]px or [Z]px. Which would you prefer?"

### 3. Name Layers Semantically
Good naming helps Claude understand component intent and structure.

| Bad (default) | Good (semantic) |
|---|---|
| `Frame 847` | `HeroSection` |
| `Group 12` | `PricingCard` |
| `Rectangle 3` | `CTAButton` |
| `Text 19` | `SectionHeading` |

**Convention:** Use PascalCase for components, camelCase for elements within them. Separate hierarchy with `/`: `HeroSection/CTAButton`, `Navbar/Logo`, `PricingCard/Price`.

### 4. Design at Standard Breakpoints
Create frames at these widths to match Tailwind's responsive breakpoints:

| Breakpoint | Width | Tailwind Prefix |
|-----------|-------|----------------|
| Mobile | 375px | Default (mobile-first) |
| Tablet | 768px | `md:` |
| Desktop | 1440px | `lg:` or `xl:` |

**Tip:** At minimum, design mobile and desktop. Tablet can often be interpolated.

### 5. Colors Must Match Your Token System
Don't use random hex values. Define a palette and stick to it.

Colors should map to your `globals.css` `@theme` variables:
```css
@theme {
  --color-primary: #value;
  --color-secondary: #value;
  --color-accent: #value;
  --color-background: #value;
  --color-foreground: #value;
  --color-muted: #value;
  --color-border: #value;
}
```

When implementing, use `bg-primary` not `bg-[#6366F1]`. If a Figma color doesn't match an existing token, ask: "This color (#hex) isn't in your design tokens. Should I add it as a new token, or use the closest existing one?"

### 6. Use Figma Components for Reusable UI
Encourage creating Figma components (with variants) for elements that repeat: buttons, cards, inputs, badges. These map 1:1 to React components and shadcn/ui primitives.

## Implementation Workflow

**ALWAYS follow this order. Do not skip steps.**

### Step 1: Pre-flight Check
Before ANY implementation, run `/design-preflight` or manually verify:
- [ ] Figma URL is available
- [ ] Design system rules exist for this project (`/create-design-system-rules`)
- [ ] Fonts are installed in the project (`next/font`)
- [ ] Design tokens are defined in `globals.css`
- [ ] Figma uses Auto Layout on all frames

### Step 2: Component-by-Component
**NEVER implement a full page in one go.** Break it into components:

```
Page Layout
├── Navbar          ← implement first
├── HeroSection     ← implement second
├── FeatureGrid     ← implement third
├── PricingCards     ← implement fourth
├── Footer          ← implement fifth
└── Page assembly   ← compose last
```

For each component:
1. `/implement-design [Figma URL to specific component node]`
2. Review output in browser side-by-side with Figma
3. Fix mismatches: tell Claude specifically what's wrong
4. Move to next component

### Step 3: Compose the Page
After all components pass review individually, assemble them in the page file.

### Step 4: Responsive Check
Test at all 3 breakpoints (375px, 768px, 1440px) before considering it done.

### Step 5: Deploy
Run `/deploy` to push to Vercel.

## Quality Checklist

After EVERY component implementation, run through this checklist:

- [ ] **Colors** — match Figma exactly, using CSS variables (not hex literals)
- [ ] **Typography** — font family, size, weight, line-height all match
- [ ] **Spacing** — padding, gap, margin match Figma (on the 4px scale)
- [ ] **Border radius** — matches Figma value
- [ ] **Shadows** — matches Figma shadow values
- [ ] **Responsive** — works at 375px, 768px, and 1440px
- [ ] **shadcn/ui** — used existing primitives where applicable (Button, Card, Input, etc.)
- [ ] **No arbitrary values** — no `p-[13px]` or `text-[17px]` unless absolutely required
- [ ] **Accessibility** — proper semantic HTML, alt text, keyboard navigation
- [ ] **Server/Client** — correct `"use client"` placement (only where needed)

## Common Beginner Mistakes

When you detect these, warn the user with the fix:

| Mistake | What Happens | Fix |
|---------|-------------|-----|
| No Auto Layout in Figma | Code uses absolute positioning, breaks on resize | Add Auto Layout to every frame (`Shift + A`) |
| Odd spacing (13px, 7px) | Arbitrary Tailwind values `p-[13px]` everywhere | Stick to 4px grid: 4, 8, 12, 16, 20, 24, 32... |
| Font not installed in project | Browser shows fallback font, everything looks "off" | Install with `next/font/google` before implementing |
| Implementing full page at once | 50 things are wrong, hard to debug any single one | Go component-by-component |
| Hardcoded hex colors | Colors drift across components, hard to maintain | Define in `@theme` in globals.css, use token classes |
| No mobile design in Figma | Desktop layout crammed into 375px | Design mobile-first, then scale up |
| Skipping `/create-design-system-rules` | Claude doesn't know your project conventions | Always run it once per project |
| Using `tailwind.config.ts` with Tailwind v4 | Config is ignored, styles break | Tailwind v4 uses `@theme` in CSS, no config file |
| Generic layer names | Claude can't infer component intent | Name layers: `HeroSection/CTAButton` not `Frame 12/Rectangle 3` |
| Ignoring shadcn/ui | Rebuilding buttons/cards from scratch | Check shadcn/ui first — `npx shadcn@latest add button` |

## Rules

1. **Always enforce the implementation workflow.** If someone tries to skip steps, remind them of the correct order.
2. **Flag spacing mismatches.** If a Figma value isn't on the 4px scale, ask before using an arbitrary value.
3. **Flag missing tokens.** If a color/font doesn't match existing tokens, ask before hardcoding.
4. **Component-by-component only.** Refuse to implement an entire page in a single pass — break it down.
5. **Check the stack.** Before generating code, verify the project's actual Tailwind version (v3 vs v4) by reading `package.json`.
6. **Prefer shadcn/ui.** Always check if a shadcn/ui component exists before building from scratch.
7. **No CDN fonts.** Always use `next/font/google` for font loading.
8. **Be specific with feedback requests.** After implementation, ask: "Compare this side-by-side with your Figma. What specific differences do you see?" — not vague "does this look good?"
