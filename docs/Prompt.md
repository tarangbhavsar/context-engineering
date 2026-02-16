# VS Code Copilot Customization Guide

> **Complete Reference for AI-Assisted Development Customization**

This comprehensive guide explains how to customize GitHub Copilot in VS Code to match your coding practices, project requirements, and development workflows.

---

## Table of Contents

1. [Overview](#overview)
2. [Customization Types](#customization-types)
3. [Loading Order & Priority](#loading-order--priority)
4. [File Structure Reference](#file-structure-reference)
5. [Best Practices](#best-practices)
6. [Quick Reference Tables](#quick-reference-tables)

---

## Overview

VS Code Copilot provides six main customization mechanisms that work independently or together:

| Customization Type | Purpose | Auto-Applied? | File Extension |
|-------------------|---------|---------------|----------------|
| **Custom Instructions** | Define coding guidelines & rules | Yes (configurable) | `.instructions.md`, `copilot-instructions.md`, `AGENTS.md` |
| **Prompt Files** | Reusable task prompts | No (on-demand) | `.prompt.md` |
| **Custom Agents** | Specialized AI personas | No (user-selected) | `.agent.md` |
| **Agent Skills** | Portable specialized capabilities | Auto (when relevant) | `SKILL.md` |
| **Language Models** | AI model selection | N/A | Settings/UI |
| **MCP Servers** | External tool integration | N/A | `mcp.json` |

---

## Customization Types

### 1. Custom Instructions

Custom instructions define persistent guidelines that automatically influence AI responses. They are your project's "coding constitution."

#### Types of Instruction Files

```
📁 Workspace Root
├── 📄 .github/copilot-instructions.md    ← Global instructions (auto-applies to all)
├── 📁 .github/instructions/
│   ├── 📄 java.instructions.md           ← Language-specific (glob pattern)
│   ├── 📄 react.instructions.md          ← Framework-specific
│   └── 📄 testing.instructions.md        ← Task-specific
└── 📄 AGENTS.md                          ← Multi-agent compatible instructions
```

#### Instruction File Format

```markdown
---
applyTo: "**/*.java"           # Glob pattern for auto-apply
description: "Java coding standards"
name: "Java Guidelines"
---

# Java Coding Standards

## Naming Conventions
- Use PascalCase for classes
- Use camelCase for methods and variables
- Use SCREAMING_SNAKE_CASE for constants

## Best Practices
- Always use try-with-resources for AutoCloseable
- Prefer composition over inheritance
- Document public APIs with Javadoc
```

#### Loading Behavior

| File Type | Scope | Auto-Apply | Location |
|-----------|-------|------------|----------|
| `.github/copilot-instructions.md` | All requests | ✅ Yes | Workspace root |
| `*.instructions.md` | Matches `applyTo` pattern | ✅ Yes | `.github/instructions/` or profile |
| `AGENTS.md` | All requests | ✅ Yes | Workspace root or subfolders |

---

### 2. Prompt Files

Prompt files are standalone, reusable prompts for common development tasks. They are triggered on-demand via `/prompt-name` in chat.

#### Prompt File Format

```markdown
---
description: "Generate a new REST controller"
name: "Create Controller"
agent: "agent"                   # agent, ask, or edit
model: "Claude Sonnet 4"         # Optional model override
tools: ['codebase', 'terminal']  # Available tools
---

# Generate REST Controller

Create a new Spring Boot REST controller with the following requirements:

## Input
- Entity name: ${input:entityName:Enter entity name}
- Package: ${input:package:com.example.api}

## Requirements
1. Use `@RestController` and `@RequestMapping`
2. Include CRUD endpoints (GET, POST, PUT, DELETE)
3. Apply proper response entity handling
4. Include Swagger/OpenAPI annotations

Reference: [API Guidelines](../docs/api-guidelines.md)
```

#### Available Variables

| Variable | Description |
|----------|-------------|
| `${workspaceFolder}` | Full path to workspace |
| `${file}` | Current file path |
| `${selection}` | Selected text in editor |
| `${input:varName}` | Prompt user for input |
| `${input:varName:placeholder}` | With default value |

---

### 3. Custom Agents

Custom agents create specialized AI personas with specific tools, instructions, and behaviors.

#### Agent File Format

```markdown
---
description: "Security-focused code reviewer"
name: "Security Reviewer"
model: "Claude Sonnet 4"
tools: ['codebase', 'search', 'fetch']
handoffs:
  - label: "Fix Issues"
    agent: "agent"
    prompt: "Fix the security issues identified above."
    send: false
---

# Security Reviewer Agent

You are a senior security engineer reviewing code for vulnerabilities.

## Focus Areas
1. **Injection Attacks**: SQL, NoSQL, Command, LDAP
2. **Authentication**: Password handling, session management
3. **Authorization**: Access control, privilege escalation
4. **Data Protection**: Encryption, PII handling
5. **Dependencies**: Known CVEs, outdated packages

## Output Format
Return findings as a prioritized list:
- 🔴 **CRITICAL**: Immediate action required
- 🟠 **HIGH**: Fix before release
- 🟡 **MEDIUM**: Address in next sprint
- 🟢 **LOW**: Consider for improvement
```

#### Storage Locations

| Location | Scope |
|----------|-------|
| `.github/agents/` | Workspace (team-shared) |
| User Profile | Personal (cross-workspace) |
| Organization | Enterprise-wide (GitHub configured) |

---

### 4. Agent Skills (Preview)

Agent Skills are portable capabilities that work across VS Code, GitHub CLI, and GitHub Copilot coding agent.

#### Skill Structure

```
📁 .github/skills/
└── 📁 webapp-testing/
    ├── 📄 SKILL.md              ← Main skill definition
    ├── 📄 test-template.js      ← Supporting resources
    └── 📁 examples/
        └── 📄 login-test.js
```

#### SKILL.md Format

```markdown
---
name: webapp-testing
description: Guide for testing web applications using Playwright
---

# Web Application Testing with Playwright

## When to Use
- Creating new browser-based tests
- Debugging failing UI tests
- Setting up test infrastructure

## Process
1. Review the [test template](./test-template.js)
2. Identify the user flow to test
3. Create test in `tests/` directory
4. Use role-based selectors for elements

## Running Tests
```bash
npx playwright test
npx playwright test --debug  # For debugging
```

## Best Practices
- Use data-testid for dynamic content
- Keep tests independent and atomic
- Take screenshots on failure
```

#### How Skills Load (Progressive Disclosure)

1. **Discovery**: Copilot reads `name` and `description` from frontmatter
2. **Instructions**: Full `SKILL.md` body loaded when relevant
3. **Resources**: Supporting files accessed only when referenced

---

### 5. Language Models

Configure which AI models power your Copilot experience.

#### Model Selection

```json
// In custom agent or prompt file frontmatter
{
  "model": "Claude Sonnet 4"    // Or "GPT-4o", "Gemini 2.5 Pro", etc.
}
```

#### Bring Your Own Key (BYOK)

Access additional models by providing your own API key:
- Built-in providers (OpenAI, Anthropic, etc.)
- AI Toolkit extensions
- Local models via Ollama

---

### 6. MCP Servers

Model Context Protocol (MCP) extends Copilot with external tools and services.

#### Configuration (`mcp.json` or `.vscode/mcp.json`)

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "github-token",
      "description": "GitHub Personal Access Token",
      "password": true
    }
  ],
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${input:github-token}"
      }
    },
    "database": {
      "type": "http",
      "url": "https://mcp.example.com/db",
      "headers": {
        "Authorization": "Bearer ${input:api-key}"
      }
    }
  }
}
```

---

## Loading Order & Priority

Understanding how VS Code loads and applies customizations is crucial for effective configuration.

### Context Loading Order

```
┌─────────────────────────────────────────────────────────────────┐
│                    USER CHAT REQUEST                            │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  1. SETTINGS-BASED INSTRUCTIONS (Deprecated)                   │
│     - github.copilot.chat.codeGeneration.instructions          │
│     - github.copilot.chat.testGeneration.instructions          │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  2. GLOBAL INSTRUCTIONS (Auto-applied)                         │
│     - .github/copilot-instructions.md                          │
│     - AGENTS.md (if enabled)                                   │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  3. PATTERN-MATCHED INSTRUCTIONS                               │
│     - *.instructions.md files where applyTo matches            │
│     - Workspace + User Profile instructions                    │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  4. AGENT-SPECIFIC CONTEXT                                     │
│     - Selected custom agent (.agent.md)                        │
│     - Agent's tools configuration                              │
│     - Agent's model preference                                 │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  5. PROMPT FILE OVERRIDES                                      │
│     - Prompt file tools (highest priority)                     │
│     - Prompt file agent reference                              │
│     - Prompt file model override                               │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  6. SKILLS (Progressive Loading)                               │
│     - Discovered from .github/skills/                          │
│     - Loaded based on relevance to request                     │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  7. MCP SERVER TOOLS                                           │
│     - Tools from configured MCP servers                        │
│     - Added to available tool set                              │
└───────────────────────────┴─────────────────────────────────────┘
```

### Tool Priority Order

When tools are specified in multiple places, priority is:

1. **Prompt file `tools`** (highest)
2. **Referenced agent `tools`**  
3. **Selected agent default tools** (lowest)

### Key Points

> [!IMPORTANT]
> - Multiple instruction files are **combined**, not replaced
> - No guaranteed order for multiple instructions files
> - Instructions apply to **chat only**, not inline suggestions
> - Skills load **progressively** based on relevance

---

## File Structure Reference

### Complete Workspace Layout

```
📁 your-project/
├── 📄 AGENTS.md                           ← Global agent instructions
├── 📄 mcp.json                            ← Workspace MCP configuration
│
├── 📁 .github/
│   ├── 📄 copilot-instructions.md         ← Global Copilot instructions
│   │
│   ├── 📁 instructions/                   ← Pattern-based instructions
│   │   ├── 📄 java.instructions.md
│   │   ├── 📄 react.instructions.md
│   │   ├── 📄 testing.instructions.md
│   │   └── 📄 docs.instructions.md
│   │
│   ├── 📁 prompts/                        ← Reusable prompts
│   │   ├── 📄 create-api.prompt.md
│   │   ├── 📄 code-review.prompt.md
│   │   └── 📄 generate-tests.prompt.md
│   │
│   ├── 📁 agents/                         ← Custom agents
│   │   ├── 📄 planner.agent.md
│   │   ├── 📄 reviewer.agent.md
│   │   └── 📄 implementer.agent.md
│   │
│   └── 📁 skills/                         ← Agent skills
│       ├── 📁 api-testing/
│       │   ├── 📄 SKILL.md
│       │   └── 📄 test-template.js
│       └── 📁 deployment/
│           ├── 📄 SKILL.md
│           └── 📁 scripts/
│
└── 📁 .vscode/
    └── 📄 mcp.json                        ← Alternative MCP location
