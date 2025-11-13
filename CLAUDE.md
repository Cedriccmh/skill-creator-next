# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository is **skill-creator-next**, an enhanced skill creation framework that combines:

1. **Anthropic's Official Skills** (`./skill-creator/`) - The base skill-creator from anthropics/skills
2. **Agent Skill Creator** (`./agent-skill-creator/`) - A community-enhanced version with AgentDB integration, multi-agent architecture support, and intelligent learning capabilities
3. **Example Skills** - Various reference skills including document processing (docx, pdf, pptx, xlsx), MCP builder, and others

**Goal**: Create an enhanced `skill-creator-next` that learns from both implementations and the broader skill ecosystem in this repository.

## Repository Structure

```
skill-creator-next/
├── skill-creator/              # Anthropic official skill-creator
│   ├── SKILL.md               # Base skill creation guidance
│   ├── scripts/               # init_skill.py, package_skill.py, quick_validate.py
│   └── template-skill/        # Basic template
│
├── agent-skill-creator/        # Enhanced community version
│   ├── SKILL.md               # Advanced agent creation with AgentDB
│   ├── README.md              # Comprehensive feature documentation
│   ├── docs/                  # Architecture, decision logic, naming conventions
│   ├── references/            # Phase guides, activation patterns, templates
│   ├── integrations/          # AgentDB bridge, fallback systems
│   ├── scripts/               # Export utilities
│   └── article-to-prototype-cskill/  # Example independent skill
│
├── mcp-builder/               # MCP server development skill
├── document-skills/           # docx, pdf, pptx, xlsx skills
├── docs/                      # General skill authoring guides
└── other example skills/      # algorithmic-art, canvas-design, etc.
```

## Common Development Tasks

### Creating Skills

**Initialize a new skill:**
```bash
python skill-creator/scripts/init_skill.py <skill-name> --path <output-directory>
```

This creates:
- `SKILL.md` with proper frontmatter and TODO placeholders
- Example `scripts/`, `references/`, and `assets/` directories

**Validate a skill:**
```bash
python skill-creator/scripts/quick_validate.py <path/to/skill-folder>
```

Checks YAML frontmatter, naming conventions, and skill structure.

**Package a skill:**
```bash
python skill-creator/scripts/package_skill.py <path/to/skill-folder> [output-dir]
```

This automatically validates first, then creates a distributable .zip if validation passes.

### Working with Agent Skill Creator

The `agent-skill-creator` is a meta-skill that creates other skills autonomously. It follows a **5-phase process**:

1. **DISCOVERY** - Research APIs, compare options, decide which to use
2. **DESIGN** - Define use cases, analyses, and methodologies
3. **ARCHITECTURE** - Structure folders, define scripts, plan performance
4. **DETECTION** - Determine activation keywords and create descriptions
5. **IMPLEMENTATION** - Create all files (marketplace.json, SKILL.md, scripts, tests)

**Key references for agent creation:**
- `agent-skill-creator/references/phase1-discovery.md` through `phase6-testing.md`
- `agent-skill-creator/references/activation-patterns-guide.md` - 30+ tested regex patterns
- `agent-skill-creator/references/quality-standards.md` - Enterprise-grade requirements
- `agent-skill-creator/references/templates/marketplace-robust-template.json`

## Skill Structure Requirements

Every skill MUST have a `SKILL.md` with:

### Required YAML Frontmatter
```yaml
---
name: skill-name-in-kebab-case
description: Complete description of what the skill does and when to use it
---
```

- `name`: Lowercase alphanumeric + hyphens only, max 64 chars
- `description`: Max 1024 chars, must include BOTH what it does AND when to use it

### Optional Frontmatter Fields
```yaml
license: Apache 2.0
allowed-tools: Read, Edit, Write  # Restrict which tools can be used
metadata:
  version: "1.0.0"
  author: "Your Name"
```

### Recommended Directory Structure
```
skill-name/
├── SKILL.md              # Required: Main instructions
├── scripts/              # Optional: Executable code (Python, JS, etc.)
├── references/           # Optional: Loaded as needed by Claude
├── assets/               # Optional: Templates, images, boilerplate
└── README.md             # Optional: User documentation
```

### Agent-Created Skills Use `-cskill` Suffix
Skills created by agent-skill-creator follow the naming convention:
- `{descriptive-name}-cskill/` (e.g., `stock-analyzer-cskill/`)
- This identifies them as Claude Skills created by Agent-Skill-Creator
- See `agent-skill-creator/docs/NAMING_CONVENTIONS.md` for complete rules

## Skill Activation System

Skills are **model-invoked** - Claude decides when to use them based on the description. The agent-skill-creator implements a **4-Layer Activation System** achieving 99.5%+ reliability:

1. **Keywords** (50-80 keywords) - High-precision explicit requests
2. **Patterns** (10-15 regex patterns) - Natural language variations
3. **Description + NLU** (300-500 chars with 60+ keywords) - Edge case fallback
4. **Context-Aware Filtering** - Prevents false positives in wrong contexts

**Key activation references:**
- `agent-skill-creator/references/activation-patterns-guide.md`
- `agent-skill-creator/references/activation-testing-guide.md`
- `agent-skill-creator/references/context-aware-activation.md`
- `agent-skill-creator/references/multi-intent-detection.md`

## Code Architecture Patterns

### Progressive Disclosure (Official Pattern)
Skills load in three stages to optimize token usage:
1. **Metadata** (name + description) - Always in context (~100 words)
2. **SKILL.md body** - When skill triggers (<5k words recommended)
3. **Bundled resources** - As needed by Claude

