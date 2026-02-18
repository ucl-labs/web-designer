---
description: Run a pre-implementation checklist before translating any Figma design to code. Catches missing fonts, tokens, design system rules, and common Figma setup issues BEFORE you start — saving hours of frustrating back-and-forth.
---

# Design Preflight

> Run this BEFORE using `/implement-design`. It checks that your project and Figma file are ready for a clean translation.

## Trigger

User runs `/design-preflight` or `/design-preflight [figma-url]`, or asks to "check if my project is ready for Figma implementation."

## Inputs

- **Figma URL** (optional): If provided, note it for the implementation step. If not, remind the user they'll need one.

## Process

Run each check below. For each, output a pass ✅ or fail ❌ with a one-line fix.

### Check 1: Figma URL

**Check:** Did the user provide a Figma URL?

- ✅ **Pass:** URL is available — note it for implementation.
- ❌ **Fail:** "No Figma URL provided. Share a link in this format: `https://figma.com/design/FILE_KEY/FILE_NAME?node-id=X-Y`. Tip: Right-click a component in Figma → Copy link to selection."

### Check 2: Design System Rules

**Check:** Does this project have design system rules configured?

Look for any of these files:
- `CLAUDE.md` (search for "figma" or "design system" references)
- `.claude/rules/figma-design-system.md`
- `.cursor/rules/figma-design-system.mdc`

- ✅ **Pass:** Design system rules found.
- ❌ **Fail:** "No design system rules found. Run `/create-design-system-rules` first — this teaches Claude your project's conventions (component locations, styling approach, naming patterns)."

### Check 3: Fonts Installed

**Check:** Are fonts loaded via `next/font`?

Scan the project for:
- `next/font/google` or `next/font/local` imports
- Check `app/layout.tsx` or `app/layout.js` for font configuration

- ✅ **Pass:** Fonts detected — list which ones are installed.
- ❌ **Fail:** "No fonts configured via `next/font`. Add them to `app/layout.tsx` before implementing. Example:
  ```tsx
  import { Inter } from 'next/font/google'
  const inter = Inter({ subsets: ['latin'] })
  ```
  Make sure the font matches what's used in your Figma file."

### Check 4: Design Tokens Defined

**Check:** Are design tokens (colors, spacing) defined in the project?

For **Tailwind v4**: Look for `@theme` block in `app/globals.css`
For **Tailwind v3**: Look for `tailwind.config.ts` theme extensions

- ✅ **Pass:** Tokens found — list the defined colors.
- ❌ **Fail:** "No design tokens found. Add your color palette to `app/globals.css`:
  ```css
  @theme {
    --color-primary: #your-primary-color;
    --color-secondary: #your-secondary-color;
    --color-background: #your-bg-color;
    --color-foreground: #your-text-color;
  }
  ```
  Match these values exactly to your Figma color styles."

### Check 5: Tailwind Version

**Check:** What version of Tailwind is installed?

Read `package.json` and check the `tailwindcss` version.

- **v4+:** Confirm CSS-first config mode. Warn if a `tailwind.config.ts` exists (it's ignored in v4).
- **v3.x:** Note that config-based approach applies. Tokens go in `tailwind.config.ts`.
- ❌ **Fail:** "Tailwind CSS not found in dependencies. Install it: `npm install tailwindcss @tailwindcss/postcss`"

### Check 6: shadcn/ui Configured

**Check:** Is shadcn/ui installed and configured?

Look for `components.json` in the project root.

- ✅ **Pass:** shadcn/ui configured — list the style variant and component aliases.
- ❌ **Fail:** "shadcn/ui not configured. Initialize it: `npx shadcn@latest init`. This gives you polished, accessible UI primitives (Button, Card, Input, Dialog, etc.) that match common Figma patterns."

### Check 7: Component Scope

**Check:** Ask the user what they're implementing.

- "Are you implementing a **single component** (e.g., a Navbar, a PricingCard) or a **full page**?"

If full page:
- ⚠️ **Warning:** "Implementing a full page at once makes it hard to catch mismatches. Break it into components and implement each separately. Which component would you like to start with?"

## Output Format

```
## 🛫 Design Preflight Check

| # | Check | Status | Notes |
|---|-------|--------|-------|
| 1 | Figma URL | ✅ / ❌ | [details] |
| 2 | Design System Rules | ✅ / ❌ | [details] |
| 3 | Fonts Installed | ✅ / ❌ | [details] |
| 4 | Design Tokens | ✅ / ❌ | [details] |
| 5 | Tailwind Version | ✅ / ❌ | [details] |
| 6 | shadcn/ui | ✅ / ❌ | [details] |
| 7 | Component Scope | ✅ / ⚠️ | [details] |

### Result: X/7 passed

[If all pass]: "You're clear for takeoff! Run `/implement-design [figma-url]` to start with your first component."

[If failures exist]: "Fix the items above before implementing. This prevents the frustrating back-and-forth of designs that don't match."
```

## After Preflight

Once all checks pass, guide the user to:
1. `/implement-design [figma-url]` for the first component
2. Review side-by-side in browser
3. Iterate until it matches
4. Move to next component
