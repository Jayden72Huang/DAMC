---
name: damc
description: |
  AI时代个人价值评估器（DAMC模型）。自动扫描用户的 Claude Code 环境（.claude/ 目录、skills、hooks、memory、MCP 配置、git 历史等），
  量化评估四个维度：蒸馏价值(D)、抗蒸馏指数(A)、AI驾驭能力(M)、职业适配(C)，生成可分享的可视化 HTML 报告。
  触发条件：用户提到"评估我的价值"、"DAMC"、"distill score"、"蒸馏评估"、"AI能力评估"、
  "我会被AI取代吗"、"我的AI水平"、"蒸馏我"、"值不值得蒸馏"。
---

# DAMC — AI 时代个人价值评估器

> 不是恐吓你会被取代，而是帮你看清自己在 AI 时代的真实坐标。

## DAMC 模型

| 维度 | 全称 | 核心问题 |
|------|------|---------|
| **D** | Distillation Value | 你的经验值得被蒸馏成 AI Skill 吗？ |
| **A** | Anti-Distillation | 你的哪些能力是 AI 拿不走的？ |
| **M** | AI Mastery | 你驾驭 AI 工具的水平如何？ |
| **C** | Career Compass | 基于以上三维，你该往哪走？ |

## 执行流程

### Phase 0: 隐私告知（必须，第一步）

**新增团队功能提示**：在隐私告知末尾，增加团队排行榜选项说明：

```
👥 团队功能（可选）：
  扫描完成后，你可以加入一个团队排行榜，和同事/社区对比 DAMC 分数。
  团队码由组织者创建（如 "ACME-CORP"），加入后仅分享你的分数和画像。
```

**触发后立刻显示，等用户确认后才能扫描：**

```
🔒 DAMC 隐私承诺（请阅读）

我即将扫描你的本地环境来生成 Agent 体检报告，包括：
  ~/.claude/ 配置 · git 历史 · 已安装开发工具

数据流向（透明承诺）：
  ✅ 所有原始内容仅在你本地处理，永远不上传
  ✅ 仅评分数字（D/A/M/C 总分 + 22 个子维度）会上传到
     damc.ai 用于生成你的可分享报告
  ✅ 数据只用于生成你的报告，绝不用于训练 AI / 卖广告 /
     分享给第三方
  ✅ 任何时候 damc.ai/account/delete 一键清空所有数据

❌ 永不上传：CLAUDE.md 全文、MEMORY.md 内容、git commit
   原文、skill 名称列表、项目路径、邮箱

→ 输入 "同意" 继续
→ 输入 "本地模式" 仅生成本地报告（不上传，不能用平台功能）
→ 输入 "取消" 退出
```

**用户回答后路由：**
- "同意" / "yes" / "ok" → Phase 1 扫描 + Phase 5 上传 + 显示平台 URL
- "本地模式" / "local" → Phase 1 扫描 + 跳过 Phase 5，仅生成 ~/Desktop/HTML
- "取消" / "no" → 直接退出

### Phase 1: 自动扫描（核心 — 不依赖问卷）

**扫描以下数据源，实时播报进度，不需要用户参与。**

**必须实时输出扫描进度**（这是用户感知的关键仪式感）：

```
🔍 正在扫描你的 AI 环境...

  ├─ CLAUDE.md ············ {行数} 行，{规则数} 条自定义规则 ✅
  ├─ Skills ················ 发现 {总数} 个（{自建数} 个自建！）✅
  ├─ Memory ··············· {文件数} 个记忆文件，{类型数} 种类型 ✅
  ├─ Hooks ················ {数量} 个自动化钩子 ✅
  ├─ MCP Servers ·········· {数量} 个外部服务集成 ✅
  ├─ Git History ·········· {AI提交数}/{总提交数} 提交含 AI 协作 ✅
  └─ Dev Tools ············ {工具列表} 已安装 ✅

  ⏱️ 扫描完成（{耗时} 秒），发现 {类别数} 类 {信号数} 个评估信号
```

**每扫完一项就立即输出一行**，不要等全部扫完才一起显示。让用户（和 Demo Day 观众）实时看到扫描过程。

#### 1.1 Claude Code 环境扫描

