---
name: gsap-scrolltrigger
description: Cinematic scroll-driven animations with GSAP 3, ScrollTrigger, and @gsap/react. Use when building pinned storytelling sections, scrubbed timelines, horizontal scroll galleries, parallax reveals, Lenis + GSAP synchronization, and Awwwards-tier choreographies.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
metadata:
  version: "3.12.0"
  stack: "gsap, scrolltrigger, @gsap/react, lenis"
---

# GSAP ScrollTrigger Master Skill

Build high-end, smooth, and cinematic scroll-driven web animations using GSAP 3, ScrollTrigger, and `@gsap/react`.

---

## 1. Installation

```bash
npm install gsap @gsap/react
```

---

## 2. The Lenis + GSAP ScrollTrigger Synchronization Bridge

To prevent jitter between Lenis smooth scrolling and GSAP ScrollTrigger, synchronize Lenis's RAF directly with GSAP's ticker:

```tsx
// components/providers/SmoothScrollAndGSAP.tsx
"use client";

import { useEffect, useRef } from "react";
import Lenis from "lenis";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

export function SmoothScrollAndGSAP({ children }: { children: React.ReactNode }) {
  const lenisRef = useRef<Lenis | null>(null);

  useEffect(() => {
    gsap.registerPlugin(ScrollTrigger);

    const lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
      smoothWheel: true,
    });
    lenisRef.current = lenis;

    // 1. Tell ScrollTrigger to update every time Lenis scrolls
    lenis.on("scroll", ScrollTrigger.update);

    // 2. Drive Lenis through GSAP's high-precision ticker
    const tickerCallback = (time: number) => {
      lenis.raf(time * 1000);
    };
    gsap.ticker.add(tickerCallback);

    // 3. Disable lag smoothing so animations stay locked to scroll
    gsap.ticker.lagSmoothing(0);

    return () => {
      gsap.ticker.remove(tickerCallback);
      lenis.destroy();
      ScrollTrigger.getAll().forEach((t) => t.kill());
    };
  }, []);

  return <>{children}</>;
}
```

---

## 3. Pinned Scrub Storytelling (Apple-Style Section)

Pin a container while scrubbing through text and visual steps:

```tsx
// components/sections/PinnedStory.tsx
"use client";

import { useRef } from "react";
import { useGSAP } from "@gsap/react";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

export function PinnedStory() {
  const containerRef = useRef<HTMLDivElement>(null);
  const cardRef = useRef<HTMLDivElement>(null);
  const titleRef = useRef<HTMLHeadingElement>(null);

  useGSAP(
    () => {
      const tl = gsap.timeline({
        scrollTrigger: {
          trigger: containerRef.current,
          start: "top top",
          end: "+=200%",
          pin: true,
          scrub: 1, // Smooth scrub with 1s catchup
          anticipatePin: 1,
        },
      });

      tl.fromTo(
        cardRef.current,
        { scale: 0.8, opacity: 0.4, y: 100 },
        { scale: 1, opacity: 1, y: 0, duration: 1, ease: "power2.out" }
      )
        .to(titleRef.current, {
          y: -50,
          opacity: 0,
          duration: 0.6,
        }, "+=0.2")
        .to(cardRef.current, {
          scale: 1.1,
          rotate: 5,
          duration: 1,
          ease: "power1.inOut",
        });
    },
    { scope: containerRef }
  );

  return (
    <section ref={containerRef} className="h-screen w-full flex items-center justify-center relative overflow-hidden bg-neutral-950 text-white">
      <div className="text-center z-10">
        <h2 ref={titleRef} className="text-5xl font-bold tracking-tight mb-8">
          Precision Engineering
        </h2>
        <div ref={cardRef} className="w-[450px] h-[300px] rounded-2xl bg-gradient-to-tr from-indigo-500 to-purple-500 shadow-2xl mx-auto flex items-center justify-center">
          <span className="text-2xl font-semibold">Crafted in Real-Time</span>
        </div>
      </div>
    </section>
  );
}
```

---

## 4. Horizontal Scroll Section

Create a seamless horizontal gallery scrubbed on vertical scroll:

```tsx
// components/sections/HorizontalGallery.tsx
"use client";

import { useRef } from "react";
import { useGSAP } from "@gsap/react";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

export function HorizontalGallery({ items }: { items: string[] }) {
  const sectionRef = useRef<HTMLDivElement>(null);
  const trackRef = useRef<HTMLDivElement>(null);

  useGSAP(
    () => {
      const track = trackRef.current;
      if (!track) return;

      const totalScroll = track.scrollWidth - window.innerWidth;

      gsap.to(track, {
        x: -totalScroll,
        ease: "none",
        scrollTrigger: {
          trigger: sectionRef.current,
          start: "top top",
          end: () => `+=${totalScroll}`,
          pin: true,
          scrub: 1,
          invalidateOnRefresh: true,
        },
      });
    },
    { scope: sectionRef }
  );

  return (
    <section ref={sectionRef} className="h-screen w-full overflow-hidden bg-neutral-900">
      <div ref={trackRef} className="flex h-full items-center gap-8 px-16 w-max">
        {items.map((item, idx) => (
          <div
            key={idx}
            className="w-[80vw] max-w-[500px] h-[65vh] rounded-3xl bg-neutral-800 flex-shrink-0 flex items-center justify-center text-white text-3xl font-bold border border-white/10"
          >
            {item}
          </div>
        ))}
      </div>
    </section>
  );
}
```

---

## 5. Parallax Image Reveals (Image Scrub inside Container)

```tsx
// components/ui/ParallaxImage.tsx
"use client";

import { useRef } from "react";
import { useGSAP } from "@gsap/react";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

export function ParallaxImage({ src, alt }: { src: string; alt: string }) {
  const containerRef = useRef<HTMLDivElement>(null);
  const imageRef = useRef<HTMLImageElement>(null);

  useGSAP(
    () => {
      gsap.fromTo(
        imageRef.current,
        { yPercent: -15, scale: 1.15 },
        {
          yPercent: 15,
          scale: 1,
          ease: "none",
          scrollTrigger: {
            trigger: containerRef.current,
            start: "top bottom",
            end: "bottom top",
            scrub: true,
          },
        }
      );
    },
    { scope: containerRef }
  );

  return (
    <div ref={containerRef} className="relative overflow-hidden w-full h-[450px] rounded-2xl">
      <img
        ref={imageRef}
        src={src}
        alt={alt}
        className="w-full h-full object-cover will-change-transform"
      />
    </div>
  );
}
```

---

## 6. Critical Golden Rules
1. **Always use `@gsap/react` `useGSAP` hook:** Never use bare `useEffect` without manually reverting `gsap.context()`. The `useGSAP` hook automatically isolates selectors and purges memory on unmount.
2. **Always scope selectors:** Provide `{ scope: containerRef }` so GSAP selectors don't leak or conflict with other elements on the page.
3. **Responsive Breakpoints:** Use `gsap.matchMedia()` when pinning sections so desktop horizontal scrolls revert back to standard vertical flows on mobile viewports (`(min-width: 768px)`).
