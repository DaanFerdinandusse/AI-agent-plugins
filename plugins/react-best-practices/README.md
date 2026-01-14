# React Best Practices Plugin

**Author:** Vercel Engineering
**Version:** 1.0.0

A Claude Code plugin containing comprehensive React and Next.js performance optimization guidelines from Vercel Engineering. Designed to help AI agents write, review, and refactor React/Next.js code with optimal performance patterns.

## Overview

This plugin provides **40+ performance rules** across **8 categories**, prioritized by impact from critical to incremental. Each rule includes detailed explanations, real-world code examples comparing incorrect vs. correct implementations, and specific impact metrics.

## When This Skill Activates

The skill automatically triggers when working on tasks involving:

- Writing new React components or Next.js pages
- Implementing data fetching (client or server-side)
- Reviewing code for performance issues
- Refactoring existing React/Next.js code
- Optimizing bundle size or load times
- React Server Components (RSC)
- Performance improvements

## Priority Categories

| Priority | Category | Impact |
|----------|----------|--------|
| 1 | Eliminating Waterfalls | **CRITICAL** |
| 2 | Bundle Size Optimization | **CRITICAL** |
| 3 | Server-Side Performance | HIGH |
| 4 | Client-Side Data Fetching | MEDIUM-HIGH |
| 5 | Re-render Optimization | MEDIUM |
| 6 | Rendering Performance | MEDIUM |
| 7 | JavaScript Performance | LOW-MEDIUM |
| 8 | Advanced Patterns | LOW |

## Key Rules Included

### Critical Impact

**Eliminating Waterfalls:**
- Defer await until needed (move into branches)
- Use `Promise.all()` for independent async operations
- Start promises early, await late
- Prevent waterfall chains in API routes
- Use Suspense boundaries to stream content

**Bundle Size Optimization:**
- Avoid barrel file imports (import directly from source)
- Use `next/dynamic` for heavy components
- Defer non-critical third-party libraries
- Conditional module loading
- Preload based on user intent

### High Impact

**Server-Side Performance:**
- Use `React.cache()` for per-request deduplication
- Cross-request LRU caching
- Minimize serialization at RSC boundaries
- Parallel data fetching with component composition

### Medium Impact

**Client-Side Patterns:**
- Use SWR for automatic request deduplication
- Deduplicate global event listeners

**Re-render Optimization:**
- Defer state reads to usage point
- Use lazy state initialization for expensive values
- Subscribe to derived state
- Use transitions for non-urgent updates
- Extract to memoized components

**Rendering Performance:**
- Animate SVG wrappers, not SVG elements directly
- Use `content-visibility: auto` for long lists
- Prevent hydration mismatch without flickering
- Use explicit conditional rendering (`? :` not `&&`)
- Hoist static JSX elements

### Low-Medium Impact

**JavaScript Performance:**
- Batch DOM CSS changes via classes
- Build index maps for repeated lookups
- Cache repeated function calls
- Use `toSorted()` instead of `sort()` for immutability
- Early length check for array comparisons
- Use Set/Map for O(1) lookups

### Advanced Patterns

- Store event handlers in refs
- `useLatest` for stable callback references

## Plugin Structure

```
react-best-practices/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   ├── react-best-practices.md      # Main skill file
│   └── references/
│       ├── react-performance-guidelines.md  # Complete guide
│       └── rules/                    # 45 individual rule files
│           ├── async-*.md            # Waterfall elimination
│           ├── bundle-*.md           # Bundle optimization
│           ├── server-*.md           # Server-side performance
│           ├── client-*.md           # Client-side fetching
│           ├── rerender-*.md         # Re-render optimization
│           ├── rendering-*.md        # DOM rendering
│           ├── js-*.md               # JavaScript optimizations
│           └── advanced-*.md         # Advanced patterns
└── README.md
```

## Installation

Add to your Claude Code plugins by including this plugin in your marketplace configuration.

## Source

This plugin is based on [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills/tree/react-best-practices/skills/react-best-practices) - performance optimization guidelines developed by Vercel Engineering for AI-assisted React and Next.js development.

## License

See the original repository for license information.