```
扫描目标 → 评估信号
──────────────────────────────────────────
~/.claude/CLAUDE.md
  → 文件是否存在、行数、自定义规则数量、是否有工作流定义

~/.claude/settings.json
  → hooks 配置数量和类型、权限模式、MCP servers 列表

~/.claude/skills/
  → skill 总数、自建 vs 安装(检查是否为 symlink)、类别分布
  → 重点：有无自建 skill（非 symlink 的目录）

~/.claude/memory/
  → MEMORY.md 是否存在、memory 文件数量、类型分布

~/.claude/keybindings.json
  → 是否存在、自定义快捷键数量

~/.claude/projects/
  → 项目数量、项目级 CLAUDE.md 深度
```

#### 1.2 Git 历史扫描（如果当前在 git 仓库中）

```
git log --oneline -100
  → AI 协作提交数（含 Co-Authored-By 的提交）
  → 总提交频率

git log --all --author 过滤
  → 用户活跃度
```

#### 1.3 工作环境信号

```
检查已安装的开发工具
  → 是否有 node/python/go 等（技术栈广度）
  → 是否有 docker/k8s（DevOps 能力信号）
```

### Phase 1.5: 历史检查与进化追踪

**扫描完成后，立即检查本地历史数据：**

1. 检查 `~/.claude/damc-history/` 目录是否存在
2. 如果存在，读取最近一次扫描结果 JSON 文件（文件名格式：`scan-{YYYY-MM-DD}.json`）
3. 计算每个维度的 delta 变化

**历史数据存储格式**（`~/.claude/damc-history/scan-{YYYY-MM-DD}.json`）：

```json
{
  "date": "2026-05-25",
  "overall": 83,
  "scores": {
    "D": { "total": 83, "subs": { "expertise": 85, "methodology": 78, ... } },
    "A": { "total": 75, "subs": { "creativity": 80, "eq": 70, ... } },
    "M": { "total": 92, "subs": { "environment": 19, "skills": 23, ... } },
    "C": { "total": 83, "subs": {} }
  },
  "archetype": "AI架构师",
  "role": "前端开发",
  "scanSummary": { ... }
}
```

**Phase 1 完成后，自动保存当前扫描结果到 `~/.claude/damc-history/scan-{YYYY-MM-DD}.json`。**
如果目录不存在则创建。如果当天已有记录则覆盖。

**有历史数据时，终端输出进化追踪：**

```
📈 进化追踪（vs 上次扫描 2026-05-10）

  D 蒸馏价值  83 → 83  ↔️ 持平
  A 抗蒸馏    76 → 75  ⬇️ -1
  M AI驾驭   89 → 92  ⬆️ +3
  C 职业适配  81 → 83  ⬆️ +2

  Overall: 82 → 83 ⬆️
```

**Delta 显示规则：**
- 正向变化：`⬆️ +N`（绿色信号）
- 负向变化：`⬇️ -N`（红色信号）
- 无变化：`↔️ 持平`
- 首次扫描无历史时跳过此段，仅显示："📝 首次扫描，已建立基线。下次运行 /damc 将显示进化追踪。"

### Phase 2: 快速画像（仅问 3 个问题）

扫描完成后，**只问用户 3 个问题**（不是问卷，是对话）：

1. **你的职业角色是什么？**（如：前端开发、产品经理、SEO专家、设计师、运营）
2. **你最核心的工作产出是什么？**（如：代码、文档、设计稿、决策、沟通协调）
3. **你的 MBTI 类型？**（可选，输入"跳过"则不纳入分析）

### Phase 3: 评分计算

读取 `references/scoring-framework.md` 获取详细评分算法。

**核心原则：**
- M 维度（AI 驾驭）100% 基于自动扫描数据，客观量化
- D 维度（蒸馏价值）70% 自动扫描 + 30% 用户画像推断
- A 维度（抗蒸馏）40% 自动扫描 + 60% 基于角色/产出类型推断
- C 维度（职业适配）= f(D, A, M) + MBTI 调整

**读取 `references/career-archetypes.md` 匹配用户的 8 种画像之一。**

### Phase 3.5: 精准 Skill 推荐引擎

