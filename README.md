<p align="center">
  <img src="https://img.shields.io/badge/skills-19-blue?style=flat-square" alt="19 skills">
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="MIT">
  <img src="https://img.shields.io/badge/平台-无关-purple?style=flat-square" alt="平台无关">
  <img src="https://img.shields.io/badge/语言-中文-red?style=flat-square" alt="中文">
</p>

<h1 align="center">Content Creator Pipeline</h1>
<h3 align="center">内容创作者工作流整合包</h3>

<p align="center">
  <b>19 个技能。一个文件夹。零平台锁定。</b><br>
  让你的 AI Agent 从灵感捕获到校准发布，全流程自动化。
</p>

<br>

```
                        ╔══════════════════════════════════════════════════════╗
                        ║           CONTENT CREATOR PIPELINE  v1.7            ║
                        ╚══════════════════════════╤═══════════════════════════╝
                                                   │
    ┌──────────┐    ┌──────────┐    ┌──────────┐    │    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │  ◉ 火花  │    │  ✎ 深挖  │    │  ◈ 评估  │    │    │  ◇ 预测  │    │  ▶ 拍摄  │    │  ✦ 发布  │
    │  SPARK   │───▶│  SEED    │───▶│  SCORE   │───┴───▶│ PREDICT  │───▶│  SHOOT   │───▶│ PUBLISH  │
    │  灵感固化 │    │  写稿造肉 │    │  盲打评分 │         │  落盘预测 │    │  登记拍摄 │    │  登记发布 │
    └────┬─────┘    └────┬─────┘    └────┬─────┘         └──────────┘    └──────────┘    └────┬─────┘
         │               │               │                                                   │
         ▼               ▼               ▼                                                   ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐                                        ┌──────────┐
    │  ☰ 灵感库 │    │  ◌ 润色   │    │  ⊙ 隔离   │                                        │  ◉ 复盘   │
    │  INBOX   │    │ humanizer │    │  blind    │                                        │  RETRO   │
    │  选题候选池 │    │  去 AI 味 │    │  sub-agent│                                        │  T+N 回收  │
    └──────────┘    └──────────┘    └────┬─────┘                                        └────┬─────┘
                                         │                                                   │
                                    ┌────┴─────┐                                        ┌────┴──────────┐
                                    │  ◈ 优化   │                                        │  ◉ 校准升级     │
                                    │ dbs-hook │                                        │  CALIBRATE     │
                                    │  开头钩子 │                                        │  ┌───────────┐ │
                                    └──────────┘                                        │  │ cheat-bump│ │
                                                                                        │  │ rubric升级│ │
                                                                                        │  ├───────────┤ │
                                                                                        │  │  persona  │ │
                                                                                        │  │  受众画像  │ │
                                                                                        │  ├───────────┤ │
                                                                                        │  │ recommend │ │
                                                                                        │  │  选题推荐  │ │
                                                                                        │  └───────────┘ │
                                                                                        └────────────────┘

      灵感采集              创作生产              评估打分              预测发布              复盘校准
```

<br>

---

## 这是什么

一个**自包含的 AI Agent 技能包**，把内容创作的全流程变成 AI 可执行的标准化工作流。

没有外部依赖。不绑定任何平台。复制文件夹，你的 AI 就能：

- 把模糊的灵感变成可追溯、可打分的选题库
- 用隔离盲打分机制做客观内容评估（Agent 不能自己给自己打分）
- 在发布前写 immutable 预测日志，发布后做预测 vs 实绩复盘
- 通过校准循环持续进化评分体系，让每一次发布都变成下一次更好的数据

**适用 AI 框架：** Hermes Agent · Claude Code · Codex · Cline · 任何能读 markdown skill 文件的 AI

<br>

---

## 快速开始

```bash
# 1. 克隆
git clone https://github.com/SamuelKwj/content-creator-pipeline.git

# 2. 安装（Hermes Agent）
cp -r content-creator-pipeline/ ~/.hermes/skills/creative/

# 3. 在 AI 对话中说：
"初始化"
```

搞定。AI 会自动加载完整工作流并引导你完成设置。

**不用 Hermes？** 把文件夹复制到你的项目根目录。AI 读 `SKILL.md` → 理解完整流程 → 按需从 `skill/` 加载子技能。

<br>

---

## AI Agent 使用指南

> ⚠️ 这一节是写给 **AI Agent 自己** 的。人类用户请跳到下一节。

### 首次加载

