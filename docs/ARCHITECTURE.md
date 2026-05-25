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

## Local History Storage (NEW)

### Design

DAMC stores timestamped score snapshots locally in `~/.claude/damc-history/` to enable progress tracking without any server dependency.

```
~/.claude/damc-history/
├── scan-2026-04-15.json        # First scan (baseline)
├── scan-2026-05-10.json        # Second scan
└── scan-2026-05-25.json        # Latest scan
```

### File Format

Each file is a JSON snapshot:

```json
{
  "date": "2026-05-25",
  "overall": 83,
  "scores": {
    "D": { "total": 83, "subs": { "expertise": 85, "methodology": 78, "codifiability": 90, "standardization": 72, "demand": 83 } },
    "A": { "total": 75, "subs": { "creativity": 80, "eq": 70, "crossDomain": 72, "ambiguity": 68, "physical": 45, "trust": 75 } },
    "M": { "total": 92, "subs": { "environment": 19, "skills": 23, "automation": 18, "memory": 13, "advanced": 19 } },
    "C": { "total": 83, "subs": {} }
  },
  "archetype": "AI架构师",
  "role": "前端开发",
  "scanSummary": {
    "totalSkills": 85,
    "customSkills": 7,
    "mcpServers": 10,
    "memoryFiles": 15,
    "claudeMdLines": 180,
    "aiCommits": 52,
    "totalCommits": 230
  }
}
```

### Delta Calculation Logic

```
delta = current_score - previous_score

Display rules:
  delta > 0  → "⬆️ +{delta}" (green)
  delta < 0  → "⬇️ {delta}" (red)
  delta == 0 → "↔️ 持平" (neutral)
```

When 3+ scans exist, a sparkline trend is rendered in the HTML report using inline SVG.

### Lifecycle

1. After Phase 1 scan completes, check if `~/.claude/damc-history/` exists
2. If exists, read all `scan-*.json` files, sort by date
3. Load the most recent as `previous` for delta calculation
4. After Phase 3 scoring, save current results as `scan-{today}.json`
5. If directory doesn't exist, create it (first run)
6. If today's file exists, overwrite (multiple scans in one day keep latest)

## Recommendation Engine (NEW)

### Design

The recommendation engine maps sub-dimension scores to a curated database of specific, actionable recommendations.

### Architecture

```
Phase 3 scores (22 sub-dimensions)
    │
    ▼
Score Rate Calculator
    │  score_rate = score / max_score
    │  Sort ascending (worst first)
    │
    ▼
Top 3 Weakest Sub-dimensions
    │
    ▼
Recommendation Mapper
    │  M dimension gaps → Skills to install, commands to run
    │  D dimension gaps → Workflows to codify, CLAUDE.md to write
    │  A dimension gaps → Cross-domain skills, habits to build
    │
    ▼
Formatted Recommendations (terminal + HTML report)
```

### Recommendation Database

Each sub-dimension has a mapping:

```
Sub-dimension → {
  threshold: score below which recommendation triggers,
  diagnosis: string template with {variables} from scan data,
  actions: [concrete action strings],
  skills: [skill names to install],
  commands: [CLI commands to run]
}
```

Example:

```json
{
  "M4_memory": {
    "threshold_rate": 0.6,
    "diagnosis": "你的 memory 文件只有 {memoryFiles} 个，类型覆盖面不足",
    "actions": ["添加 project 和 reference 类型记忆", "为常用项目创建专属 memory 条目"],
    "skills": [],
    "commands": ["claude memory add --type project \"...\""]
  }
}
```

### Key Design Decisions

1. **Specificity over generality**: Every recommendation must include at least one concrete action (skill name, command, or measurable habit)
2. **Data-backed**: Recommendations reference actual scan numbers (e.g., "you only have 3 memory files")
3. **Prioritized**: Only show top 3 weakest areas to avoid overwhelming users
4. **Cross-dimensional**: Recommendations span M, D, and A dimensions — not just technical improvements

## Team Leaderboard API (NEW)

### Design

The team leaderboard enables group-level comparison through a lightweight "group code" system.

### Architecture