**评分完成后，分析用户最弱的子维度，生成具体、可执行的提升建议。**

**推荐逻辑：**
1. 从 22 个子维度中找出得分率最低的 3-5 项（得分/满分 比率最低者优先）
2. 根据子维度所属的 D/A/M 维度，匹配不同类型的推荐
3. 每条推荐必须包含：具体问题诊断 + 可执行动作 + 推荐安装的 skill/工具

**M 维度提升推荐映射表：**

| 子维度 | 得分低时的推荐 |
|--------|---------------|
| 环境配置 (M1) | "你的 CLAUDE.md 只有 {N} 行 → 建议参考高分用户模板，添加工作流规则和快捷指令。运行: `claude memory add` 开始积累" |
| Skill 生态 (M2 < 15) | "你只安装了 {N} 个 skills → 推荐安装: `firecrawl-scrape`(网页抓取), `perplexity-search`(深度搜索), `browse`(页面测试)。自建 skill 是高分关键: 尝试用 `/skillify` 将常用工作流封装" |
| 自动化集成 (M3 < 12) | "你只有 {N} 个 hooks 和 {N} 个 MCP servers → 推荐添加: GitHub MCP, Notion MCP, Supabase MCP。Hook 建议: 添加 pre-commit 代码检查 hook" |
| 记忆系统 (M4 < 8) | "你的 memory 文件只有 {N} 个 → 建议添加 project 类型和 reference 类型记忆。运行: `claude memory add --type project \"...\"` 开始积累项目经验" |
| 高级特性 (M5 < 12) | "你还没使用多项目配置/定时任务/Agent 协作 → 建议: 为你最常用的 3 个项目添加项目级 CLAUDE.md, 尝试 `/schedule` 设置定时任务" |

**D 维度提升推荐映射表：**

| 子维度 | 得分低时的推荐 |
|--------|---------------|
| 专业深度低 | "建议在你的核心领域产出更多可量化成果 → 将最佳实践文档化为 CLAUDE.md 规则" |
| 方法论独特性低 | "你的工作流标准化程度不够 → 尝试用 `/skillify` 将你的独特方法论编码为可复用 skill" |
| 知识可编码性低 | "建议将隐性知识显性化 → 为你的 top 3 工作流创建 step-by-step skill 定义" |
| 标准化程度低 | "你有 {N} 个项目但项目级 CLAUDE.md 较少 → 建议为 top 5 常用项目添加专属 CLAUDE.md，定义项目规范和工作流" |
| 市场需求低 | "建议拓展到 AI 时代高需求交叉领域 → 尝试 AI+你的领域 的组合 skill" |

**A 维度提升推荐映射表：**

| 子维度 | 得分低时的推荐 |
|--------|---------------|
| 创意力低 | "AI 最难复制非线性思维 → 尝试跨领域 skill: `design-consultation`, `ai-podcast-creation`" |
| 情商低 | "领导力和沟通是抗蒸馏核心 → 建议主动参与 code review 和 mentoring，安装 `/review` skill 实践" |
| 跨域思维低 | "单一领域知识最容易被蒸馏 → 每周学习一个非本领域 AI 用例，尝试: `seo-audit`, `geo`, `xiaohongshu` 等跨域 skill" |
| 模糊容忍力低 | "处理模糊需求是人类优势 → 练习用 AI 解决开放性问题，尝试 `/dev-idea-demo` 从模糊想法到原型" |
| 身体在场低 | "远程工作削弱了人际护城河 → 每月参加 1 次线下技术社区活动，尝试: `meetup-finder`, `event-scheduler`" |
| 信任资本低 | "建议在社区中建立 AI 导师身份 → 分享你的 DAMC 报告，帮助他人提升，安装 `/agent-reach` 建立影响力" |

**终端输出格式：**

```
🎯 精准提升建议（基于你的最弱子维度）

  📉 身体在场 (45/100) — A 维度最大短板
     → 建议：每月参加 1 次线下技术社区活动
     → 尝试安装 skill: meetup-finder, event-scheduler

  📉 记忆系统 (13/25) — M 维度可提升项
     → 你的 memory 类型只有 3 种，建议添加 project 和 reference 类型
     → 运行: claude memory add --type project "..."

  📉 标准化 (72/100) — D 维度可提升项
     → 你有 58 个项目但项目级 CLAUDE.md 较少
     → 建议为 top 5 常用项目添加专属 CLAUDE.md
```

