# UCWS Singapore Hackathon 2026 — Submission

## Project Information

| Field | Details |
|-------|---------|
| **Project Name** | DAMC — Know Your Worth in the AI Era |
| **Track** | Skills |
| **Team Size** | 1 (Solo) |
| **Submission Date** | 2026-05-25 |

## One-Line Pitch

An AI-native Claude Code skill that scans your real AI usage environment, scores your value across 4 dimensions (Distillation, Anti-Distillation, AI Mastery, Career Compass), and generates a shareable career report — in 30 seconds.

## Problem Statement

Every professional is asking "Will AI replace me?" but there is no data-driven tool that evaluates your actual relationship with AI. Existing assessments rely on self-reporting surveys or generic personality tests — none of them look at how you actually use AI.

## Solution

DAMC (Distillation Value, Anti-Distillation Index, AI Mastery, Career Compass) is a Claude Code skill that:

1. **Automatically scans** your Claude Code environment (skills, config, memory, hooks, MCP servers, git history)
2. **Asks only 3 questions** (role, core output, MBTI)
3. **Scores you across 22 sub-dimensions** using a hybrid automated + inference model
4. **Generates a beautiful HTML report** with radar chart, career archetype, and action items
5. **Respects privacy** — raw data never leaves your machine; only numeric scores upload (with consent)

## Key Differentiators

1. **Data-driven, not survey-driven**: DAMC scans real behavior, not self-reported answers
2. **AI evaluating AI usage**: Meta AI-native — using AI to understand your relationship with AI
3. **Privacy-first**: Score-only upload architecture, explicit consent model
4. **Instant value**: 30-second scan → visual report with actionable career guidance
5. **Universal framework**: D/A/M/C applies to any profession, any country, any AI tool ecosystem

## Technical Highlights

- **Self-contained HTML report**: Single file, no dependencies, works offline, dark theme with SVG radar chart
- **Hybrid scoring model**: M dimension is 100% objective (automated scan), D and A blend automated signals with role-based inference
- **8 career archetypes**: Based on high/low combinations of D, A, M — each with specific risk assessment and action items
- **Privacy architecture**: Three-tier data classification with mandatory consent flow

## Demo Instructions

```bash
# Prerequisites: Claude Code installed
# Install DAMC skill
npx skills add https://github.com/[your-username]/damc-skill

# Run assessment
# In Claude Code, type:
/damc
# Then follow the prompts
```

## Business Model

| Tier | Features | Revenue Model |
|------|----------|---------------|
| Free (LITE) | 4-dimension scores + archetype + local report | Open source, community growth |
| Pro | 22 sub-dimensions + distillable skills list + moat analysis + 90-day action plan | Platform subscription |
| Enterprise | Team analytics, department benchmarking, workforce transformation insights | B2B SaaS |

## Global Scalability

- Framework is language-agnostic (scan-based scoring works regardless of language)
- Career archetypes map to universal workplace dynamics
- Bilingual English + Chinese from day one
- Zero infrastructure for core functionality (runs in Claude Code)
- Expandable to other AI coding tools (VS Code + Copilot, Cursor, Windsurf)

## Team

**Jayden** — Solo builder
- AI-native developer and content creator
- 170+ Claude Code skills ecosystem builder
- Experienced in AI product development and growth

## Links

| Resource | URL |
|----------|-----|
| GitHub Repository | [To be updated after repo creation] |
| Platform | damc.ai |
| Demo Video | [To be recorded] |

---

*Built for UCWS Singapore Hackathon 2026 — Skills Track*
*NO RULES. JUST CREATE.*
