---
name: css-scroll-driven
description: Zero-JavaScript, off-main-thread CSS scroll-driven animations using animation-timeline scroll() and view(). Use for performant scroll reveals, reading progress indicators, parallax image headers, and scroll-linked element morphing.
allowed-tools: Read, Write, Edit, Glob, Grep
metadata:
  version: "1.0.0"
  stack: "css, modern-web, baseline-2024"
---

# Modern CSS Scroll-Driven Animations Master Skill

Create zero-JS, buttery 120 FPS scroll-linked animations running directly on the GPU compositor thread using native CSS `animation-timeline`.

---

## 1. Zero-JS Reading Progress Bar

```html
<div class="scroll-progress-bar"></div>
```

```css
@keyframes grow-progress {
  from { transform: scaleX(0); }
  to { transform: scaleX(1); }
}

.scroll-progress-bar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(90deg, #6366f1, #a855f7);
  transform-origin: 0% 50%;
  animation: grow-progress auto linear;
  animation-timeline: scroll();
  z-index: 9999;
}
```

---

## 2. In-View Reveal Animation (Fade & Rise on Scroll)

Trigger elements to fade and slide up automatically as they enter the viewport:

```css
@keyframes reveal-card {
  from {
    opacity: 0;
    transform: translateY(60px) scale(0.92);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.reveal-on-scroll {
  animation: reveal-card linear both;
  animation-timeline: view();
  /* Animates between entering the bottom 0% to covering 35% of the viewport */
  animation-range: entry 0% cover 35%;
}
```

---

## 3. Parallax Hero Image (Off-Main-Thread)

```html
<div class="parallax-container">
  <img src="/hero.jpg" class="parallax-img" alt="Hero" />
</div>
```

```css
.parallax-container {
  height: 60vh;
  overflow: hidden;
  position: relative;
}

@keyframes parallax-travel {
  from {
    transform: translateY(-15%);
  }
  to {
    transform: translateY(15%);
  }
}

.parallax-img {
  width: 100%;
  height: 130%;
  object-fit: cover;
  animation: parallax-travel linear both;
  animation-timeline: view();
  animation-range: entry 0% exit 100%;
}
```

---

## 4. Progressive Sticky Header Shrink

Shrink the site header automatically as the user scrolls away from the top:

```css
@keyframes header-shrink {
  to {
    height: 56px;
    background-color: rgba(10, 10, 10, 0.85);
    backdrop-filter: blur(12px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  }
}

header.sticky-header {
  position: sticky;
  top: 0;
  height: 96px;
  animation: header-shrink auto linear forwards;
  animation-timeline: scroll(root);
  animation-range: 0px 150px;
}
```

---

## 5. Fallback & Progressive Enhancement

Ensure browsers without `animation-timeline` support still see content in its final visible state:

```css
@supports not (animation-timeline: view()) {
  .reveal-on-scroll {
    opacity: 1;
    transform: none;
  }
}

@media (prefers-reduced-motion: reduce) {
  .reveal-on-scroll,
  .parallax-img,
  .scroll-progress-bar {
    animation: none !important;
  }
}
```