**输出规则：**
- 最多显示 3 条最关键的推荐（得分率最低的 3 个子维度）
- 每条推荐必须包含具体数字（从扫描结果中引用）
- 每条推荐至少包含 1 个可直接执行的命令或可安装的 skill
- 推荐结尾附加："💡 90 天后再次运行 /damc，追踪你的进化轨迹！"

### Phase 4: 生成完整可视化报告

1. 读取 `templates/report.html` 模板
2. 将**完整**评分数据填入模板中的 `window.DAMC_DATA` 对象，**必须包含所有字段**：
   - `scores`：4 个维度的总分 **+ 全部 22 个子维度分数**
   - `insights.distillTargets`：2-4 项值得蒸馏的具体能力（基于扫描发现的自建 skill、高频工作流等推断）
   - `insights.moats`：2-3 项 AI 最难替代的护城河（基于 A 维度高分子项）
   - `insights.risks`：1-2 项最大风险点（基于最低分的子维度）
   - `insights.actions`：3 项 90 天行动建议（针对最大短板，具体可执行）
   - `scanSummary`：完整扫描统计数据
   - `percentiles`：全球基准百分位（见评分框架中的百分位计算方法）
3. 将完整版 HTML 保存到 `~/Desktop/DAMC-Report-{YYYY-MM-DD}.html`

4. **新增：Progress 进化追踪区块**（仅当有历史数据时）：
   - 在报告顶部（radar chart 下方）插入进化追踪卡片
   - 显示每个维度的分数变化趋势（带箭头和颜色标识）
   - 如有 3 次以上历史数据，显示迷你趋势线图（sparkline）
   - 在 `window.DAMC_DATA` 中添加 `history` 字段：
   ```javascript
   history: {
     previous: { date: "2026-05-10", overall: 82, D: 83, A: 76, M: 89, C: 81 },
     deltas: { overall: +1, D: 0, A: -1, M: +3, C: +2 },
     scanCount: 3  // 第几次扫描
   }
   ```

5. **新增：Recommendations 精准推荐区块**：
   - 在报告的 Actions 区块后，插入"精准提升建议"卡片
   - 每条推荐包含：子维度名称 + 当前分数 + 所属维度 + 具体行动 + 推荐 skill
   - 在 `window.DAMC_DATA` 中添加 `recommendations` 字段：
   ```javascript
   recommendations: [
     {
       subDimension: "身体在场",
       score: 45,
       maxScore: 100,
       dimension: "A",
       diagnosis: "远程工作削弱了人际护城河",
       actions: ["每月参加 1 次线下技术社区活动"],
       skills: ["meetup-finder", "event-scheduler"]
     },
     // ... 最多 3 条
   ]
   ```

6. **新增：Team Leaderboard 区块**（仅当用户加入了团队时）：
   - 在报告底部插入团队排行榜卡片
   - 显示团队名称、成员数、用户排名
   - 在 `window.DAMC_DATA` 中添加 `team` 字段：
   ```javascript
   team: {
     code: "ACME-CORP",
     rank: 1,
     totalMembers: 12,
     leaderboard: [
       { name: "Jayden", score: 83, archetype: "AI 架构师" },
       { name: "Alex", score: 76, archetype: "AI 原生创造者" },
       // ...
     ],
     url: "https://vibergo.space/damc/team/ACME-CORP"
   }
   ```

**关键原则**：insights 必须具体，不能用通用占位符。例如：
- ✅ "你的 SEO + 内容创作工作流（article-rewriter → programmatic-seo → backlink）可以蒸馏为完整的 SEO 自动化 Skill"
- ❌ "值得蒸馏的能力1"

### Phase 4.5: 生成社交分享卡

生成报告后，额外生成一段 HTML 分享卡代码，用户可以截图或直接分享：

