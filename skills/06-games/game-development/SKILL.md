---
name: game-development
description: Game development orchestrator. Routes to platform-specific skills based on project needs.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
---

# Game Development

> **Orchestrator skill** that provides core principles and routes to specialized sub-skills.

---

## When to Use This Skill

You are working on a game development project. This skill teaches the PRINCIPLES of game development and directs you to the right sub-skill based on context.

---

## Sub-Skill Routing

### Platform Selection

| If the game targets... | Use Sub-Skill |
|------------------------|---------------|
| Web browsers (HTML5, WebGL) | `06-games/web-games` |
| Mobile (iOS, Android) | `06-games/mobile-games` |
| PC (Steam, Desktop) | `06-games/pc-games` |
| VR/AR headsets | `06-games/vr-ar` |

### Dimension Selection

| If the game is... | Use Sub-Skill |
|-------------------|---------------|
| 2D (sprites, tilemaps) | `06-games/2d-games` |
| 3D (meshes, shaders) | `06-games/3d-games` |

### Specialty Areas

| If you need... | Use Sub-Skill |
|----------------|---------------|
| GDD, balancing, player psychology | `06-games/game-design` |
| Multiplayer, networking | `06-games/multiplayer` |
| Visual style, asset pipeline, animation | `06-games/game-art` |
| Sound design, music, adaptive audio | `06-games/game-audio` |

---

## Core Principles (All Platforms)

### 1. The Game Loop

Every game, regardless of platform, follows this pattern:

```
INPUT  → Read player actions
UPDATE → Process game logic (fixed timestep)
RENDER → Draw the frame (interpolated)
```

**Fixed Timestep Rule:**
- Physics/logic: Fixed rate (e.g., 50Hz)
- Rendering: As fast as possible
- Interpolate between states for smooth visuals

---

### 2. Pattern Selection Matrix

| Pattern | Use When | Example |
|---------|----------|---------|
| **State Machine** | 3-5 discrete states | Player: Idle→Walk→Jump |
| **Object Pooling** | Frequent spawn/destroy | Bullets, particles |
| **Observer/Events** | Cross-system communication | Health→UI updates |
| **ECS** | Thousands of similar entities | RTS units, particles |
| **Command** | Undo, replay, networking | Input recording |
| **Behavior Tree** | Complex AI decisions | Enemy AI |

**Decision Rule:** Start with State Machine. Add ECS only when performance demands.

---

### 3. Input Abstraction

Abstract input into ACTIONS, not raw keys:

```
"jump"  → Space, Gamepad A, Touch tap
"move"  → WASD, Left stick, Virtual joystick
```

**Why:** Enables multi-platform, rebindable controls.

---

### 4. Performance Budget (60 FPS = 16.67ms)

| System | Budget |
|--------|--------|
| Input | 1ms |
| Physics | 3ms |
| AI | 2ms |
| Game Logic | 4ms |
| Rendering | 5ms |
| Buffer | 1.67ms |

**Optimization Priority:**
1. Algorithm (O(n²) → O(n log n))
2. Batching (reduce draw calls)
3. Pooling (avoid GC spikes)
4. LOD (detail by distance)
5. Culling (skip invisible)

---

### 5. AI Selection by Complexity

| AI Type | Complexity | Use When |
|---------|------------|----------|
| **FSM** | Simple | 3-5 states, predictable behavior |
| **Behavior Tree** | Medium | Modular, designer-friendly |
| **GOAP** | High | Emergent, planning-based |
| **Utility AI** | High | Scoring-based decisions |

---

### 6. Collision Strategy

| Type | Best For |
|------|----------|
| **AABB** | Rectangles, fast checks |
| **Circle** | Round objects, cheap |
| **Spatial Hash** | Many similar-sized objects |
| **Quadtree** | Large worlds, varying sizes |

---

## Anti-Patterns (Universal)

| Don't | Do |
|-------|-----|
| Update everything every frame | Use events, dirty flags |
| Create objects in hot loops | Object pooling |
| Cache nothing | Cache references |
| Optimize without profiling | Profile first |
| Mix input with logic | Abstract input layer |

---

## Routing Examples

### Example 1: "I want to make a browser-based 2D platformer"
→ Start with `06-games/web-games` for framework selection
→ Then `06-games/2d-games` for sprite/tilemap patterns
→ Reference `06-games/game-design` for level design

### Example 2: "Mobile puzzle game for iOS and Android"
→ Start with `06-games/mobile-games` for touch input and stores
→ Use `06-games/game-design` for puzzle balancing

### Example 3: "Multiplayer VR shooter"
→ `06-games/vr-ar` for comfort and immersion
→ `06-games/3d-games` for rendering
→ `06-games/multiplayer` for networking

---

> **Remember:** Great games come from iteration, not perfection. Prototype fast, then polish.
