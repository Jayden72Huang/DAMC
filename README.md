<p align="center">
  <img src="docs/assets/damc-banner.png" alt="DAMC Banner" width="800">
</p>

<h1 align="center">DAMC — Know Your Worth in the AI Era</h1>

<p align="center">
  <strong>An AI-native skill that scans your AI environment, quantifies your value across 4 dimensions, and generates a personalized career report — in 30 seconds.</strong>
</p>

<p align="center">
  <a href="#quick-start">Quick Start</a> •
  <a href="#how-it-works">How It Works</a> •
  <a href="#the-damc-framework">The Framework</a> •
  <a href="#demo">Demo</a> •
  <a href="README.zh-CN.md">中文</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Track-Skills-blue" alt="Track: Skills">
  <img src="https://img.shields.io/badge/Platform-Claude_Code-purple" alt="Platform: Claude Code">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License: MIT">
  <img src="https://img.shields.io/badge/UCWS_Hackathon-2026-orange" alt="UCWS Hackathon 2026">
</p>

---

## The Problem

> Everyone is asking: *"Will AI replace me?"* — but nobody has a real answer.

In the AI era, professionals face an existential question: **What is my real value when AI can do so much?** Current assessments are either generic personality tests or hand-wavy "AI readiness" surveys. None of them actually look at *how you use AI* to evaluate *where you stand*.

## The Solution

**DAMC** is a Claude Code skill that **uses AI to evaluate your relationship with AI**. It automatically scans your actual AI usage environment — skills, configurations, memory systems, automation pipelines, git history — and combines this with a lightweight career profile to generate a quantified, actionable assessment.

No surveys. No self-reporting bias. Just data.

<p align="center">
  <img src="docs/assets/report-preview.png" alt="DAMC Report Preview" width="700">
</p>

## Quick Start

### Installation

```bash
# Install via npx skills (recommended)
npx skills add https://github.com/anthropics/damc-skill

# Or clone manually
git clone https://github.com/anthropics/damc-skill.git ~/.claude/skills/damc
```

### Run Your Assessment

In Claude Code, simply type:

```
/damc
```

or say:

```
Evaluate my value in the AI era
```

That's it. DAMC handles the rest — scanning, scoring, and report generation — automatically.

## What's New (v2)

DAMC v2 transforms from a one-time assessment into a **continuous improvement system** with three major new features:

### Re-scan & Progress Tracking

Run `/damc` again and see how you've evolved. DAMC saves each scan locally to `~/.claude/damc-history/` and shows your delta changes on the next run.

```
📈 Progress Tracking (vs last scan 2026-05-10)

  D Distillation  83 → 83  ↔️ No change
  A Anti-Distill  76 → 75  ⬇️ -1
  M AI Mastery    89 → 92  ⬆️ +3
  C Career        81 → 83  ⬆️ +2

  Overall: 82 → 83 ⬆️
```

Your HTML report now includes a Progress section with trend sparklines when you have 3+ scans.

### Smart Skill Recommendations

DAMC doesn't just score you — it tells you **exactly what to do**. Based on your weakest sub-dimensions, it recommends specific skills to install, commands to run, and habits to build.

```
🎯 Smart Recommendations (based on your weakest sub-dimensions)

  📉 Physical Presence (45/100) — A dimension gap
     → Join 1 offline tech community event per month
     → Try installing: meetup-finder, event-scheduler

  📉 Memory System (13/25) — M dimension gap
     → You only have 3 memory types — add project and reference types
     → Run: claude memory add --type project "..."

  📉 Standardization (72/100) — D dimension gap
     → You have 58 projects but few project-level CLAUDE.md files
     → Add dedicated CLAUDE.md for your top 5 projects
```

Every recommendation is concrete, data-backed, and immediately actionable — not generic advice.

### Team Leaderboard

**找到你身边与 Agent 协作最6的人！** (Find the best Agent collaborator around you!)

Join a team with a group code and see how you rank against your colleagues or community members.

```
🏆 ACME-CORP Leaderboard (12 members)

  1. 🥇 Jayden   83 pts  AI Architect
  2. 🥈 Alex     76 pts  AI-Native Creator
  3. 🥉 Sarah    71 pts  Efficiency Machine
  ...

  Your rank: #1 / 12 🎉
```

