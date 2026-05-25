# DAMC Architecture

## System Overview

DAMC operates as a Claude Code skill — a self-contained instruction set that transforms Claude from a general-purpose agent into a specialized career assessment engine.

```
User ──▶ Claude Code ──▶ DAMC Skill ──▶ Report
                │
                ├── Phase 1: Environment Scan (automated)
                │   ├── ~/.claude/CLAUDE.md
                │   ├── ~/.claude/skills/
                │   ├── ~/.claude/memory/
                │   ├── ~/.claude/settings.json
                │   ├── ~/.claude/projects/
                │   └── git log (if in a repo)
                │
                ├── Phase 2: Quick Profile (3 questions)
                │   └── Role + Core Output + MBTI (optional)
                │
                ├── Phase 3: Scoring Engine
                │   ├── M: 100% automated (5 sub-dimensions, max 100)
                │   ├── D: 70% auto + 30% role inference (5 sub-dimensions)
                │   ├── A: 40% auto + 60% role inference (6 sub-dimensions)
                │   └── C: f(D, A, M) + MBTI adjustment
                │
                ├── Phase 4: Report Generation
                │   ├── Inject DAMC_DATA into report.html template
                │   └── Save to ~/Desktop/DAMC-Report-{date}.html
                │
                └── Phase 5: Platform Upload (optional)
                    └── POST numeric scores to API (never raw content)
```

## Scoring Engine Design

### Why Hybrid Scoring?

Pure automated assessment misses human context (creativity, leadership). Pure self-reporting suffers from bias. DAMC uses a hybrid approach:

| Dimension | Auto % | Inference % | Rationale |
|-----------|--------|-------------|-----------|
| M (AI Mastery) | 100% | 0% | Tool usage is fully measurable |
| D (Distillation) | 70% | 30% | Knowledge codifiability is partially measurable |
| A (Anti-Distillation) | 40% | 60% | Human qualities require role-based estimation |
| C (Career Compass) | Derived | — | Computed from D, A, M |

### Sub-dimension Mapping

**M Dimension** — 5 sub-scores, 100% objective:
- M1: Environment Config Depth (0-20)
- M2: Skill Ecosystem (0-25)
- M3: Automation & Integration (0-20)
- M4: Memory System (0-15)
- M5: Advanced Features (0-20)

**D Dimension** — 5 sub-scores, hybrid:
- Expertise Depth, Methodology Uniqueness, Knowledge Codifiability, Output Standardization, Market Demand

**A Dimension** — 6 sub-scores, hybrid:
- Creativity, Emotional Intelligence, Cross-domain Thinking, Ambiguity Tolerance, Physical Presence, Trust Capital

## Report Template Architecture

The report is a **single self-contained HTML file** with:
- No external dependencies (no CDN, no npm packages)
- Inline CSS with CSS custom properties for theming
- Vanilla JavaScript for data binding and SVG radar chart
- Responsive design (mobile-first)
- Dark theme with animated gradient accents
- Data injected via `window.DAMC_DATA` JSON object

This design ensures:
1. Works offline
2. Shareable as a single file
3. No security concerns (no external requests)
4. Consistent rendering across browsers

## Privacy Architecture

### Data Classification

```
TIER 1 (Never leaves machine):
├── File contents (CLAUDE.md, memory files, skill code)
├── File paths and names
├── Git commit messages
├── Personal identifiers
└── Configuration details

TIER 2 (Uploaded only with explicit consent):
├── Numeric scores (D, A, M, C + 22 sub-dimensions)
├── Archetype name (e.g., "AI Architect")
├── User-provided role label
├── Aggregate counts (totalSkills: 83, not skill names)
└── OS/shell type (for anonymized analytics)
```

### Consent Flow

```
User triggers /damc
    │
    ▼
Privacy notice displayed (REQUIRED, cannot skip)
    │
    ├── "agree" → Full experience (scan + upload + platform URL)
    ├── "local" → Local-only (scan + local HTML, no upload)
    └── "cancel" → Exit immediately
```

## Extensibility

DAMC is designed for future expansion:

- **New scan sources**: VS Code settings, Cursor config, other AI tools
- **New dimensions**: Team collaboration (T), Learning velocity (L)
- **New archetypes**: Industry-specific variants
- **Platform features**: Historical tracking, team comparisons, trend analysis
