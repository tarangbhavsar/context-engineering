# VS Code Copilot Customization - Project Planning

> **Development Roadmap for Custom AI Workflow Configuration**

---

## Project Overview

Create comprehensive documentation and sample configurations for VS Code Copilot customization, enabling teams to establish effective AI-assisted development workflows.

---

## Phase 1: Research & Documentation ✅

### Completed Tasks

- [x] Analyzed VS Code customization overview documentation
- [x] Studied custom instructions configuration
- [x] Reviewed prompt files documentation
- [x] Examined custom agents functionality
- [x] Investigated Agent Skills (preview feature)
- [x] Reviewed language models integration
- [x] Studied MCP servers configuration
- [x] Analyzed context engineering guide
- [x] Researched Salt Design System components

### Key Findings

1. **Six Customization Mechanisms**
   - Custom Instructions (auto-applied)
   - Prompt Files (on-demand)
   - Custom Agents (role-based)
   - Agent Skills (portable capabilities)
   - Language Models (model selection)
   - MCP Servers (external tools)

2. **Loading Order**
   - Settings → Global Instructions → Pattern Instructions → Agent Config → Prompt Overrides → Skills → MCP Tools

3. **File Locations**
   - `.github/copilot-instructions.md` - Global
   - `.github/instructions/` - Pattern-based
   - `.github/prompts/` - Reusable prompts
   - `.github/agents/` - Custom agents
   - `.github/skills/` - Agent skills
   - `mcp.json` - MCP configuration

---

## Phase 2: Core Documentation ✅

### Documents Created

| Document | Status | Description |
|----------|--------|-------------|
| `docs/Prompt.md` | ✅ Complete | Main customization guide |
| `docs/architecture.md` | ✅ Complete | System architecture |
| `docs/tech_stack.md` | ✅ Complete | Technology comparison |
| `docs/planning.md` | ✅ Complete | This document |
| `docs/walkthrough.md` | 🔄 In Progress | Developer guide |

---

## Phase 3: Sample Configurations 🔄

### Spring Boot Application

| File | Status | Purpose |
|------|--------|---------|
| `samples/springboot/copilot-instructions.md` | 📝 Pending | Global Spring Boot standards |
| `samples/springboot/java.instructions.md` | 📝 Pending | Java coding standards |
| `samples/springboot/spring-api.instructions.md` | 📝 Pending | REST API guidelines |
| `samples/springboot/create-service.prompt.md` | 📝 Pending | Service generation prompt |
| `samples/springboot/create-controller.prompt.md` | 📝 Pending | Controller generation prompt |
| `samples/springboot/planner.agent.md` | 📝 Pending | Planning agent |
| `samples/springboot/api-testing/SKILL.md` | 📝 Pending | API testing skill |

### React + Salt Design System Application

| File | Status | Purpose |
|------|--------|---------|
| `samples/react-salt/copilot-instructions.md` | 📝 Pending | Global React/Salt standards |
| `samples/react-salt/react.instructions.md` | 📝 Pending | React coding standards |
| `samples/react-salt/salt-components.instructions.md` | 📝 Pending | Salt component guidelines |
| `samples/react-salt/create-component.prompt.md` | 📝 Pending | Component generation prompt |
| `samples/react-salt/reviewer.agent.md` | 📝 Pending | Code review agent |
| `samples/react-salt/ui-testing/SKILL.md` | 📝 Pending | UI testing skill |

---

## Phase 4: Visual Documentation 📝

### Planned Images (Handwritten Sketchnote Style)

| Image | Status | Content |
|-------|--------|---------|
| `customization_types_overview.png` | 📝 Pending | Six customization types with icons |
| `loading_order_flow.png` | 📝 Pending | Context loading sequence |
| `context_engineering_workflow.png` | 📝 Pending | Plan → Implement → Review flow |

### Image Generation Prompt Template

```
Create a hand-drawn sketchnote visual summary of [TOPIC]. 
Use a pristine white paper background (no lines). 
The art style should be 'graphic recording' or 'visual thinking' 
using black ink fine-liners for clear outlines and text. 
Use colored markers (specifically teal, orange, and muted red) 
for simple shading and accents. 
Center the main title in a 3D-style rectangular box. 
Surround the title with radially distributed simple doodles, 
business icons, stick figures, and graphs that explain the concepts. 
Use arrows to connect ideas. 
The text should be distinct, handwritten, all-caps printing, 
legible and organized like a professional brainstorming session. 
16:9
```

---

## Phase 5: Finalization

- [ ] Review all documentation for consistency
- [ ] Validate sample configurations
- [ ] Generate handwritten-style diagrams
- [ ] Create walkthrough guide
- [ ] Final user review

---

## Timeline

| Phase | Tasks | Status |
|-------|-------|--------|
| Research | Documentation analysis | ✅ Complete |
| Core Docs | Main guides creation | ✅ Complete |
| Samples | Spring Boot & React configs | 🔄 In Progress |
| Visuals | Handwritten diagrams | 📝 Pending |
| Review | Final validation | 📝 Pending |

---

## Dependencies

- VS Code 1.106+ (for custom agents)
- VS Code Insiders (for Agent Skills preview)
- Salt Design System documentation
- Spring Boot best practices

---

## Success Criteria

1. Complete documentation covering all six customization types
2. Working sample configurations for both application types
3. Visual diagrams explaining key concepts
4. Clear walkthrough for new developers
5. Production-ready examples

---

*Last Updated: January 2026*