```
📤 分享你的 DAMC 画像

  ┌─────────────────────────────────┐
  │                                 │
  │    🏆 AI 架构师                  │
  │                                 │
  │    D ████████░░ 78              │
  │    A ██████░░░░ 62              │
  │    M █████████░ 85              │
  │    C ██████░░░░ 65              │
  │                                 │
  │    Overall: 72/100 · Grade A    │
  │    Top 8% of AI-era builders    │
  │                                 │
  │    测测你的 → damc.ai            │
  │                                 │
  └─────────────────────────────────┘

  📋 已复制到剪贴板（纯文本版，可直接粘贴到 Twitter/LinkedIn）
```

同时在 `~/Desktop/` 保存一个 `DAMC-Share-{date}.html` 分享卡页面（1080x1080 正方形布局，适合社交媒体截图），使用报告同款暗色主题。

### Phase 4.8: 团队排行榜（Phase 5 上传前执行）

**在上传前，询问用户是否加入团队：**

```
👥 团队排行榜

  加入团队？输入团队码（如 "ACME-CORP"）或 "跳过"
```

**用户输入路由：**
- 输入团队码（如 "ACME-CORP"）→ 将 `group_code` 加入上传 payload
- "跳过" / "skip" → 不加入团队，正常上传
- 本地模式下跳过此步骤

**加入团队后，API 返回排行榜数据，终端显示：**

```
🏆 ACME-CORP 排行榜（12 人）

  1. 🥇 Jayden   83 分  AI 架构师
  2. 🥈 Alex     76 分  AI 原生创造者
  3. 🥉 Sarah    71 分  效率怪兽
  4.    Mike     68 分  沉睡专家
  5.    Lisa     65 分  未雕璞玉
  ...

  你的排名：第 1 名 / 12 人 🎉

  💡 Jayden 公开了 12 个 skills，点击查看：vibergo.space/damc/team/ACME-CORP

  找到你身边与 Agent 协作最6的人！
```

**团队码规则：**
- 格式：大写字母 + 数字 + 短横线，3-20 字符（如 "ACME-CORP", "UCWS-2026"）
- 首次使用某团队码时自动创建团队
- 同一用户可以加入多个团队（每次扫描选择一个）
- 团队成员的显示名称 = 用户输入的角色 或 自选昵称

**隐私规则：**
- 加入团队仅额外分享：总分 + 画像名 + 显示名称
- 不分享子维度分数、扫描详情
- 用户可选择是否公开已安装的 skill 列表（默认不公开）

### Phase 5: 上传分数 + 生成平台链接（仅同意上传时）

**核心规则：只上传数字，永不上传原文。**

```python
# 仅以下数据会通过 HTTPS POST 到 damc.ai
upload_payload = {
    "scores": {
        "D": 78,                          # 总分
        "A": 62,
        "M": 85,
        "C": 65,
        "subs": {                          # 22 个子维度数字
            "expertise": 82, "methodology": 65, ...
        }
    },
    "archetype": "AI架构师",                # 画像名
    "role": "前端开发",                     # 用户输入
    "mbti": "INTJ",                        # 可选
    "env": {"os": "darwin", "shell": "zsh"}, # 桌面环境
    "scan_summary": {                       # 仅数字摘要
        "totalSkills": 83,
        "customSkills": 5,
        "mcpServers": 8,
        "memoryFiles": 12,
        "claudeMdLines": 150,
        "aiCommits": 45,
        "totalCommits": 200
    },
    "group_code": "ACME-CORP",             # 可选，团队码
    "share_skills": false,                  # 是否公开 skill 列表给团队
    "display_name": "Jayden"               # 团队中的显示名称
}
# ❌ 不上传任何原始内容
```

**API 响应扩展**（当包含 `group_code` 时）：

```json
{
  "token": "aB7xK9",
  "url": "https://vibergo.space/r/aB7xK9",
  "team": {
    "code": "ACME-CORP",
    "rank": 1,
    "total_members": 12,
    "leaderboard": [
      { "name": "Jayden", "score": 83, "archetype": "AI架构师" },
      { "name": "Alex", "score": 76, "archetype": "AI原生创造者" }
    ],
    "team_url": "https://vibergo.space/damc/team/ACME-CORP"
  }
}
```

接收 `{ token, url }` 响应后，在终端显示：

