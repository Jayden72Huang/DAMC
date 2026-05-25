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

**扫描以下数据源，静默执行，不需要用户参与：**

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

### Phase 4: 生成可视化报告（本地 LITE 版）

1. 读取 `templates/report.html` 模板
2. 将评分数据填入模板中的 `window.DAMC_DATA` 对象
3. 将 LITE 版 HTML 保存到 `~/Desktop/DAMC-Report-{YYYY-MM-DD}.html`
   - LITE 版只含 4 维总分和画像，不含子维度详情、可蒸馏清单、护城河识别、行动建议
4. 完整版需在 damc.ai 平台解锁（见 Phase 5）

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
    }
}
# ❌ 不上传任何原始内容
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
  insights: {
    distillTargets: ["值得蒸馏的能力1", "值得蒸馏的能力2"],
    moats: ["护城河1", "护城河2"],
    risks: ["风险点1"],
    actions: ["行动建议1", "行动建议2", "行动建议3"]
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
  }
};
```

## 输出规范

### 在终端显示的摘要

生成报告后，在终端输出简短摘要：

```
📊 DAMC 评估完成

  D 蒸馏价值  ████████░░  78
  A 抗蒸馏    ██████░░░░  62
  M AI驾驭   █████████░  85
  C 职业适配  ██████░░░░  65

  画像：🏆 AI架构师
  "你有值得蒸馏的深度经验，且善用 AI 放大自己的价值"

  📄 完整报告已保存至：~/Desktop/DAMC-Report-2026-04-08.html
     用浏览器打开即可查看和分享
```

### 报告输出路径

- 默认保存到 `~/Desktop/DAMC-Report-{YYYY-MM-DD}.html`
- 如果用户指定路径则按用户指定

## 评分时的注意事项

- M 维度的评分必须基于实际扫描到的数据，不要凭感觉打分
- D 和 A 维度中基于推断的部分，要在报告中标注"基于角色推断"
- 整体评分 = D×0.25 + A×0.30 + M×0.25 + C×0.20
- A 维度权重最高，因为抗蒸馏能力是 AI 时代最核心的竞争力