### Script Organization
- Place reusable code in `scripts/` directory
- Use for operations that are repeatedly rewritten or need deterministic reliability
- Scripts may be executed without loading into context window
- Example: `scripts/rotate_pdf.py` for PDF operations

### Reference Files vs SKILL.md
- **SKILL.md**: Essential procedural instructions, workflow guidance
- **references/**: Detailed documentation, API specs, schemas, examples
- Avoid duplication - put detailed info in references/, keep SKILL.md lean
- For large files (>10k words), include grep search patterns in SKILL.md

## MCP Server Development

Use the `mcp-builder` skill for creating MCP servers. Key workflow:

1. **Research** - Study MCP protocol, SDK docs, API documentation exhaustively
2. **Plan** - Tool selection, input/output design, error handling strategy
3. **Implement** - Core infrastructure first, then tools systematically
4. **Evaluate** - Create 10 complex, realistic test questions

**Essential references:**
- `mcp-builder/reference/mcp_best_practices.md` - Universal guidelines
- `mcp-builder/reference/python_mcp_server.md` - Python/FastMCP guide
- `mcp-builder/reference/node_mcp_server.md` - TypeScript guide
- `mcp-builder/reference/evaluation.md` - Testing methodology

## Git Workflow

This repository has two remotes:
- `origin`: Cedriccmh/skill-creator-next (your enhanced version)
- `upstream`: anthropics/skills (official Anthropic repository)

To sync with upstream changes:
```bash
git fetch upstream
git merge upstream/main
# Resolve conflicts if any
```

## Writing Style for Skills

- Use **imperative/infinitive form** (verb-first instructions), not second person
- Example: "To accomplish X, do Y" NOT "You should do X"
- Write objectively and instructionally for AI consumption
- Third-person descriptions: "This skill should be used when..." NOT "Use this skill when..."

## Quality Standards

When creating or evaluating skills:
- ✅ **100% Functional Code** - No TODOs, no placeholders
- ✅ **Production Ready** - Error handling, logging, validation
- ✅ **Comprehensive Documentation** - Clear usage examples, troubleshooting
- ✅ **Type Safety** - Python type hints, TypeScript strict mode
- ✅ **Testing** - Built-in validation and test cases

## Important Implementation Notes

### AgentDB Integration (agent-skill-creator)
- Optional learning system that stores agent creation episodes
- Works with or without AgentDB - graceful fallback
- See `agent-skill-creator/docs/AGENTDB_LEARNING_FLOW_EXPLAINED.md`
- Integration code in `agent-skill-creator/integrations/`

### Cross-Platform Export (v3.2 Feature)
Skills can be exported for different platforms:
- **Claude Code** - Native (no export needed)
- **Desktop/Web** - .zip upload
- **API** - Programmatic integration

Export utilities in `agent-skill-creator/scripts/export_utils.py`
Guide: `agent-skill-creator/references/export-guide.md`

### Marketplace vs Plugin vs Skill
- **Marketplace**: Distribution container (has `.claude-plugin/marketplace.json`)
- **Plugin**: Executor/manager (how Claude Code loads it)
- **Skill**: Actual functionality (SKILL.md with instructions)

Install: `/plugin marketplace add <path>` in Claude Code

## Key Documentation Files to Reference

**For basic skill creation:**
- `agent_skills_spec.md` - Official specification
- `skill-creator/SKILL.md` - Standard creation process
- `template-skill/SKILL.md` - Minimal starting template
- `docs/skill-authoring-best-practice.md` - Authoring guidelines
- `docs/building-custom-skills.md` - Comprehensive examples

**For advanced agent creation:**
- `agent-skill-creator/README.md` - Feature overview and user guide
- `agent-skill-creator/SKILL.md` - Meta-skill implementation
- `agent-skill-creator/docs/CLAUDE_SKILLS_ARCHITECTURE.md` - Architecture patterns
- `agent-skill-creator/docs/DECISION_LOGIC.md` - Choosing architectures
- `agent-skill-creator/docs/PIPELINE_ARCHITECTURE.md` - Creation pipeline

**For activation/detection:**
- `agent-skill-creator/references/ACTIVATION_BEST_PRACTICES.md`
- `agent-skill-creator/references/activation-quality-checklist.md`
- `agent-skill-creator/references/context-aware-activation.md`

## Development Principles for skill-creator-next

When enhancing the skill-creator-next:

1. **Learn from Both Sources** - Combine Anthropic's simplicity with agent-skill-creator's sophistication
2. **Maintain Compatibility** - Ensure skills work across Claude Code, Desktop, Web, and API
3. **Progressive Enhancement** - Start simple, add complexity as needed
4. **Documentation First** - Clear docs enable both humans and AI to use skills effectively
5. **Activation Reliability** - Aim for 99.5%+ activation accuracy using proven patterns
6. **Quality Over Speed** - Production-ready code with no TODOs or placeholders

## Testing and Validation

Always validate skills before distribution:
1. Run `quick_validate.py` to check structure
2. Test activation phrases manually in Claude Code
3. Verify all scripts execute without errors
4. Review documentation for completeness
5. Use `package_skill.py` which validates automatically

## Common Pitfalls to Avoid

- ❌ Including sensitive information (API keys) in skill files
- ❌ Creating files with TODOs or placeholder code
- ❌ Writing second-person instructions ("You should...")
- ❌ Duplicating information between SKILL.md and references/
- ❌ Creating overly broad skill descriptions that cause false activations
- ❌ Forgetting to include activation examples in documentation
- ❌ Missing the `-cskill` suffix for agent-created skills