- Create or join a team with a simple code (e.g., "ACME-CORP", "UCWS-2026")
- Privacy-first: only your total score and archetype are shared with the team
- Optionally share your installed skills list with teammates
- Team leaderboard page: `vibergo.space/damc/team/{CODE}`

The team feature turns DAMC into a **viral loop** — when one person shares their score, the whole team wants to try it.

---

## How It Works

```
┌───────────────────────────────────────────────────────────────────┐
│                       DAMC v2 Workflow                             │
│                                                                   │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │  Phase 1  │─▶│ Phase 1.5 │─▶│  Phase 2  │─▶│  Phase 3  │         │
│  │   SCAN    │  │ HISTORY   │  │  PROFILE  │  │   SCORE   │         │
│  │           │  │  CHECK    │  │           │  │           │         │
│  │ • Skills  │  │           │  │ 3 Quick   │  │ Scoring   │         │
│  │ • Config  │  │ • Load    │  │ Questions │  │ Algorithm │         │
│  │ • Memory  │  │   prev    │  │           │  │ (22 sub-  │         │
│  │ • Hooks   │  │   scores  │  │ • Role    │  │ dimensions│         │
│  │ • MCP     │  │ • Calc    │  │ • Output  │  │  mapped)  │         │
│  │ • Git     │  │   deltas  │  │ • MBTI    │  │           │         │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘          │
│                                                   │                │
│                                                   ▼                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │  Phase 5  │─▶│ Phase 4.8 │◀─│  Phase 4  │◀─│Phase 3.5  │         │
│  │  UPLOAD   │  │   TEAM    │  │  REPORT   │  │  SMART    │         │
│  │ (optional)│  │LEADERBOARD│  │           │  │  RECS     │         │
│  │           │  │           │  │ • HTML    │  │           │         │
│  │ Scores +  │  │ • Join    │  │ • Radar   │  │ • Weakest │         │
│  │ group_code│  │   team    │  │ • Career  │  │   dims    │         │
│  │ → API     │  │ • Show    │  │ • Progress│  │ • Skills  │         │
│  │           │  │   rank    │  │ • Recs    │  │ • Actions │         │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘          │
│                                                                   │
│  💾 Save snapshot → ~/.claude/damc-history/scan-{date}.json       │
└───────────────────────────────────────────────────────────────────┘
```

### Phase 1: Automated Scan (Zero User Input)

DAMC scans your Claude Code environment silently:

| Data Source | Signals Extracted |
|-------------|------------------|
| `~/.claude/CLAUDE.md` | Configuration depth, custom rules, workflow definitions |
| `~/.claude/skills/` | Total count, self-built vs installed, category diversity |
| `~/.claude/memory/` | Memory file count, type distribution |
| `settings.json` | Hooks count, MCP servers, permission model |
| `git log` | AI-collaborative commits (Co-Authored-By), commit frequency |
| System tools | Dev tool breadth (Node/Python/Go/Docker/K8s) |

### Phase 2: Quick Profile (3 Questions Only)

