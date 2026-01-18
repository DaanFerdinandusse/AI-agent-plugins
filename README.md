# AI Agent Plugins

A collection of Claude Code plugins for enhanced development workflows.

## Plugins

### spec-driven-dev

Create comprehensive specification documents through deep iterative interviews, then implement them phase-by-phase with verification and decision logging.

**Commands:**
- `/interview-spec` - Create a detailed spec through an interactive interview process
- `/brainstorm` - Collaboratively brainstorm high-level architecture and create an implementation plan
- `/implement-spec` - Implement an approved spec phase-by-phase with verification
- `/restore-changes` - Restore uncommitted changes using git

**Agents:**
- `spec-codebase-explorer` - Explores existing codebase for context during spec creation
- `spec-doc-researcher` - Researches package documentation using Context7 MCP

### react-best-practices

React and Next.js performance optimization guidelines from Vercel Engineering. Contains 40+ rules across 8 categories for writing, reviewing, and refactoring React/Next.js code.

**Skills (auto-activated):**
- `react-best-practices` - Automatically applies performance optimization patterns when working with React/Next.js

**Categories covered:**
- Eliminating waterfalls (Critical)
- Bundle size optimization (Critical)
- Server-side performance (High)
- Client-side data fetching (Medium-High)
- Re-render optimization (Medium)
- Rendering performance (Medium)
- JavaScript performance (Low-Medium)
- Advanced patterns (Low)

## Installation

### 1. Add the marketplace

```bash
/plugin marketplace add DaanFerdinandusse/AI-agent-plugins
```

### 2. Install a plugin

```bash
/plugin install spec-driven-dev@daan-plugins
```

## Usage

After installation, use the commands directly in Claude Code:

```bash
/spec-driven-dev:interview-spec
/spec-driven-dev:implement-spec path/to/spec.md
/spec-driven-dev:restore-changes
```

## Structure

```
.
├── .claude-plugin/
│   └── marketplace.json      # Marketplace catalog
├── plugins/
│   ├── spec-driven-dev/
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json   # Plugin manifest
│   │   ├── agents/           # Custom agents
│   │   │   ├── spec-codebase-explorer.md
│   │   │   └── spec-doc-researcher.md
│   │   └── commands/         # Slash commands
│   │       ├── interview-spec.md
│   │       ├── brainstorm.md
│   │       ├── implement-spec.md
│   │       └── restore-changes.md
│   └── react-best-practices/
│       ├── .claude-plugin/
│       │   └── plugin.json   # Plugin manifest
│       └── skills/           # Auto-activated skills
│           ├── react-best-practices.md
│           └── references/   # 40+ rule files
└── README.md
```

## License

MIT