1. 读 `SKILL.md` —— 这是主流程文档
2. 如果用户还没初始化：加载 `skill/cheat-init/SKILL.md`，引导完成脚手架搭建
3. **必须问：「你做什么类型的内容？」** —— 永远不要假设

### 会话中触发词路由

| 用户说 | 加载哪个 skill | 做什么 |
|--------|--------------|--------|
| 「我有个想法」「灵感」「火花」 | `cheat-spark` | 给 3-4 个标题选项 → 用户选 → 盲打分 → 写入 inbox |
| 「我要口述」「我说你记」 | `cheat-spark` | 进入口述采集模式：只转录暂存，不归档 |
| 「说完了」「完事了，整理」 | `cheat-spark` | 整理所有暂存片段为完整文章 |
| 「归入档案」 | `cheat-spark` | 将口述整理结果正式收录为火花 |
| 「我想做一条关于X的」 | `cheat-seed` | 引导式深挖对话 → 出 draft 大纲 |
| 「打分这篇 [路径]」 | `cheat-score` | 委派给盲打分 sub-agent → 展示分数 |
| 「启动预测 [路径]」 | `cheat-predict` | 完整预测流程 → immutable 落盘 |
| 「拍了 [路径]」 | `cheat-shoot` | 建 video folder，登记拍摄 |
| 「已发布 [链接]」 | `cheat-publish` | 写 URL + 时间戳到预测文件 |
| 「复盘 [路径]」 | `cheat-retro` | 回收数据 → 预测 vs 实绩对比 |
| 「状态」「看板」 | `cheat-status` | 展示校准进度 / buffer / 待复盘 |
| 「升级 rubric」 | `cheat-bump` | 校准池重打分 → 进化评分维度 |
| 「优化开头」 | `dbs-hook` | 诊断开头问题 → 生成优化方案 |
| 「去 AI 味」 | `humanizer` | 剥离 AI 写作痕迹 |
| 「配话题」「发布前话题」 | `douyin-hashtag-advisor` | 分析标题/脚本 → 生成抖音话题包 |

### 铁律（不可违反）

1. **盲打分隔离。** 绝对不在主对话中 inline 打分。所有评分必须通过 `delegate_task` 委派给 `cheat-score-blind` sub-agent。
2. **预测不可变。** 预测段一旦落盘不能修改。复盘只能追加到复盘段。
3. **不替创作者写完整稿。** AI 只出大纲和评分骨架。创作者自己写终稿。
4. **灵感必须过标题选项。** 收到灵感后必须先给 3-4 个标题角度让用户选，不能不选直接收录。
5. **不假设内容形态。** 首次对话必须问用户做什么类型的内容。

### AI 管理的项目结构

```
<项目根>/
├── .cheat-state.json          # 全局状态（AI 读写）
├── rubric_notes.md            # 评分维度定义
├── scripts/                   # 拍前草稿
├── predictions/               # 不可变预测日志
├── videos/                    # 拍后工作目录
├── archive/
│   ├── inbox.jsonl            # 已打分灵感库
│   └── good-articles/         # 好文收藏
└── candidates.md              # 选题候选池
```

<br>

---

## 技能清单

### 核心流程（16 个，基于 cheat-on-content 体系）

| # | 技能 | 阶段 | 做什么 |
|---|------|------|--------|
| 1 | `cheat-spark` | 💡 火花固化 | 灵感捕获 → 标题提炼 → 盲打分 → inbox |
| 2 | `cheat-seed` | ✍️ 选题深挖 | 引导式对话 → 写 draft 大纲 |
| 3 | `cheat-score` | 🎯 盲打分 | 调度 sub-agent 出分，主 Agent 不自己打分 |
| 4 | `cheat-score-blind` | 🎯 隔离打分 | sub-agent 隔离通道：只读脚本+rubric，不看任何上下文 |
| 5 | `cheat-predict` | 🔮 预测 | 盲打分 → 锚点对比 → bucket → 反事实 → immutable 落盘 |
| 6 | `cheat-shoot` | 🎬 拍摄 | 建 video folder，登记拍摄 |
| 7 | `cheat-publish` | 🚀 发布 | 写 URL + 平台 + 时间戳 |
| 8 | `cheat-retro` | 📊 复盘 | T+N 数据回收 → 预测 vs 实绩 |
| 9 | `cheat-bump` | 🔄 校准 | 校准池重打分 → 进化 rubric 维度 |
| 10 | `cheat-persona` | 🔄 校准 | 从评论数据派生受众画像 |
| 11 | `cheat-recommend` | 🔄 校准 | 按 rubric 排序推荐选题 |
| 12 | `cheat-trends` | 🔄 校准 | 抓热点 → 粗打分 → 写入候选池 |
| 13 | `cheat-status` | 📋 监控 | 状态看板：进度 / buffer / 待复盘 |
| 14 | `cheat-learn-from` | 📋 初始化 | 导入对标账号 → 拆解 pattern |
| 15 | `cheat-migrate` | 📋 初始化 | 升级 .cheat-state.json schema |
| 16 | `cheat-init` | 📋 初始化 | 首次项目脚手架搭建 |