1. **What's your role?** (e.g., developer, PM, designer, marketer)
2. **What's your core output?** (e.g., code, documents, designs, decisions)
3. **Your MBTI type?** (optional — skip if you don't know)

### Phase 1.5: History Check (NEW)

Checks `~/.claude/damc-history/` for previous scan results. If found, calculates delta changes for each dimension and prepares progress tracking data.

### Phase 3: Scoring Engine

22 sub-dimensions scored across 4 dimensions, producing an overall 0-100 score and one of 8 career archetypes.

### Phase 3.5: Smart Recommendations (NEW)

Analyzes the 3 weakest sub-dimensions and generates specific, actionable recommendations — including exact skills to install, commands to run, and habits to build.

### Phase 4: Visual Report

A beautiful, self-contained HTML report with:
- Radar chart visualization
- Animated score bars
- Career archetype analysis
- **Progress tracking with trend sparklines** (NEW)
- **Personalized skill recommendations** (NEW)
- **Team leaderboard** (NEW, if joined)
- Shareable format

### Phase 4.8: Team Leaderboard (NEW)

Users can join a team by entering a group code. The platform returns a ranked leaderboard showing who has the highest DAMC score in the team.

### Phase 5: Platform Upload (Optional)

Upload payload now includes optional `group_code` and `display_name` fields. API response includes leaderboard rank when a team is joined.

## The DAMC Framework

DAMC evaluates your value through four complementary lenses:

| Dimension | Full Name | Core Question | Weight |
|-----------|-----------|---------------|--------|
| **D** | Distillation Value | Is your expertise worth distilling into an AI skill? | 25% |
| **A** | Anti-Distillation | Which of your abilities can AI never replicate? | **30%** |
| **M** | AI Mastery | How well do you wield AI tools? | 25% |
| **C** | Career Compass | Based on D, A, M — where should you go next? | 20% |

**Why A has the highest weight**: In the AI era, your irreplaceable human qualities — creativity, emotional intelligence, cross-domain thinking, ambiguity tolerance — are your ultimate moat.

### The 8 Career Archetypes

Based on High/Low combinations of D, A, M:

| Archetype | D | A | M | Description |
|-----------|---|---|---|-------------|
| 🏆 AI Architect | High | High | High | Top of the food chain — expert, irreplaceable, and AI-fluent |
| 🎯 Sleeping Expert | High | High | Low | Diamond-level expertise, just hasn't amplified it with AI yet |
| ⚡ Efficiency Machine | High | Low | High | High-output AI user, but the work itself is automatable |
| 🚨 Danger Zone | High | Low | Low | Replaceable knowledge + no AI skills = urgent action needed |
| 🌟 AI-Native Creator | Low | High | High | Value lies in creativity and judgment, AI amplifies it |
| 💎 Uncut Diamond | Low | High | Low | Irreplaceable soft skills, hasn't discovered AI leverage yet |
| 🔧 AI Tool Operator | Low | Low | High | Good at using AI, but "using tools" alone depreciates |
| 📚 Explorer | Low | Low | Low | Early career or transitioning — best time to reposition |

### Scoring Details

**M Dimension (AI Mastery)** — 100% automated, zero self-reporting:

| Sub-dimension | Max Score | Signals |
|---------------|-----------|---------|
| Environment Config | 20 | CLAUDE.md depth, keybindings, settings |
| Skill Ecosystem | 25 | Skill count, self-built ratio, category coverage |
| Automation & Integration | 20 | Hooks, MCP servers |
| Memory System | 15 | Memory files, type diversity |
| Advanced Features | 20 | Multi-project config, cron, agent teams, AI commits |

**D & A Dimensions** — hybrid scoring: automated signals (40-70%) + role-based inference (30-60%)

**C Dimension** — derived from D, A, M with optional MBTI adjustment for career path recommendations

## Privacy-First Design

DAMC is designed with a strict privacy architecture:

```
✅ What stays local (ALWAYS):
   • CLAUDE.md content
   • Memory file content  
   • Git commit messages
   • Skill names and paths
   • Project paths
   • Personal identifiers

❌ What NEVER leaves your machine:
   • Raw scan data
   • Configuration file contents
   • Any personally identifiable information

🔒 What uploads (ONLY with explicit consent):
   • Numeric scores only (D/A/M/C + 22 sub-scores)
   • Archetype name
   • Role (user-provided)
   • Aggregate scan statistics (counts only, no names)
```

Users can choose:
- **"Agree"** → Full experience with platform features
- **"Local mode"** → Everything stays on your machine
- **"Cancel"** → Exit immediately

## Technical Architecture

```
damc/
├── SKILL.md                    # Main skill definition (Claude Code format)
├── references/
│   ├── scoring-framework.md    # 22-dimension scoring algorithm
│   └── career-archetypes.md    # 8 archetype definitions + matching logic
├── templates/
│   └── report.html             # Self-contained HTML report template
│                                 (CSS + JS + SVG radar chart, no dependencies)
├── docs/
│   ├── ARCHITECTURE.md         # Technical deep-dive
│   └── assets/                 # Demo screenshots and banner
├── README.md                   # This file (English)
├── README.zh-CN.md             # Chinese version
└── LICENSE                     # MIT License

Local data (created at runtime):
~/.claude/damc-history/         # Progress tracking history
├── scan-2026-04-15.json        # Timestamped score snapshots
├── scan-2026-05-10.json
└── scan-2026-05-25.json
```

### Key Technical Decisions

1. **Self-contained HTML report**: Single file, no CDN dependencies, works offline. Dark theme with animated radar chart, gradient bars, and responsive design.

2. **Hybrid scoring model**: M dimension is 100% objective (automated scan), D and A blend automated signals with role-based inference, creating a balanced assessment that's neither pure black-box nor pure self-report.

3. **Score-only upload**: Privacy architecture ensures only numeric scores travel over the network. Raw content never leaves the machine.

4. **Claude Code native**: Built as a Claude Code skill — no separate backend, no browser extension, no additional setup. Just install and run.

## Global Scalability

DAMC is designed for global adoption:

- **Language-agnostic scoring**: The scan-based M dimension works regardless of language
- **Role-universal framework**: D, A, M, C dimensions apply to any profession in any country
- **Cultural adaptability**: Career archetypes map to universal workplace dynamics
- **English + Chinese**: Full bilingual support from day one
- **Zero-infrastructure**: Runs entirely within Claude Code — no servers needed for core functionality

## Commercial Potential

| Tier | Features | Model |
|------|----------|-------|
| **Free (LITE)** | 4-dimension scores + archetype + local HTML report + progress tracking + smart recommendations | Open source skill |
| **Pro** | 22 sub-dimension deep dive + distillable skills list + moat analysis + 90-day action plan + team leaderboard | Platform (damc.ai) |
| **Enterprise** | Team analytics, department benchmarking, workforce transformation insights, org-wide DAMC rankings | B2B SaaS |

### The Viral Loop

```
User runs /damc → Gets score + archetype → Shares with team
    ↓
Team members try /damc → Join team leaderboard → Compete & improve
    ↓
Re-scan shows progress → User shares improvement → More people join
    ↓
"找到你身边与 Agent 协作最6的人！" → Organic growth engine
```

## Demo

### Running DAMC

```bash
# In Claude Code terminal
> /damc

🔒 DAMC Privacy Notice (please read)
I will scan your local environment to generate an Agent Health Report...

→ Type "agree" to continue
→ Type "local" for local-only mode
→ Type "cancel" to exit

> agree

🔍 Scanning environment...
  ✅ CLAUDE.md: 150 lines, 12 custom rules
  ✅ Skills: 83 total, 5 self-built, 10 categories
  ✅ Memory: 12 files, 4 types
  ✅ Hooks: 3 configured
  ✅ MCP: 8 servers
  ✅ Git: 45/200 AI-collaborative commits

Quick profile questions...
  1. Your role: Frontend Developer
  2. Core output: Code
  3. MBTI: INTJ

📊 DAMC Assessment Complete

  D ████████░░  78    M █████████░  85
  A ██████░░░░  62    C ██████░░░░  65

  Archetype: 🏆 AI Architect
  "Deep expertise + irreplaceable skills + AI fluency = top of the food chain"

📈 Progress Tracking (vs last scan 2026-05-10)
  D 78 → 78  ↔️  |  A 60 → 62  ⬆️ +2  |  M 82 → 85  ⬆️ +3  |  C 63 → 65  ⬆️ +2
  Overall: 71 → 72 ⬆️

🎯 Smart Recommendations
  📉 Physical Presence (40/100) → Join 1 offline tech community event/month
  📉 Memory System (12/25) → Run: claude memory add --type project "..."
  📉 Cross-domain (68/100) → Try skills: seo-audit, geo, xiaohongshu

👥 Team: ACME-CORP — Rank #1 / 12 🎉
  🔗 vibergo.space/damc/team/ACME-CORP

  📄 Full report saved: ~/Desktop/DAMC-Report-2026-05-25.html
```

## Contributing

Contributions welcome! Areas we'd love help with:

- **New language support**: Translate archetypes and recommendations
- **Scoring model refinement**: Suggest new signals or weighting adjustments
- **Platform integrations**: VS Code, Cursor, Windsurf skill versions
- **Enterprise features**: Team-level analytics design

## License

MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">

**Built for [UCWS Singapore Hackathon 2026](https://epicconnector.ai) — Skills Track**

*DAMC: Because in the AI era, knowing your value is the first step to multiplying it.*

</div>