```

---

## Best Practices

### 1. Instruction Guidelines

| ✅ Do | ❌ Don't |
|-------|---------|
| Keep instructions short & focused | Write novels of instructions |
| Use multiple files for different concerns | Put everything in one file |
| Reference other files via Markdown links | Duplicate content across files |
| Store in workspace for team sharing | Keep only in user profile |
| Update as codebase evolves | Let instructions become stale |

### 2. Prompt File Tips

- **Clear objective**: State exactly what the prompt accomplishes
- **Expected output**: Describe the format and structure expected
- **Use variables**: Leverage `${input:}` for flexibility
- **Reference instructions**: Link to coding guidelines, don't duplicate

### 3. Custom Agent Design

- **Single responsibility**: One agent = one specialized role
- **Tool curation**: Include only tools needed for the role
- **Handoffs**: Connect agents for workflow continuity
- **Model selection**: Choose models appropriate for the task

### 4. Context Engineering Workflow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   CURATE    │────▶│    PLAN     │────▶│  IMPLEMENT  │
│   Context   │     │   Feature   │     │    Code     │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │                   │
       ▼                   ▼                   ▼
 copilot-         planner.agent.md     agent mode +
 instructions.md  + planning prompt    TDD approach
```

### 5. Anti-Patterns to Avoid