### 辅助工具（3 个）

| # | 技能 | 做什么 | 来源 |
|---|------|--------|------|
| 17 | `humanizer` | 去 AI 味，恢复真人语气 | [blader/humanizer](https://github.com/blader/humanizer) |
| 18 | `dbs-hook` | 短视频开头诊断与优化 | dontbesilent 商业工具箱 |
| 19 | `douyin-hashtag-advisor` | 抖音发布前话题推荐 | Hermes 内置 skill |

<br>

---

## 法律声明与来源归属

本包是**整合包**（integrated bundle），以快照形式打包了以下上游来源的子技能。

### cheat-on-content 体系（16 个技能）

- **来源：** [原地址和原作者在此](https://github.com/XBuilderLAB/cheat-on-content)
- **说明：** 这 16 个 cheat-* 技能构成本包的核心流程。每个技能的快照独立存放于 `skill/cheat-*/SKILL.md`。修改请直接改对应文件。

### humanizer（去 AI 味）

- **来源：** [github.com/blader/humanizer](https://github.com/blader/humanizer)
- **作者：** Siqi Chen ([@blader](https://github.com/blader))
- **许可：** MIT
- **本包路径：** `skill/humanizer/`
- **说明：** 原始项目以 MIT 协议开源，本包保留完整许可文件（`skill/humanizer/LICENSE`）和原始署名。未做任何修改。

### dbs-hook（短视频开头优化）

- **来源：** dontbesilent 商业工具箱
- **许可：** dontbesilent 体系内部工具。本包以快照形式包含该技能用于工作流完整性。如果你是该技能的原始作者且不希望它被包含在此整合包中，请提交 issue 或 PR，我们会立即移除。
- **本包路径：** `skill/dbs-hook/SKILL.md`

### douyin-hashtag-advisor（抖音话题推荐）

- **来源：** Hermes 内置 skill: content-creator/douyin-hashtag-advisor
- **作者：** KK + Hermes
- **许可：** MIT
- **本包路径：** `skill/douyin-hashtag-advisor/SKILL.md`
- **说明：** 分析标题/脚本/账号定位，生成抖音发布前话题推荐包。包含来源可信度标记、风险标记、重复控制、可直接复制的发布版本。

### 总许可

本整合包整体以 **MIT** 协议发布（见仓库根目录 `LICENSE` 文件）。各子技能可能带有不同的原始许可，详见上述声明及各子技能目录下的具体文件。

<br>

---

## 依赖

| 依赖 | 说明 | 必须？ |
|------|------|--------|
| AI Agent（支持 tool calling） | Hermes / Claude Code / Codex / Cline 等 | ✅ 必须 |
| LLM API Key（OpenAI 兼容） | 打分、格式化、分析等技能需要 | ✅ 必须 |
| 数据回收 adapter | 自动获取播放/评论数据用于复盘 | ❌ 可选 |

<br>

---

## 设计哲学

> **「厌蠢是赚钱的敌人 —— 先发再优化」**

这套工具的核心洞察：大多数创作者失败不是因为内容差，而是缺少**校准循环**。发了就发了，凭感觉判断好坏，下一次还是凭感觉。

本工具用 **打分 → 预测 → 复盘 → 进化** 的循环替代凭感觉：

- 每篇内容变成数据点
- 每个数据点打磨评分体系
- 每次打磨让下一篇预测更准

不是为了完美。是为了**可衡量地、持续地变得更好**。

<br>

---

## License

MIT · [github.com/SamuelKwj/content-creator-pipeline](https://github.com/SamuelKwj/content-creator-pipeline)

---

## 作者

Created by **请叫我奉孝大人**

- GitHub: [@SamuelKwj](https://github.com/SamuelKwj)
- 抖音: `52168570433`