```
📊 你的 Agent 体检 · 部分结果

  D ████████░░ 78  M █████████░ 85
  A ██████░░░░ 62  C ██████░░░░ 65

  画像：🏆 AI 架构师

  ⚠️  Top 风险：你的 [跨域思维] 评分较低

  🔓 解锁完整分析（22 子维度 + 可蒸馏清单 +
     护城河识别 + 90 天行动路径）：

  https://damc.ai/r/{token}

  📄 本地 LITE 版已保存：~/Desktop/DAMC-Report-{date}.html
```

**API 端点（MVP 阶段使用 vibergo.space 域名）：**
```
POST https://vibergo.space/api/scan
Content-Type: application/json
Body: <upload_payload 见上>
Response: { "token": "aB7xK9", "url": "https://vibergo.space/r/aB7xK9" }
```

如果 API 调用失败（网络/服务不可用），降级为本地模式：
- 终端只显示 LITE 摘要
- 告知用户：「平台暂不可达，已生成本地报告」

**`window.DAMC_DATA` 结构：**

```javascript
window.DAMC_DATA = {
  userName: "用户角色",
  date: "2026-04-08",
  mbti: "INTJ", // 或 null
  archetype: { name: "AI架构师", emoji: "🏆", tagline: "一句话定位" },
  overall: 72,
  scores: {
    D: { total: 78, subs: { expertise: 82, methodology: 65, codifiability: 88, standardization: 72, demand: 83 } },
    A: { total: 62, subs: { creativity: 70, eq: 55, crossDomain: 68, ambiguity: 60, physical: 40, trust: 75 } },
    M: { total: 85, subs: { environment: 18, skills: 22, automation: 16, memory: 12, advanced: 17 } },
    C: { total: 65, fit: "AI-Augmented Expert", paths: ["推荐路径1", "推荐路径2"] }
  },
  percentiles: {
    D: 92,        // 百分位排名，基于全球基准分布计算
    A: 69,        // 参见 scoring-framework.md 百分位计算方法
    M: 98,
    C: 83,
    overall: 91
  },
  insights: {
    distillTargets: ["你的 SEO 内容工作流可蒸馏为自动化 Skill", "代码审查方法论可编码为 review checklist"],
    moats: ["跨领域整合能力（技术+内容+营销三栖）", "社区信任资产和人脉网络"],
    risks: ["跨域思维评分偏低，可能限制战略性角色转型"],
    actions: ["每周学习一个非本专业领域的 AI 用例", "把你最高频的 3 个工作流蒸馏为 Claude Code skill", "在社区中建立 AI 导师身份，强化信任资产"]
  },
  scanSummary: {
    totalSkills: 83,
    customSkills: 5,
    hooksCount: 3,
    mcpServers: 8,
    memoryFiles: 12,
    claudeMdLines: 150,
    aiCommits: 45,
    totalCommits: 200
  },

  // ===== 新增字段 =====

  // 进化追踪（仅当有历史数据时）
  history: {
    previous: {                          // 上次扫描数据
      date: "2026-05-10",
      overall: 82,
      D: 83, A: 76, M: 89, C: 81
    },
    deltas: {                            // 与上次的差值
      overall: +1, D: 0, A: -1, M: +3, C: +2
    },
    scanCount: 3,                        // 第几次扫描
    allScans: [                          // 所有历史扫描（用于趋势图）
      { date: "2026-04-15", overall: 78, D: 80, A: 74, M: 82, C: 76 },
      { date: "2026-05-10", overall: 82, D: 83, A: 76, M: 89, C: 81 },
      { date: "2026-05-25", overall: 83, D: 83, A: 75, M: 92, C: 83 }
    ]
  },  // 首次扫描时为 null

  // 精准推荐
  recommendations: [
    {
      subDimension: "身体在场",          // 子维度名称
      score: 45,                         // 当前得分
      maxScore: 100,                     // 满分
      dimension: "A",                    // 所属维度
      diagnosis: "远程工作削弱了人际护城河",
      actions: ["每月参加 1 次线下技术社区活动"],
      skills: ["meetup-finder", "event-scheduler"],
      commands: []                       // 可直接执行的命令
    },
    {
      subDimension: "记忆系统",
      score: 13,
      maxScore: 25,
      dimension: "M",
      diagnosis: "memory 类型只有 3 种，覆盖面不足",
      actions: ["添加 project 和 reference 类型记忆"],
      skills: [],
      commands: ["claude memory add --type project \"...\""]
    }
  ],

  // 团队排行榜（仅当用户加入团队时）
  team: {
    code: "ACME-CORP",                   // 团队码
    rank: 1,                             // 用户排名
    totalMembers: 12,                    // 团队总人数
    leaderboard: [                       // 排行榜（最多前 10 名）
      { name: "Jayden", score: 83, archetype: "AI 架构师", isCurrentUser: true },
      { name: "Alex", score: 76, archetype: "AI 原生创造者", isCurrentUser: false },
      { name: "Sarah", score: 71, archetype: "效率怪兽", isCurrentUser: false }
    ],
    url: "https://vibergo.space/damc/team/ACME-CORP"
  }  // 未加入团队时为 null
};
```

