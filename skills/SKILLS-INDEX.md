# Agent skills

The real skill directories live inside the numbered category folders. Codex and the IDE already discover nested skills such as `game-development/pc-games`, so the grouped layout remains slash-addressable.
The category folders contain the canonical files, not duplicate views.

## Groups

| Folder | Focus |
| --- | --- |
| [`00-orchestration`](00-orchestration/README.md) | Planning, app building, routing, and multi-agent workflows |
| [`01-frontend-ui`](01-frontend-ui/README.md) | React, Next.js, Tailwind, shadcn/ui, UX, accessibility, and product UI |
| [`02-animation-motion`](02-animation-motion/README.md) | Animation, transitions, interaction polish, and motion review |
| [`03-backend-data`](03-backend-data/README.md) | APIs, Node.js, databases, Prisma, Python, Rust, and server work |
| [`04-quality-shipping`](04-quality-shipping/README.md) | Clean code, testing, debugging, security, performance, and deployment |
| [`05-cloud-data`](05-cloud-data/README.md) | BigQuery, GCP data pipelines, notebooks, and data applications |
| [`06-games`](06-games/README.md) | Game development, art, audio, design, multiplayer, and platform skills |
| [`07-content-platform`](07-content-platform/README.md) | Blog-specific writing and other platform-focused skills |

## Canonical layout

The canonical files are inside the numbered folders. Gemini's corresponding skill paths now symlink back to those grouped locations.
That means updates made to a canonical skill are shared by both agents without maintaining duplicate copies.

## Recommended full-stack workflow

1. Orchestrate with `behavioral-modes` and `app-builder`.
2. Plan with `architecture` and `plan-writing`.
3. Design with `frontend-design`, `ui-ux-pro-max`, and `shadcn`.
4. Implement with `nextjs-react-expert`, `vercel-composition-patterns`, and `tailwind-patterns`.
5. Build the backend with `api-patterns`, `nodejs-best-practices`, and the Prisma skills.
6. Validate with `clean-code`, `lint-and-validate`, `testing-patterns`, and `agent-browser`.
7. Review and ship with `vulnerability-scanner`, `performance-profiling`, and `deployment-procedures`.
