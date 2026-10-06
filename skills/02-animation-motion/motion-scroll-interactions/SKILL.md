---
name: motion-scroll-interactions
description: High-fidelity scroll interactions and spring animations using Motion for React (formerly Framer Motion). Use when implementing scroll progress meters, sticky parallax transforms, spring-based magnetic cursors/buttons, and shared layoutId transitions.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
metadata:
  version: "12.0.0"
  stack: "motion, framer-motion, react, nextjs"
---

# Motion (Framer Motion) Scroll & Interaction Master Skill

Build fluid, spring-driven interactive components and scroll-linked transforms using `motion` (Motion for React / Framer Motion v11+).

---

## 1. Installation

```bash
npm install motion
# Or legacy alias if project uses framer-motion:
npm install framer-motion
```

---

## 2. Scroll-Linked Parallax & Scale Transform

Use `useScroll`, `useTransform`, and `useSpring` to create butter-smooth scroll-linked animations:

```tsx
// components/sections/ScrollHero.tsx
"use client";

import { useRef } from "react";
import { motion, useScroll, useTransform, useSpring } from "motion/react";

export function ScrollHero() {
  const containerRef = useRef<HTMLDivElement>(null);

  const { scrollYProgress } = useScroll({
    target: containerRef,
    offset: ["start start", "end start"],
  });

  // Smooth raw scroll with spring physics
  const smoothProgress = useSpring(scrollYProgress, {
    stiffness: 100,
    damping: 30,
    restDelta: 0.001,
  });

  const scale = useTransform(smoothProgress, [0, 1], [1, 0.85]);
  const opacity = useTransform(smoothProgress, [0, 0.8], [1, 0]);
  const y = useTransform(smoothProgress, [0, 1], [0, 150]);

  return (
    <div ref={containerRef} className="relative h-[150vh] w-full">
      <div className="sticky top-0 h-screen w-full flex items-center justify-center overflow-hidden">
        <motion.div
          style={{ scale, opacity, y }}
          className="text-center px-4"
        >
          <h1 className="text-6xl md:text-8xl font-black tracking-tight mb-6 bg-gradient-to-r from-neutral-100 via-neutral-300 to-neutral-500 bg-clip-text text-transparent">
            Fluid Motion
          </h1>
          <p className="text-lg md:text-xl text-neutral-400 max-w-lg mx-auto">
            Interactive elevation built with calibrated spring physics.
          </p>
        </motion.div>
      </div>
    </div>
  );
}
```

---

## 3. Top Reading / Scroll Progress Bar

```tsx
// components/ui/ScrollProgress.tsx
"use client";

import { motion, useScroll, useSpring } from "motion/react";

export function ScrollProgress() {
  const { scrollYProgress } = useScroll();
  const scaleX = useSpring(scrollYProgress, {
    stiffness: 100,
    damping: 30,
    restDelta: 0.001,
  });

  return (
    <motion.div
      style={{ scaleX }}
      className="fixed top-0 left-0 right-0 h-1 bg-gradient-to-r from-blue-500 to-indigo-500 origin-left z-50 pointer-events-none"
    />
  );
}
```

---

## 4. Magnetic Interactive Button (Spring Physics)

```tsx
// components/ui/MagneticButton.tsx
"use client";

import { useRef, useState } from "react";
import { motion, useSpring } from "motion/react";

export function MagneticButton({ children }: { children: React.ReactNode }) {
  const ref = useRef<HTMLButtonElement>(null);
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const springConfig = { stiffness: 150, damping: 15, mass: 0.1 };
  const x = useSpring(position.x, springConfig);
  const y = useSpring(position.y, springConfig);

  const handleMouseMove = (e: React.MouseEvent) => {
    if (!ref.current) return;
    const { clientX, clientY } = e;
    const { left, top, width, height } = ref.current.getBoundingClientRect();
    const middleX = clientX - (left + width / 2);
    const middleY = clientY - (top + height / 2);
    setPosition({ x: middleX * 0.35, y: middleY * 0.35 });
  };

  const handleMouseLeave = () => {
    setPosition({ x: 0, y: 0 });
  };

  return (
    <motion.button
      ref={ref}
      onMouseMove={handleMouseMove}
      onMouseLeave={handleMouseLeave}
      style={{ x, y }}
      whileTap={{ scale: 0.95 }}
      className="relative px-8 py-4 rounded-full bg-white text-black font-semibold shadow-lg hover:shadow-indigo-500/20 transition-shadow"
    >
      {children}
    </motion.button>
  );
}
```

---

## 5. Shared Layout Expansion (`layoutId`)

Seamless morphing between a summary card and an expanded dialog:

```tsx
// components/ui/ExpandableCard.tsx
"use client";

import { useState } from "react";
import { motion, AnimatePresence } from "motion/react";

export function ExpandableCard() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <motion.div
        layoutId="card-container"
        onClick={() => setIsOpen(true)}
        className="cursor-pointer p-6 rounded-2xl bg-neutral-900 border border-neutral-800 hover:border-neutral-700 w-80"
      >
        <motion.h3 layoutId="card-title" className="text-xl font-bold text-white">
          Architectural Detail
        </motion.h3>
        <p className="text-neutral-400 text-sm mt-2">Click to inspect deep specs.</p>
      </motion.div>

      <AnimatePresence>
        {isOpen && (
          <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
            <motion.div
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              onClick={() => setIsOpen(false)}
              className="absolute inset-0 bg-black/60 backdrop-blur-sm"
            />
            <motion.div
              layoutId="card-container"
              className="relative w-full max-w-lg p-8 rounded-3xl bg-neutral-900 border border-neutral-700 z-10"
            >
              <motion.h3 layoutId="card-title" className="text-3xl font-bold text-white">
                Architectural Detail
              </motion.h3>
              <motion.p
                initial={{ opacity: 0, y: 10 }}
                animate={{ opacity: 1, y: 0 }}
                transition={{ delay: 0.1 }}
                className="text-neutral-300 mt-4 leading-relaxed"
              >
                Full specifications, interactive controls, and deep layout configuration.
              </motion.p>
              <button
                onClick={() => setIsOpen(false)}
                className="mt-6 px-4 py-2 bg-neutral-800 rounded-lg text-sm text-white hover:bg-neutral-700"
              >
                Close
              </button>
            </motion.div>
          </div>
        )}
      </AnimatePresence>
    </>
  );
}
```

---

## 6. Anti-Patterns & Best Practices
1. **Never animate layout properties directly:** Avoid animating `width`, `height`, `top`, `left`. Always animate `x`, `y`, `scale`, and `opacity` which render on the GPU compositor thread without triggering layout reflows.
2. **Respect Reduced Motion:**
   ```tsx
   import { useReducedMotion } from "motion/react";
   const shouldReduceMotion = useReducedMotion();
   ```
   If active, replace springs and transforms with instant cuts or simple opacity fades.
