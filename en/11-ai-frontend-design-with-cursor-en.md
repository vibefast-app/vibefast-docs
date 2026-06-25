# Designing Beautiful UI with AI: A Complete Cursor Prompt Guide

[English](../en/11-ai-frontend-design-with-cursor-en.md) · [繁中](../zh/11-ai-frontend-design-with-cursor-zh.md) · [Español](../es/11-ai-frontend-design-with-cursor-es.md) · [日本語](../jp/11-ai-frontend-design-with-cursor-jp.md) · [Português (BR)](../pt-br/11-ai-frontend-design-with-cursor-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~12 minutes

-----

## You Don’t Need to Learn Design — You Need to Describe What You Want

Most people’s AI frontend results look like: “it works, but it’s ugly.”

That’s not because AI can’t design. It’s because the instructions weren’t specific enough. “Make me a nice homepage” and “Make me a SaaS homepage with a dark background, orange CTA button, and IBM Plex Mono for headlines” produce completely different results.

This tutorial teaches you how to give the second kind of instruction.

-----

## A Few Concepts to Know First

You don’t need to study these deeply — just enough to understand what AI is talking about.

### What Are React and JSX?

**React** is the most widely used frontend framework today. Your pages are built from individual “components” — each component is responsible for one section of the UI.

**JSX** is the syntax React uses, which lets you write HTML-like structure directly inside JavaScript:

```tsx
// This is a React component
export default function ProductCard({ name, price }) {
  return (
    <div className="p-4 border rounded-lg">
      <h2>{name}</h2>
      <p>${price}</p>
    </div>
  )
}
```

The curly braces `{}` contain JavaScript expressions. Everything else looks like HTML.

### What is Remix?

**Remix** is a full-stack framework built on React — the frontend framework vibefast.app uses. Key concepts:

- **loader**: a function that fetches data from the backend when a page loads
- **action**: a function that handles user actions when a form is submitted
- **route**: each file maps to a URL path — `app/routes/shop.tsx` corresponds to `/shop`

You don’t need to fully understand these. When you encounter something confusing, ask AI to explain it.

### What is Tailwind CSS?

**Tailwind** is the CSS framework vibefast.app uses. Instead of writing CSS, you apply style directly using class names in your HTML:

```tsx
// Without Tailwind
<button style={{ backgroundColor: 'orange', padding: '12px 24px', borderRadius: '8px' }}>
  Buy
</button>

// With Tailwind
<button className="bg-orange-500 px-6 py-3 rounded-lg text-white font-semibold">
  Buy
</button>
```

Tailwind class names are intuitive: `bg-orange-500` is an orange background, `px-6` is horizontal padding, `rounded-lg` is rounded corners. You don’t need to memorize them — AI knows them all. Just describe the effect you want.

### Common UI Terms You’ll Hear

- **Hero**: the large top section of a page, usually with a headline, subheadline, and CTA button
- **CTA (Call to Action)**: a button that prompts users to do something — “Buy Now,” “Start Free Trial”
- **Card**: a bordered or shadowed content block
- **Grid**: items arranged in columns — like a 3-column feature section
- **Responsive**: adapts correctly to both mobile and desktop screen sizes

-----

## How to Describe Your Design Style to AI

This is the most important skill. When giving design instructions, cover these dimensions:

### 1. Overall Style

```
"Dark theme"
"Minimal white, lots of whitespace"
"Tech aesthetic with glow effects"
"Warm, personal feel — suits an individual brand"
```

### 2. Colors

Don’t say “blue” — be specific with Tailwind color names or hex values:

```
# Too vague
"Use a blue theme"

# Specific enough
"Primary color: orange-500 (#f97316), background: zinc-950, text: zinc-100"
```

Common Tailwind color families: `slate`, `zinc`, `neutral` (grays), `orange`, `blue`, `violet`, `emerald`. Each has shades from 50–950.

### 3. Typography

```
"Headlines in IBM Plex Mono (monospace, technical feel)"
"Body text in Inter (modern, readable)"
"Headlines in Bricolage Grotesque (distinctive sans-serif)"
```

If you’re not sure, just say “suggest a font pairing that suits a SaaS product” and AI will recommend something.

### 4. Element Details

```
"Buttons with pill shape (rounded-full)"
"Cards with a subtle border (border border-zinc-800)"
"Hover animation (hover:scale-105 transition)"
"Headline with an orange underline accent"
```

-----

## Walkthrough: Building a Hero Section Step by Step

I’ll use vibefast.app’s own homepage design as an example to demonstrate the full prompt flow.

### Round 1: Establish the Basic Structure

In Cursor, press `Cmd+L` and type:

```
Create a Hero section in apps/web/app/routes/_index.tsx.

Design specs:
- Dark background: bg-zinc-950
- Centered layout
- Small badge at the top with text "Edge-native · No Next.js · No Vercel", orange border
- Main headline: large, white, IBM Plex Mono font, text "The Cloudflare-Native Web App Template"
- Subheadline: gray, smaller, text "From idea to production in 40 minutes."
- Two buttons side by side: primary button in orange "Buy Now — $99", secondary button in transparent with border "View Demo"
- Full section vertically centered, minimum height 100vh
```

AI generates a complete JSX structure.

### Round 2: Refine the Details

After seeing the output, you’ll notice things to adjust. Continue in Cursor:

```
Adjust a few things:
1. Headline font size is too small — change to text-5xl md:text-7xl
2. Increase space between the two buttons — change gap-4 to gap-6
3. Add hover effect to the primary button: hover:bg-orange-400 transition-colors duration-200
4. Add a very subtle orange radial glow in the hero background
```

### Round 3: Add Decorative Details

```
Add a dot grid pattern to the hero background.
Use a very faint zinc-800 color.
The goal is depth without being distracting.
Similar to the background style on linear.app.
```

Three rounds later, you have a complete Hero section with near-professional design quality.

-----

## Use Screenshots to Show AI Reference Sites

This is the technique that most dramatically improves AI output quality.

Find a website design you like. Screenshot it. Drag it directly into Cursor’s chat panel. Then say:

```
Reference the design style in this screenshot and create a similar pricing card component.
Technical requirements:
- Tailwind CSS
- Remix JSX format
- Keep my existing zinc-950 dark theme
- Replace the text content with vibefast.app's pricing: $99 early bird, includes private repo and lifetime updates
```

Cursor has visual understanding — it can read the design from your screenshot and reimplement it in your tech stack.

**Good sites to screenshot for reference:**

- [linear.app](https://linear.app) — dark, minimal, strong tech aesthetic
- [vercel.com](https://vercel.com) — modern SaaS style
- [stripe.com](https://stripe.com) — clean, high trust
- [tailwindui.com](https://tailwindui.com) — component references directly

-----

## Common Problems and How to Fix Them

### Problem 1: AI Produced a Design That Breaks on Mobile

**Symptom:** Looks great on desktop, but text is too large or layout falls apart on mobile.

**Fix:**

```
The current design has issues on mobile. Add responsive adjustments:
- Headline: text-3xl on mobile, text-5xl on tablet, text-7xl on desktop
- Two buttons: stack vertically on mobile (flex-col), side by side on desktop (sm:flex-row)
- Horizontal padding: px-4 on mobile, px-8 on desktop
```

### Problem 2: The Design Looks Generic — Like Every Other AI-Generated Site

**Symptom:** Blue and white, rounded cards, box shadows — indistinguishable from a thousand other apps.

**Fix:** Give more specific personality instructions:

```
The current design is too generic. Make these adjustments to give it more character:
1. Replace all blue with orange (orange-500)
2. Add strikethrough to "No Next.js" in the main headline
3. Add a terminal-style code block showing the three installation commands
4. Change the font to IBM Plex Mono for an engineering feel
```

### Problem 3: You Wanted One Small Change But AI Changed Too Much

**Symptom:** You said “change the button color to orange” and AI rewrote half the component.

**Fix:** Be more precise:

```
Change only this one thing and leave everything else untouched:
Change bg-blue-500 to bg-orange-500 in the <button> element.
Do not modify anything else.
```

-----

## Small Techniques That Immediately Improve Design Quality

These are things I learned building vibefast.app’s homepage. Each one is minor, but combined they make a noticeable difference.

**Add transition animations**

```tsx
// Subtle scale on hover
className="hover:scale-105 transition-transform duration-200"

// Color transition on hover
className="hover:bg-orange-400 transition-colors duration-200"
```

**Gradient text**

```tsx
className="bg-gradient-to-r from-white to-zinc-400 bg-clip-text text-transparent"
```

**Refined card borders**

```tsx
// More refined than a plain white border
className="border border-zinc-800 hover:border-zinc-600 transition-colors"
```

**Glow effect on dark backgrounds**

```tsx
// Orange radial glow
<div className="absolute inset-0 bg-[radial-gradient(ellipse_at_center,rgba(249,115,22,0.15),transparent_70%)] pointer-events-none" />
```

-----

## Complete Prompt Template

Copy this template, replace the bracketed content, and use it directly:

```
Create a [component name] in [file path].

Design style:
- Theme: [dark / light]
- Primary color: [e.g. orange-500]
- Background: [e.g. zinc-950]
- Typography: [e.g. IBM Plex Mono for headlines, Inter for body]

Content:
- [Describe what this component should display]

Technical requirements:
- Tailwind CSS
- Remix JSX format
- Include hover animations
- Must be responsive (correct display on both mobile and desktop)

Reference style: [if you have a reference site, describe it or attach a screenshot]
```

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on August 1, 2026.
