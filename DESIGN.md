---
trigger: always_on
description: "Anti-slop design system: banned AI aesthetics, strict 2-tier typography, semantic color tokens, and responsive layout standards for all frontend code."
---

# Antigravity Global Anti-Slop Design System (DESIGN.md)

> **Core Philosophy:** Clean, editorial, responsive-by-default design. Zero hallucinated AI aesthetic slop. Every interface looks hand-crafted by a senior human product designer.

---

## 1. The Anti-Slop Banned List (Zero Tolerance)

Autonomous agents must NEVER generate the following common AI design patterns:

| Pattern | Status | Replacement |
| :--- | :--- | :--- |
| **Rainbow / Purple Gradient Cards** | ❌ BANNED | High-contrast solid backgrounds (`bg-background`, `bg-secondary`) with crisp 1px borders (`border-border`). |
| **Gradient Text Masks** (`bg-clip-text text-transparent`) | ❌ BANNED | Clean solid typography with bold optical weights and intentional tracking. |
| **Arbitrary Raw Hex in Tailwind** (`bg-[#121317]`, `text-[#64748b]`) | ❌ BANNED | Semantic token classes only (`bg-primary`, `text-muted-foreground`, `border-border`). |
| **Multi-Font Chaos** (3+ random fonts) | ❌ BANNED | Strict 2-tier font hierarchy (1 clean sans + 1 optional editorial serif/display). |
| **Cards Inside Cards Inside Cards** | ❌ BANNED | Flat semantic sections separated by whitespace or single-layer cards. |
| **Emoji as Icons** (🚀, 💡, 🔥 in UI) | ❌ BANNED | Dedicated Lucide React SVG icons only. |
| **Generic AI Marketing Fluff** ("Revolutionize your workflow") | ❌ BANNED | Punchy, concrete, human copy with specific nouns and verifiable numbers. |
| **Fixed Width Containers** (`w-[1200px]`, `min-w-[800px]`) | ❌ BANNED | Fluid responsive containers (`max-w-7xl w-full px-4 sm:px-6 lg:px-8`). |

---

## 2. Typography Architecture

### A. Strict 2-Tier Font Hierarchy
1. **Primary Interface Font (Sans-Serif):** High-craft modern variable sans for UI, body, labels, tables, and buttons.
   * *Recommended choices:* **Geist**, **Inter**, **Satoshi**, **Switzer**.
2. **Display / Headline Font (Editorial Serif or Character Sans):** Reserved exclusively for `H1` and major feature `H2` headlines.
   * *Recommended choices:* **Instrument Serif**, **Newsreader**, **Cal Sans**, **Syne**, **Cabinet Grotesk**.
   * *Rule:* Never use the display font for body copy, buttons, inputs, or metadata.

### B. Responsive Fluid Type Scale
Never let giant desktop headings cause text wrap or horizontal overflow on mobile:
* **H1 Hero:** `text-3xl sm:text-5xl lg:text-6xl font-bold tracking-tight leading-[1.08]`
* **H2 Section:** `text-2xl sm:text-3xl lg:text-4xl font-semibold tracking-tight`
* **H3 Subsection:** `text-lg sm:text-xl font-medium tracking-tight`
* **Body Regular:** `text-sm sm:text-base leading-relaxed text-muted-foreground`
* **Caption / Label:** `text-xs uppercase tracking-wider font-semibold text-muted-foreground`

---

## 3. Color Tokens & Surface Hierarchy

### A. Semantic Token Enforcement
Every color MUST map to a semantic CSS custom property in `globals.css`. Never hardcode arbitrary hex codes in JSX/HTML.

```css
/* Canonical Design Tokens (globals.css) */
@layer base {
  :root {
    --background: #ffffff;
    --foreground: #09090b;
    --primary: #18181b;
    --primary-foreground: #fafafa;
    --secondary: #f4f4f5;
    --secondary-foreground: #18181b;
    --muted: #f4f4f5;
    --muted-foreground: #71717a;
    --accent: #f4f4f5;
    --accent-foreground: #18181b;
    --border: #e4e4e7;
    --radius: 0.75rem;
  }

  .dark {
    --background: #09090b;
    --foreground: #fafafa;
    --primary: #fafafa;
    --primary-foreground: #18181b;
    --secondary: #27272a;
    --secondary-foreground: #fafafa;
    --muted: #27272a;
    --muted-foreground: #a1a1aa;
    --accent: #27272a;
    --accent-foreground: #fafafa;
    --border: #27272a;
  }
}
```

### B. Tailwind Usage
Use semantic utilities directly:
* Surfaces: `bg-background`, `bg-secondary`, `bg-muted`
* Text: `text-foreground`, `text-muted-foreground`, `text-primary-foreground`
* Borders: `border-border`
* Interactive: `hover:bg-accent hover:text-accent-foreground`

---

## 4. Mobile-First Responsive Layout Engine

Every screen and component must look flawless from mobile (390px) up to ultra-wide (1440px+):

### A. Default Section Container
```tsx
<section className="w-full py-12 sm:py-20 lg:py-28">
  <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
    {/* Content */}
  </div>
</section>
```

### B. Mobile-First Grid Stepping
Never declare multi-column grids without a mobile fallback:
* **2-Column Features:** `grid grid-cols-1 md:grid-cols-2 gap-6 lg:gap-10`
* **3-Column Cards:** `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6`
* **4-Column Stats/Products:** `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 sm:gap-6`

### C. Zero Horizontal Overflow
Apply `overflow-x-clip` on the outermost app shell to strictly prevent mobile side-scrolling:
```tsx
<main className="min-h-screen w-full overflow-x-clip bg-background text-foreground">
  {children}
</main>
```

### D. Touch Target Compliance
Any button, link, tab, or interactive element must have a minimum clickable area of **44×44px** on touch viewports (`h-11 px-4` or equivalent).

---

## 5. Component Standards & Polish

1. **Border Radius Consistency:**
   * Outer cards: `rounded-2xl` (16px) or `rounded-xl` (12px).
   * Inner buttons / inputs: `rounded-lg` (8px).
   * Badges / tags: `rounded-full` pills.
   * *Rule:* Never mix random border radii across identical UI elements.

2. **Lucide SVG Icons:**
   * Always pass explicit sizing: `className="w-4 h-4"` or `className="w-5 h-5"`.
   * Never use unstyled raw SVGs or emojis in buttons.

3. **Micro-Interactions (On Intent Only):**
   * Use transitions on hover and tap (`transition-all duration-200 hover:scale-[1.02] active:scale-[0.98]`).
   * No looping infinite pulsing or spinning elements unless representing an active loading state.