## 输出规范

### 在终端显示的完整摘要

生成报告后，在终端按以下顺序输出：

**第一部分：分数概览**

```
📊 DAMC 评估完成

  D 蒸馏价值  ████████░░  78
  A 抗蒸馏    ██████░░░░  62
  M AI驾驭   █████████░  85
  C 职业适配  ██████░░░░  65

  画像：🏆 AI架构师
  "你有值得蒸馏的深度经验，且善用 AI 放大自己的价值"
```

**第二部分：进化追踪**（仅当有历史数据时显示）

```
📈 进化追踪（vs 上次扫描 2026-05-10）

  D 蒸馏价值  83 → 83  ↔️ 持平
  A 抗蒸馏    76 → 75  ⬇️ -1
  M AI驾驭   89 → 92  ⬆️ +3
  C 职业适配  81 → 83  ⬆️ +2

  Overall: 82 → 83 ⬆️

  📝 这是你的第 3 次扫描，M 维度提升最显著（+10 since first scan）
```

首次扫描时显示：
```
  📝 首次扫描，已建立基线。下次运行 /damc 将显示进化追踪。
```

**第三部分：精准推荐**

```
🎯 精准提升建议（基于你的最弱子维度）

  📉 身体在场 (45/100) — A 维度最大短板
     → 建议：每月参加 1 次线下技术社区活动
     → 尝试安装 skill: meetup-finder, event-scheduler

  📉 记忆系统 (13/25) — M 维度可提升项
     → 你的 memory 类型只有 3 种，建议添加 project 和 reference 类型
     → 运行: claude memory add --type project "..."

  📉 标准化 (72/100) — D 维度可提升项
     → 你有 58 个项目但项目级 CLAUDE.md 较少
     → 建议为 top 5 常用项目添加专属 CLAUDE.md

  💡 90 天后再次运行 /damc，追踪你的进化轨迹！
```

**第四部分：团队排行榜**（仅当用户加入团队时显示）

```
🏆 ACME-CORP 排行榜（12 人）

  1. 🥇 Jayden   83 分  AI 架构师
  2. 🥈 Alex     76 分  AI 原生创造者
  3. 🥉 Sarah    71 分  效率怪兽
  ...

  你的排名：第 1 名 / 12 人 🎉

  🔗 团队页面：vibergo.space/damc/team/ACME-CORP
  找到你身边与 Agent 协作最6的人！
```

**第五部分：报告链接**

```
  📄 完整报告已保存至：~/Desktop/DAMC-Report-2026-04-08.html
     用浏览器打开即可查看和分享
```

### 报告输出路径

- 默认保存到 `~/Desktop/DAMC-Report-{YYYY-MM-DD}.html`
- 如果用户指定路径则按用户指定
- 历史数据保存到 `~/.claude/damc-history/scan-{YYYY-MM-DD}.json`

## 评分时的注意事项

- M 维度的评分必须基于实际扫描到的数据，不要凭感觉打分
- D 和 A 维度中基于推断的部分，要在报告中标注"基于角色推断"
- 整体评分 = D×0.25 + A×0.30 + M×0.25 + C×0.20
- A 维度权重最高，因为抗蒸馏能力是 AI 时代最核心的竞争力