> [!CAUTION]
> - **Context dumping**: Overwhelming AI with unfocused information
> - **Inconsistent guidance**: Conflicting rules across files
> - **Neglecting validation**: Assuming AI understands correctly
> - **One-size-fits-all**: Using same setup for all tasks

---

## Quick Reference Tables

### Settings Reference

| Setting | Purpose |
|---------|---------|
| `github.copilot.chat.codeGeneration.useInstructionFiles` | Enable instruction files |
| `chat.instructionsFilesLocations` | Additional instruction folders |
| `chat.promptFilesLocations` | Additional prompt folders |
| `chat.useAgentsMdFile` | Enable AGENTS.md support |
| `chat.useNestedAgentsMdFiles` | Enable subfolder AGENTS.md |
| `chat.useAgentSkills` | Enable Agent Skills (preview) |

### File Extension Reference

| Extension | Purpose | Location |
|-----------|---------|----------|
| `.instructions.md` | Pattern-based instructions | `.github/instructions/` |
| `.prompt.md` | Reusable prompts | `.github/prompts/` |
| `.agent.md` | Custom agents | `.github/agents/` |
| `SKILL.md` | Skill definition | `.github/skills/<name>/` |
| `mcp.json` | MCP server config | Root or `.vscode/` |

### Frontmatter Fields

| Field | Files | Description |
|-------|-------|-------------|
| `applyTo` | instructions | Glob pattern for auto-apply |
| `description` | all | Human-readable description |
| `name` | all | Display name |
| `agent` | prompts | Agent mode (agent/ask/edit) |
| `model` | prompts, agents | AI model to use |
| `tools` | prompts, agents | Available tools list |
| `handoffs` | agents | Workflow transitions |

---

## Related Resources

- [VS Code Copilot Documentation](https://code.visualstudio.com/docs/copilot)
- [Context Engineering Guide](https://code.visualstudio.com/docs/copilot/guides/context-engineering-guide)
- [Awesome Copilot Repository](https://github.com/github/awesome-copilot)
- [Agent Skills Standard](https://agentskills.io)
- [MCP Specification](https://modelcontextprotocol.io)
- [OpenSpec vs Speckit Comparison](openspec_vs_speckit.md) - SDD tools analysis

---

*Last Updated: February 2026*
