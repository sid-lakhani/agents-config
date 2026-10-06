---
name: lenis-smooth-scroll
description: Physics-based smooth scrolling with Lenis (v1.1+). Use when building smooth inertia scrolling, smooth scroll anchor links, locked scroll modals/drawers, and coordinating scroll physics with modern React and Next.js applications.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
metadata:
  version: "1.1.0"
  stack: "lenis, react-lenis, nextjs"
---

# Lenis Smooth Scroll Master Skill

High-performance, physics-driven smooth scrolling using `lenis` for modern Next.js App Router and React applications.

---

## 1. Installation

```bash
npm install lenis
# Optional if using official React wrapper:
npm install lenis/react
```

---

## 2. Next.js App Router Global Setup

Create a dedicated client-side provider in `components/providers/SmoothScrollProvider.tsx` and wrap your root layout.

```tsx
// components/providers/SmoothScrollProvider.tsx
"use client";

import { useEffect, useRef } from "react";
import Lenis from "lenis";

export function SmoothScrollProvider({ children }: { children: React.ReactNode }) {
  const lenisRef = useRef<Lenis | null>(null);

  useEffect(() => {
    // Initialize Lenis with elite cinematic defaults
    const lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)), // easeOutExpo
      orientation: "vertical",
      gestureOrientation: "vertical",
      smoothWheel: true,
      wheelMultiplier: 1,
      touchMultiplier: 2,
    });

    lenisRef.current = lenis;

    // Connect RAF loop
    let rafId: number;
    function raf(time: number) {
      lenis.raf(time);
      rafId = requestAnimationFrame(raf);
    }
    rafId = requestAnimationFrame(raf);

    // Expose lenis instance globally for modal triggers/anchor links
    (window as any).__lenis = lenis;

    return () => {
      cancelAnimationFrame(rafId);
      lenis.destroy();
      (window as any).__lenis = null;
    };
  }, []);

  return <>{children}</>;
}
```

```tsx
// app/layout.tsx
import { SmoothScrollProvider } from "@/components/providers/SmoothScrollProvider";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <SmoothScrollProvider>
          {children}
        </SmoothScrollProvider>
      </body>
    </html>
  );
}
```

---

## 3. Essential CSS for Lenis

Add these base rules to your `app/globals.css` to prevent layout jumps or jitter:

```css
html.lenis, html.lenis body {
  height: auto;
}

.lenis.lenis-smooth {
  scroll-behavior: auto !important;
}

.lenis.lenis-smooth [data-lenis-prevent] {
  overscroll-behavior: contain;
}

.lenis.lenis-stopped {
  overflow: hidden;
}

.lenis.lenis-scrolling iframe {
  pointer-events: none;
}
```

---

## 4. Modal / Drawer Scroll Locking

When a modal, drawer, or dialog opens, lock Lenis so the background doesn't scroll behind the overlay:

```tsx
// hooks/useLenisControl.ts
"use client";

export function useLenisControl() {
  const stopScroll = () => {
    const lenis = (window as any).__lenis;
    if (lenis) lenis.stop();
  };

  const startScroll = () => {
    const lenis = (window as any).__lenis;
    if (lenis) lenis.start();
  };

  const scrollTo = (target: string | HTMLElement, offset: number = 0) => {
    const lenis = (window as any).__lenis;
    if (lenis) {
      lenis.scrollTo(target, {
        offset,
        duration: 1.4,
        easing: (t: number) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
      });
    }
  };

  return { stopScroll, startScroll, scrollTo };
}
```

---

## 5. Smooth Anchor Navigation

Jump to sections with smooth decelerated easing:

```tsx
"use client";

import { useLenisControl } from "@/hooks/useLenisControl";

export function AnchorLink({ to, children }: { to: string; children: React.ReactNode }) {
  const { scrollTo } = useLenisControl();

  return (
    <button
      onClick={() => scrollTo(to, -80)}
      className="text-sm font-medium hover:opacity-75 transition-opacity"
    >
      {children}
    </button>
  );
}
```

---

## 6. Hard Rules
1. **Never use CSS `scroll-behavior: smooth` alongside Lenis:** It creates two competing scroll controllers that cause stutter and frame drops.
2. **Use `data-lenis-prevent` on internal scroll containers:** If you have an inner modal or chat window with its own scrollbar, add `data-lenis-prevent` to that element so Lenis doesn't hijack it.
3. **Always destroy on unmount:** Avoid orphan RAF listeners on component unmount or fast-refresh cycles.