```
User scans → Scores calculated → User enters group code (optional)
    │
    ▼
POST /api/scan (with group_code field)
    │
    ▼
Backend:
    ├── Save user score + archetype to group
    ├── If group doesn't exist, create it
    ├── Calculate rank within group
    └── Return leaderboard (top 10 + user's position)
    │
    ▼
Response:
{
  "token": "aB7xK9",
  "url": "https://vibergo.space/r/aB7xK9",
  "team": {
    "code": "ACME-CORP",
    "rank": 1,
    "total_members": 12,
    "leaderboard": [...],
    "team_url": "https://vibergo.space/damc/team/ACME-CORP"
  }
}
```

### API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `POST /api/scan` | POST | Submit scores (existing) — now accepts optional `group_code`, `display_name`, `share_skills` |
| `GET /api/team/{code}` | GET | Get leaderboard for a team (public, no auth required) |
| `GET /damc/team/{code}` | GET | Web page showing team leaderboard (shareable URL) |

### Group Code Rules

- Format: uppercase letters + digits + hyphens, 3-20 characters
- Examples: `ACME-CORP`, `UCWS-2026`, `TEAM-ALPHA`
- Auto-created on first use (no registration needed)
- One user can join multiple teams (each scan selects one)
- Display name defaults to user's role or can be customized

### Privacy Rules for Teams

```
Shared with team:
  ✅ Total DAMC score (overall number)
  ✅ Archetype name + emoji
  ✅ Display name (user-chosen)
  ✅ Installed skill list (ONLY if user opts in)

Never shared with team:
  ❌ 22 sub-dimension scores
  ❌ Scan details or raw data
  ❌ Email or personal identifiers
  ❌ Full report content
```

### Team Page Design

Each team gets a public URL: `vibergo.space/damc/team/{CODE}`

The page shows:
- Team name and total member count
- Ranked leaderboard (score + archetype per member)
- Aggregate team statistics (average DAMC, archetype distribution)
- CTA: "Join this team — run `/damc` and enter code `{CODE}`"
- Tagline: "找到你身边与 Agent 协作最6的人！"

## Data Flow Diagram (Complete v2)

```
┌─────────────────────────────────────────────────────────────────┐
│                         LOCAL MACHINE                            │
│                                                                  │
│  ~/.claude/ ──scan──▶ Phase 1 ──▶ Phase 1.5 ──▶ Phase 2        │
│                       (env data)   (load history)  (3 questions) │
│                                                                  │
│                       Phase 3 ◀──────────────────────────────┘   │
│                       (scoring)                                  │
│                          │                                       │
│                          ▼                                       │
│                       Phase 3.5 ──▶ Phase 4 ──▶ ~/Desktop/      │
│                       (smart recs)  (report)    DAMC-Report.html │
│                                        │                         │
│                                        ▼                         │
│                       ~/.claude/damc-history/                    │
│                       scan-{date}.json (saved)                   │
│                                                                  │
└────────────────────────────┬────────────────────────────────────┘
                             │ (only if user agrees + not local mode)
                             ▼
┌────────────────────────────────────────────────────────────────┐
│                      PLATFORM (vibergo.space)                   │
│                                                                 │
│  POST /api/scan                                                 │
│  Body: { scores (numbers only), group_code, display_name }     │
│                                                                 │
│  Response: { token, url, team: { rank, leaderboard, team_url }}│
│                                                                 │
│  GET /damc/team/{CODE} → Public leaderboard page               │
│  GET /r/{TOKEN} → Individual report page                       │
└────────────────────────────────────────────────────────────────┘
```

## Extensibility

DAMC is designed for future expansion:

- **New scan sources**: VS Code settings, Cursor config, other AI tools
- **New dimensions**: Team collaboration (T), Learning velocity (L)
- **New archetypes**: Industry-specific variants
- **Platform features**: Historical tracking, team comparisons, trend analysis
- **Recommendation engine plugins**: Community-contributed recommendation mappings for specific roles/industries
- **Team analytics**: Aggregate team DAMC distribution, identify skill gaps across organizations
- **Leaderboard gamification**: Monthly challenges, streak tracking, team vs team competitions
