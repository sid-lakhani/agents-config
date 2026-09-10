# AI Agent Configuration Workspace

This repository serves as the central configuration and knowledge base for AI agents operating within the development environment. It contains specialized skills, orchestrated workflows, rule enforcement, and dependency locks to ensure consistent and high-quality AI assistance.

## Directory Structure

*   **`skills/`**
    This directory contains the domain-specific knowledge base. Each subdirectory represents a discrete skill (e.g., game development patterns, backend architecture, UI/UX guidelines). Skills instruct the agent on *how* to execute tasks using established best practices and preferred frameworks.

*   **`workflows/`**
    This directory contains markdown-based Standard Operating Procedures (SOPs). Workflows define the step-by-step logic that the agent must execute when specific slash commands (e.g., `/test`, `/deploy`) are invoked.

*   **`AGENTS.md`**
    The core rulebook for agent behavior. This file establishes baseline requirements for code quality, communication style, formatting guidelines, and security protocols across all projects.

*   **`.skill-lock.json`**
    A lockfile that tracks versions and local paths of third-party skills installed from remote sources (such as GitHub repositories) to ensure deterministic behavior.

## Installation and Setup Guide

To restore this AI configuration on a new workstation, follow these steps sequentially:

### 1. Clone the Repository
Clone this repository directly into the home directory under the `.agents` namespace. This location is standard for global agent configuration.

```bash
git clone git@github.com:sid-lakhani/agents-config.git ~/.agents
```

### 2. Configure Global Workflows (Slash Commands)
For the IDE to recognize the custom slash commands (such as `/test` or `/deploy`), the workflow files must be accessible in the global configurations directory. 

To maintain `.agents` as the single source of truth, create symbolic links from the repository into the IDE's global workflow directory:

```bash
# Ensure the target directory exists
mkdir -p ~/.gemini/config/global_workflows

# Create symbolic links for all markdown workflows
ln -sf ~/.agents/workflows/*.md ~/.gemini/config/global_workflows/
```

### 3. Verify Installation
To confirm the setup was successful:
1. Open the Antigravity IDE.
2. Navigate to the chat interface and type `/`.
3. The autocomplete menu should now populate with the custom workflows originating from the `~/.agents/workflows/` directory.

### 4. Updating and Managing Skills
When adding new local skills to the `skills/` directory, ensure they are placed exactly one or two folder levels deep (e.g., `skills/category-name/skill-name/SKILL.md`). Deeply nested folders may not be automatically discovered by the agent's context engine without manual intervention.
