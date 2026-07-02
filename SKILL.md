---
name: content-creator-pipeline
description: |
  通配内容创作者工作流 skill。适用于任何内容形态（观点视频/口播/长文/播客等）。
  完整闭环：火花→深挖写稿→盲打分→预测→拍摄→发布→复盘→校准升级。
  启动时自动加载所有依赖 skill，新 AI 读此文件即知完整流程 + 所有子 skill 依赖。
  依赖清单见 references/dependencies.md。
version: 1.6.0
author: Hermes Agent (整合包维护者)
license: MIT
metadata:
  hermes:
    tags: [content, creator, pipeline, workflow, 自媒体, 内容创作, generic, integrated]
    related_skills:
      - cheat-seed
      - cheat-score
      - cheat-score-blind
      - cheat-predict
      - cheat-shoot
      - cheat-publish
      - cheat-retro
      - cheat-bump
      - cheat-persona
      - cheat-recommend
      - cheat-trends
      - cheat-status
      - cheat-learn-from
      - cheat-migrate
      - cheat-init
      - humanizer
      - dbs-hook
      - hermes-agent-skill-authoring
---

# Content Creator Pipeline — 内容创作全流程

> 通用创作流水线，适用于任何内容形态（观点视频/口播/长文/播客）。
> 加载即知完整流程，自动调用对应子 skill，闭环从灵感发布到复盘校准。

---

## 子 Skill 来源声明

本包是整合包，所有子 skill 快照拷贝于此。修改请直接改本目录下对应文件，保留原许可证即可。

### cheat-on-content 体系（16 个 — 核心流程）

| 子 skill | 所属体系 | 上游来源 | 说明 |
|----------|---------|---------|------|
| `cheat-spark` | cheat-on-content | Hermes skills: creative/cheat-on-content | 火花固化：灵感捕获 → 标题提炼 → 盲打分 → inbox |
| `cheat-seed` | cheat-on-content | Hermes skills: creative/cheat-on-content | 选题深挖+写draft |
| `cheat-score` | cheat-on-content | 同上 | 盲打分调度（主 agent） |
| `cheat-score-blind` | cheat-on-content | 同上 | 盲打分 sub-agent（隔离通道） |
| `cheat-predict` | cheat-on-content | 同上 | immutable 预测日志 |
| `cheat-shoot` | cheat-on-content | 同上 | 拍摄登记 |
| `cheat-publish` | cheat-on-content | 同上 | 发布登记 |
| `cheat-retro` | cheat-on-content | 同上 | T+N 复盘 |
| `cheat-bump` | cheat-on-content | 同上 | rubric 升级 |
| `cheat-persona` | cheat-on-content | 同上 | 受众画像 |
| `cheat-recommend` | cheat-on-content | 同上 | 选题推荐 |
| `cheat-trends` | cheat-on-content | 同上 | 热点抓取 |
| `cheat-status` | cheat-on-content | 同上 | 状态看板 |
| `cheat-learn-from` | cheat-on-content | 同上 | 对标导入 |
| `cheat-migrate` | cheat-on-content | 同上 | schema 迁移 |
| `cheat-init` | cheat-on-content | 同上 | 首次初始化 |

### 外部独立项目（2 个 — 辅助）

| 子 skill | 上游来源 | 许可证 | 说明 |
|----------|---------|--------|------|
| `humanizer` | [github.com/blader/humanizer](https://github.com/blader/humanizer) | MIT | 去 AI 味 |
| `dbs-hook` | Hermes built-in: creative/dbs-hook | MIT | 短视频开头优化 |

### 已声明但非子 skill（1 个）

| 名称 | 说明 |
|------|------|
| `hermes-agent-skill-authoring` | Hermes 系统内置 skill（software-development/），不在 pipeline/skill/ 下，不在此打包 |

---

## 零、需求分类（First Decision）— 不一路走到黑

**在任何生产动作之前，先判断用户真正要什么。** 不要用户问一句"帮我看个选题"就自动跑完整条流水线。

### 分类规则

| 用户的话 | 属于 | AI 怎么做 |
|---------|------|----------|
| "帮我看看这个选题值不值得做" / "这个角度行不行" | **选题验证** | 只做分析，不写稿不做卡 |
| "帮我做条视频" / "怎么做" 但没说要什么 | **交付物选择** | 出示下面的交付物菜单，让用户挑 |
| "给我写个标题" / "做个金句卡" / "优化开头" | **按需生产** | 只产指定的东西，不多做 |
| "从这篇文章做成视频全套物料" / "完整做一条" | **完整生产** | 走完整 8 阶段流程 |
| "复盘" / "帮我看看这条数据" | **复盘** | 只做数据复盘，不走生产 |

### 核心原则

> **不要一条路走到黑。** 用户问"这个选题怎么样"就只分析选题，不要顺手写 full script 再问要不要做。等用户说了"好，做"再往下走。

### 口述采集模式（Voice Dictation Capture）

当用户说类似："我要口述一篇文章"、"我会一直发语音"、"你先识别，最后我说‘说完了’再整理"：

1. **立即进入采集模式**，不要把第一句当成正式灵感收录，也不要归档、打分、总结。
2. 每条后续语音/文字只做：转录 → 去水词 → 暂存片段编号。
3. 输出要短，只回 `已暂存片段 N` + 去水词后的片段；不要通篇整理、不要改观点、不要升华。
4. 如果用户纠正"接下来，不是记下来"，表示他要启动未来采集流程，不是让你把这句写进档案。
5. 只有用户明确说 **"说完了" / "完事了，整理"**，才把所有片段通篇整理成文章。
6. 整理完成后仍不要自动收录为火花；只有用户再说 **"归入档案" / "这就是该火花文章"** 才进入归档流程。


### 交付物菜单

当用户没明确说要什么时，出示这个菜单：

```
1. 选题分析（推荐指数A/B/C + 受众/痛点/风险/角度）
2. 标题/封面句
3. 短文案/发布文案
4. 口播脚本
5. 分镜脚本
6. 横版 overlay 卡片（露脸同屏）
7. 全屏切卡（9:16 cutaway，2-4秒）
8. 动态 HTML 示意
9. 视频剪辑方案
10. 成片剪辑/导出
11. 发布文案（标题+简介+话题+评论区引导）
12. 金句卡轮播视频（卡片+配音+字幕，无人出镜）
13. 发布后复盘
14. 全套物料（以上全部）
```

用户选多项 → 只产选中的项。

### 选题验证模板

纯选题分析时的输出格式：

```
选题：
一句话判断：
推荐指数：A / B / C / 放弃
适合人设：
目标受众：
核心痛点：
爆点来源：
潜在风险：
推荐切入角度：
前 3 秒钩子：
评论区引导：
```

---

## 一、快速开始（新创作者初始化）

1. 运行 `cheat-init` — 创建项目脚手架（.cheat-state.json / rubric_notes.md / scripts/ / predictions/ / videos/ 等）
2. 可选：运行 `cheat-learn-from` — 导入对标账号建立基准
3. 开始创作：按下面的阶段走

> **已有项目：** 直接进阶段 1。

**首次对话必须问用户："你做什么类型的内容？"** — 不要假设默认是观点视频。

---

## 二、依赖 skill 总表

所有子 skill 清单见 `references/dependencies.md`。
每个子 skill 对应一个具体的生产环节。

**核心依赖（必须安装）：**

| skill | 作用 | 优先级 |
|-------|------|--------|
| cheat-init | 初始化脚手架 | 首次必须 |
| cheat-spark | 火花固化：灵感→标题→盲打分→inbox | 每次有灵感 |
| cheat-seed | 选题深挖→写draft | 高频 |
| cheat-score | 盲打分（调用 sub-agent） | 每次打分 |
| cheat-score-blind | 盲打分 sub-agent | 被 cheat-score/predict 调用 |
| cheat-predict | 写 immutable 预测日志 | 每篇发布前 |
| cheat-shoot | 登记拍摄 | 每拍一次 |
| cheat-publish | 登记发布 | 每发一次 |
| cheat-retro | T+3 复盘 | 每篇发布后 |
| cheat-status | 状态看板 | 随时 |

**扩展依赖（可选，后期用）：**

| skill | 作用 | 什么时候装 |
|-------|------|-----------|
| cheat-bump | rubric 升级（v0→v1→v2） | 累积 ≥5 篇复盘后 |
| cheat-persona | 从评论派生受众画像 | 有几篇复盘数据后 |
| cheat-recommend | 从候选池推荐选题 | 候选池有内容时 |
| cheat-trends | 抓热点 | 没选题时 |
| cheat-learn-from | 导入对标账号 | 想找 benchmark 时 |
| cheat-migrate | 升级 state schema | cheat-on-content 更新时 |
| humanizer | 去 AI 味 | cheat-seed 写 draft 后自动调 |
| dbs-hook | 短视频开头优化 | 优化钩子时 |

---

## 三、完整业务流程

```
【阶段 0】火花固化（日常输入）
    │  用户给灵感 → AI 给 3-4 标题选项 → 用户选/改 → 盲打分 → 收录
    │  涉及 skill: cheat-spark（火花固化：灵感捕获 → 标题提炼 → 盲打分 → inbox.jsonl）
    │  口述采集模式也在 cheat-spark 内处理
    ▼
【阶段 1】选题深挖 → 写稿
    │  用户说"我想做一条关于X的" → cheat-seed → 深挖角度 → 写 draft → humanizer 去 AI 味
    │  → 用户改写（脚手架警告）
    │  涉及 skill: cheat-seed, humanizer
    ▼
【阶段 2】盲打分
    │  用户说"打分这篇 scripts/xxx.md" → cheat-score
    │  → delegate_task 调 cheat-score-blind sub-agent → 展示 7 维分 + composite
    │  涉及 skill: cheat-score → cheat-score-blind
    │  ⚠️ 铁律：所有评分必须盲打，主 AI 绝不 inline 打分
    ▼
【阶段 3】预测
    │  用户说"启动预测 scripts/xxx.md" → cheat-predict
    │  → blind check → 盲打分 → 锚点对比 → bucket → 反事实 → 用户 review → immutable 落盘
    │  涉及 skill: cheat-predict → cheat-score-blind
    │  ⚠️ 铁律：预测段 immutable，发布后不能接受播放数据
    ▼
【阶段 4】拍摄登记
    │  用户说"拍了 scripts/xxx.md" → cheat-shoot → 建 video folder
    │  涉及 skill: cheat-shoot
    ▼
【阶段 5】发布登记
    │  用户说"已发布 https://..." → cheat-publish → 更新 header + state
    │  涉及 skill: cheat-publish
    ▼
【阶段 6】复盘
    │  用户说"复盘 predictions/xxx.md" → cheat-retro → 回收数据 → 预测 vs 实绩 → 写观察
    │  涉及 skill: cheat-retro
    ▼
【阶段 7】长期校准（N≥5 解锁）
    │  升级 rubric → cheat-bump
    │  更新受众画像 → cheat-persona
    │  推荐选题 → cheat-recommend
    │  抓热点 → cheat-trends
    │  导入对标 → cheat-learn-from
```

---

## 四、路由表（触发词 → 子 skill）

| 用户说/想做 | 调用 | 说明 |
|-----------|------|------|
| "初始化"/"init" | cheat-init | 首次使用，建脚手架 |
| "我有个想法"/"灵感"/"火花"/"记下来"/"归入档案" | cheat-spark | 火花固化：标题提炼 → 盲打分 → inbox.jsonl |
| "我要口述"/"我说你记"/"说完了"/"完事了，整理" | cheat-spark | 口述采集+整理 |
| "我想做一条关于X的"/"seed" | cheat-seed | 深挖选题→写 draft |
| "打分这篇 [path]" | cheat-score | 盲打分（控制台输出） |
| "启动预测 [path]" | cheat-predict | 完整预测流程→落盘 immutable |
| "拍了 [path]" | cheat-shoot | 建 video folder |
| "已发布 [url]" | cheat-publish | 登记发布 |
| "复盘 [path]" | cheat-retro | T+3 数据复盘 |
| "状态" | cheat-status | 状态看板 |
| "抓热点" | cheat-trends | 抓热点→粗打分 |
| "推荐选题" | cheat-recommend | 候选池排序推荐 |
| "升级 rubric" | cheat-bump | 校准池重打→升级公式 |
| "更新受众画像" | cheat-persona | 从评论派生画像 |
| "学这个账号" | cheat-learn-from | 导入对标账号 |
| 金句卡轮播视频 | 见 references/video-card-slideshow.md | 卡片+配音+字幕合成 |
| 优化开头 | dbs-hook | 优化前 3 秒钩子 |
| "迁移" | cheat-migrate | 升级 state schema |
| "做成思维导图"/"画个流程图"/"可视化工作流" | 见 references/workflow-visualization.md | 双轨产出：excalidraw 编辑底稿 + baoyu-infographic 成品图 |
| "收藏好文" / "分析好文" / "归档好文" / "提炼一下" | 好文收藏分析（内置本 pipeline） | 提炼核心观点+金句+结构分析 → 归档到好文收藏库 |

---

## 四加一：好文收藏分析流程（新增）

用户提供一篇外部好文/观点文，要求分析归档 → 走这个流程：

### 触发词
- "提炼一下"
- "分析这篇文章"
- "收藏这篇好文"
- "归档这篇文章"

### 输出要求
必须输出三部分：
1. **核心观点** — 100字以内抓本质，一句话说清楚文章讲了什么
2. **金句摘抄** — 列出原文中适合直接复用、能当标题/口播的句子（每条完整摘出）
3. **文章结构分析** — 拆解作者的写作逻辑，分析为什么抓人，总结可复用的结构pattern

### 归档规则
- 好文原文保存到 `<content-project>/archive/good-articles/` 目录
- 分析结果（核心观点+金句+结构）记录到 `<content-project>/archive/good-articles/index.jsonl`
- 文件名格式：`YYYYMMDD_<slug>_<author>.md`（原文）
- 每条index entry含：`id`、`title`、`author`、`original_path`、`created_at`、`tags`、`core_idea`、`quotes`、`structure_notes`

### Pitfall: JSON 逐行写入转义
- 当修改 `inbox.jsonl` / `index.jsonl` 这类JSONL文件时，不要直接拼接字符串，要先load所有行修正错误再整体写出，避免JSON解析错误。
- 常见错误：一行损坏导致后续整个文件解析失败 — 正确做法是逐行load，修改目标entry，再重新写出所有行。

---

## 五、铁律（不可违反）

### 5.1 盲打分
- **所有评分必须通过 `cheat-score-blind` sub-agent（delegate_task）完成**
- sub-agent 只读 `script + rubric_notes.md`，不读 predictions/ / state / 对话历史
- 主 AI **绝不 inline 打分**（任何维度的 0-5 分都不能在主对话中给出）
- 即使用户说"跳过 blind"也要拒绝——这是校准循环的核心保障
- 违规后果：校准循环退化为凭直觉的自我安慰

### 5.2 预测 immutable
- `## 预测 段`一旦落盘不可修改（由 hook 锁定）
- 复盘只能追加到 `## 复盘` 段
- 发布后，AI 不能接受播放/点赞/评论等实际数据——盲度声明失效
- 如果不小心看到了，必须在预测文件头补 integrity warning

### 5.3 创作者写稿
- AI **只提供大纲、标题建议、评分骨架**
- 完整稿子由创作者自己写（AI 写的稿子缺真实语气/经历/金句）
- draft 开头必须加 ⚠️ 必须改写的醒目警告
- 直接拍 AI draft 会导致 ER 偏低，污染校准数据

### 5.4 火花收录
- 创作者给灵感 → AI 必须给 3-4 个标题选项 → 创作者选/改 → 再收录
- 不跳过选项直接收录
- 收录后走盲打分，分写进 inbox.jsonl
- **系列/栏目/长期企划不当普通火花处理**：写成系列企划文档，归档到 `archive/<series-name>_系列企划.md`，并写入 `archive/topics.jsonl`；只有单条可拍内容才进 `archive/inbox.jsonl`

### 5.5 基于 skill 工作
- 能用 skill 的路径必须用 skill
- 打分走 `cheat-score`、预测走 `cheat-predict`、不复盘不升级 rubric
- AI 不跳过 skill 定义的任何阶段

### 5.6 不硬编码创作者身份
- **本 skill 是通用 pipeline**，不假设使用者是某种创作者
- 不要硬编码内容形态（观点视频/口播）、赛道（AI+社会/职场）、平台（抖音/YouTube）
- 所有系统提示词、seed 写稿提示、路由判断中使用的创作者信息，必须从配置或 `.cheat-state.json` 读取
- 当用户新开项目或首次使用时，必须先问"你做什么类型的内容？"再继续
- 违规后果：用户收到"默认我是 AI 博主"的困扰，需要手动纠偏

### 5.7 文件名一致性（Critical）
- **当通过 skill 创建文件时（如 cheat-seed 写稿、cheat-predict 落盘），AI 回复中的文件名必须使用 skill 返回的实际文件路径**
- 常见的 bug 模式：AI 在对话回复中自己编了一个文件名（如 "scripts/my-draft.md"），而实际 skill 创建的文件名是系统自动生成的（如 "scripts/2026-06-15_abc123_标题.md"）
- 修复方法：前端/后端在展示 skill result 时用大字突出显示实际路径；AI 在后续对话引用该文件时，使用 result 中的 `rel_path` 或 `filename` 字段，不得自己生成
- 违规后果：用户说"打分这篇"时找不到文件，以为是 skill 没装

### 5.8 状态三统一（Critical）
- 内容状态不得只查单一文件。判断「写完/待拍/已发布/已复盘」时，必须同时核对并同步三块：`archive/inbox.jsonl`、`predictions/*.md`、`.cheat-state.json`
- 发布登记后：`predictions` 写入 URL/平台/发布时间；`inbox.jsonl` 同步 `stage/status`；`.cheat-state.json` 同步 buffer/进度
- 复盘登记后：`predictions` 追加复盘段；`inbox.jsonl` 同步 `stage/status`；`.cheat-state.json` 同步校准样本/待复盘
- 归档已发作品后：若 `archive/<主题>/` 已含终稿、预测日志、成品视频等完整资产，必须把对应 inbox 条目标为 `stage/status=done`（或 published/archived），并从桌面灵感清单的待做区移除；同时清掉指向该作品的 `in_progress_session`。
- 生成任何「待做/未完成/状态」列表前，先排除 `status/stage in done|published|archived` 的条目，并交叉搜索 archive 目录，避免把已发作品重新列为未完成。
- 不要出现「inbox 还标待发，但 predictions 已发布」的信息差。回答状态前先交叉检查三处，必要时先修正再回复


---

## 六、项目目录结构

```
<content-project>/
├── .cheat-state.json         # 状态文件（各 skill 共享上下文）
├── rubric_notes.md           # 评分维度定义
├── rubric-memo.md            # bump 升级 Memo 档案（含实绩，blind sub-agent 禁读）
├── script_patterns.md        # 写作 pattern 沉淀
├── audience.md               # 受众画像（含实绩，blind sub-agent 禁读）
├── benchmark.md              # 对标账号参考
├── AGENTS.md / WORKFLOW.md   # 流程说明（可选的）
│
├── scripts/                  # 拍前草稿（写权限） → YYYY-MM-DD_<id>_<short>.md
├── predictions/              # immutable 预测日志（写权限）→ YYYY-MM-DD_<id>_<short>.md
├── videos/                   # 拍后工作目录（创建权限）→ <id>/script.md, report.md
├── samples/                  # 对标账号样本
├── candidates.md             # 选题候选池
├── archive/                  # 原始火花存档 → inbox.jsonl
│   ├── good-articles/        # 好文收藏分析 → 原文 + index.jsonl
│   ├── topics.jsonl          # 系列/企划类索引（不混入 inbox）
│   └── <series-name>_系列企划.md # 系列企划文档
```

**权限说明：**
- 所有 `scripts/`、`predictions/`、`videos/`、`archive/` 下的文件 AI 有读写权限
- `.cheat-state.json` AI 有读写权限
- 用户需要确保项目目录所在磁盘有写入权限

---

## 七、Session 启动指令

每次新会话（AI 加载本 skill 后），AI 应该：

1. 读 `.cheat-state.json` → 了解当前状态
2. 报告当前进度：校准样本数、待复盘数、最近一次动作
3. 提示可选下一步：打分/预测/拍摄/发布/复盘/状态
4. 根据用户的 content_type/niche/platform 配置调整语气和选题方向
5. 等待用户指令

### 7.1 定时早安 / 断更提醒 Cron 模式

当系统提示当前是 scheduled cron job（无人值守、不能追问）且任务是「今天有稿子要写吗 / 灵感进度提醒 / 断更提醒」时：

1. **只读状态，不主动改文件**：按 `cheat-status` + `cheat-recommend` 的逻辑读取 `.cheat-state.json`、`AGENTS.md`、`candidates.md`、`scripts/`、`predictions/`、`archive/inbox.jsonl`（如存在），不要登记发布/复盘/改状态。
2. **先定位项目根**：优先使用当前目录下的 `.cheat-state.json`；否则在用户 home 下找最近的内容项目根。后续读写一律用项目根绝对路径或 tool 的 `workdir`，不要在持久 terminal 会话里反复 `cd cheat-content`（terminal cwd 会跨调用保留，容易嵌套 cd 失败）。
3. **三统一过滤**：生成「待写/待拍/待发」前，必须交叉检查 `archive/inbox.jsonl`、`predictions/*.md`、`.cheat-state.json`，排除 `done|published|archived` 和已归档完整作品，避免把已发作品重新催一遍。
4. **Buffer 优先级覆盖**：
   - 🔴/🟠 buffer 紧张：先提醒已拍未发的存货，再从候选池只推 top 1 稳分（可附 1 条实验性作为备选，但明确「今天优先稳分」）。
   - 🔵 buffer 积压：不要催写新稿；改为催发布存货 + 复盘。
5. **无人值守输出**：不能问「要不要我帮你」，必须给可执行下一步；如果确实没有待办、无风险、无新信息，最终只回 `[SILENT]`。
6. **不要替用户写完整稿**：cron 提醒只推荐选题/路径/下一步命令；完整稿仍等用户发来或明确要求后再进入 `cheat-seed`/写稿流程。

---

## 八、便携部署方式

本包支持两种部署方式：

### 方式 A：Hermes Agent skill（默认）

把 `content-creator-pipeline/` 整个文件夹复制到 `~/.hermes/skills/creative/` 下，然后 `/reload-skills`。
所有子 skill 在 `skill/` 目录下，开箱即用。

### 方式 B：独立桌面程序（Python + Flask）

另一种部署方式：**不依赖 Hermes Agent**，用 Python + Flask 提供浏览器 UI。

详见 `references/desktop-workbench.md`。

当用户反馈桌面端仍然像“按钮入参式”、不能在闲聊中推进业务流时，参考 `references/desktop-chat-agent-state.md`：需要后端小状态机（profile / collecting_spark / pending_spark）+ 明确的内容创作助理系统提示词 + API级回归测试，而不是只改按钮文案。

当产品化桌面工作台时，参考 `references/desktop-workbench-productization-lessons.md`：覆盖聊天式工作流、会话持久化、评分结果必须展示、提示词外置通用化、左侧栏整合对话管理与火花看板、以及对应回归测试。

所有 skill 实现在 `main.py` 中，通过 OpenAI 兼容 API 调用 LLM。
适合不需要 / 不方便装 Hermes 的用户。

---

## 九、辅助工具

| 需要 | 调什么 |
|------|--------|
| 看当前状态 | cheat-status |
| 金句卡轮播视频 | references/video-card-slideshow.md（Playwright + ffmpeg 合成） |
| 优化开头 | dbs-hook |
| 去 AI 味 | humanizer |
| 做脚本骨架 | cheat-seed（只给大纲模式） |

### 视觉物料规则（卡片类型）

| 类型 | 尺寸 | 用法 | 禁止 |
|------|------|------|------|
| **全屏切卡** (cutaway) | 9:16 竖屏 | 切走画面 2-4 秒，替换露脸画面 | 不要叠加在脸上 |
| **横版 overlay 卡** | 水平长条，下半屏 | 露脸同屏时放在面部下方 | 不要遮挡眼睛/嘴/右下方平台UI |
| **动态 HTML 示意** | 浏览器播放 | 仅用于需要动态解说/演示时 | 不要替代实际视频卡片素材 |


### 完整发布前物料包（Pre-publish Package）

当用户说「完整跑一遍流程」「全部物料」「审稿、预测、发布时间建议、观点卡片」时，视为**完整发布前物料包**，不是单点咨询。默认交付必须是可验证的实际文件，而不是只给建议。

必做顺序：

1. **读取最新版稿件**：以用户给定/当前确认的稿件为准，先读文件，避免基于旧稿。
2. **标题入稿/脚本受管**：若用户已确认标题，先 patch 原稿标题，再复制/生成 `scripts/YYYY-MM-DD_<hash>_<short>.md` 受管脚本。
3. **盲打分 + 预测日志**：打分必须走 `delegate_task` 盲打；预测日志写入 `predictions/`，并更新 `.cheat-state.json.in_progress_session`。
4. **视觉物料**：若用户要求配图/卡片，生成实际 PNG/HTML/PDF，并验证尺寸/可读性。
5. **状态同步**：同步 `archive/inbox.jsonl`、桌面灵感清单、`.cheat-state.json`；状态通常为 `predicted / ready_to_shoot`。
6. **发布前报告**：写一份报告到稿件同级或 archive 目录，列出脚本、预测日志、卡片清单和最终建议。
7. **最终回复**：只汇总实际产物路径 + 关键结论；不要把整份报告完整粘贴到聊天里。

推荐报告文件名：`archive/<文章名>_发布前流程报告.md`。

### 单句加入文章作为金句卡

当用户说「把这句加入 X 文章作为金句卡片」「加到某篇作为金句卡」时：

1. 这属于**按需生产**，不是新火花：不要要求 3-4 个标题，不要收录，不要盲打分，除非用户明确要求。
2. 先定位目标文章/脚本/大纲，优先 patch 到 `金句备选` / `卡片素材` / 对应段落，并保留原句，附用途标记如 `【金句卡片 / 中段转场】`。
3. 可行时直接产出一个 9:16 cutaway 金句卡 HTML，文件名建议 `<文章名>_金句卡_<短名>.html`，放在脚本或文章资产附近。
4. 报告成功前要实际验证 HTML 能渲染；回复里给出文章路径、卡片路径，能截图预览就附媒体。
5. 默认按**全屏切卡 cutaway**处理；只有用户明确说露脸同屏/overlay 时，才做 lower-third 或 side-card。

---

## 十、自包含优先（Packaging Philosophy）

本 skill 遵循**自包含优先**原则——这是来自使用者的明确偏好：

1. **不要只写文档** — 一个 AGENTS.md 告诉 AI "去装 X、Y、Z skill"是不够的。必须把依赖打包在一起，复制即可用。
2. **所有子 skill 打包** — dependencies.md 只是清单，skills/ 目录才是实质。另一个 AI 拿到 skills/ 就知道每个阶段怎么执行。
3. **可以有双重部署** — Hermes skill（本文件）和独立桌面程序（references/desktop-workbench.md）是同一套流程的两种载体，互不依赖。
4. **便携优先** — 一个文件夹 = 一套完整工作流。ideally 只需要填 API Key 就能跑。

---

## 十一、整合包维护指南

本包是**整合包**（integrated bundle）——从多个上游来源（cheat-on-content 体系、外部 GitHub 项目、Hermes 内置 skill）取快照拼装而成。不是独立项目，不是 fork。

### 维护者须知

| 角色 | 怎么做 |
|------|--------|
| **上游更新了** | 手动同步到 `skill/<name>/` 下的快照。没有自动同步机制 |
| **要改子 skill 逻辑** | 直接改 `skill/<name>/SKILL.md`，同时在「来源声明」表里注明修改偏离了上游 |
| **新增子 skill** | 复制 SKILL.md 到 `skill/<name>/`，更新「来源声明」表和路由表 |
| **删除子 skill** | 删 `skill/<name>/`，从「来源声明」表和路由表移除 |

### 发布前审计清单

每次准备发布整合包给他人时，检查以下项目：

| 检查项 | 说明 |
|--------|------|
| ❌ 硬编码路径 | 所有 `~/user/`、`/home/xxx`、`C:\Users\xxx` 路径必须泛化 |
| ❌ 用户专属内容 | 用户名、账号名、具体视频标题、项目路径必须替换为占位符 |
| ❌ 断链引用 | 所有 `../cheat-on-content/`、`../../other-package/` 等跨包路径必须改为本地路径 |
| ❌ 缺失依赖 | templates/、starter-rubrics/、shared-references/ 等被引用的目录必须存在 |
| ❌ 安装指引过时 | dependencies.md 的安装方式必须指向本包，而非上游包 |
| ✅ 有 README.md | 最外层入口，告诉人这是什么、怎么装 |
| ✅ 有 AGENTS.md | AI 操作手册，含安装步骤、首次使用指引、skill 加载路径 |

### AGENTS.md 标准格式

整合包应附带一份 AGENTS.md，让另一个 AI 读完就知道怎么干活。标准结构：

1. **零、这是什么** — 一句话说明包的作用 + 来源声明
2. **一、安装** — 分场景（Hermes / 其他 AI CLI / 独立程序）给出可执行的命令
3. **二、第一次使用** — AI 执行步骤（问用户做什么 → 运行 cheat-init → 可选导入对标）
4. **三、项目结构** — init 后自动创建的目录和文件
5. **四、Skill 总表** — 每行含 skill 名、作用、触发词、**加载路径**（指向 `skill/<name>/SKILL.md`）
6. **五、完整业务流程** — 8 阶段流程图，每阶段标注调用的 skill 和加载路径
7. **六、铁律** — AI 必须遵守的规则
8. **七、状态快照** — 占位符，init 后自动填写
9. **八、触发词速查** — 用户说什么 → AI 做什么

---

## 十一、Common Pitfalls

### Pitfall 1: 文件名不一致（最常犯）
- **症状**：AI 在对话中说"已写入 scripts/my-draft.md"，但实际文件是 scripts/2026-06-15_hash_标题.md
- **根因**：LLM 在对话文本中自己生成文件名，而不是使用 skill 返回的实际路径
- **修复**：每次 skill 操作后，前端/backend 必须把实际文件路径展示给用户，后续引用用这个路径
- **预防**：见铁律 5.7

### Pitfall 2: 假设用户内容形态
- **症状**：AI 默认用户是"观点视频博主"，seed 写稿提示词写"口播稿"
- **根因**：系统提示词硬编码了内容形态
- **修复**：从配置或 state 读取 content_type/niche/platform，首次使用时先问用户
- **预防**：见铁律 5.6

### Pitfall 3: 过度生产
- **症状**：用户问"这个选题怎么样"，AI 直接写了完整脚本 + 做了卡片
- **根因**：没有先做需求分类
- **修复**：先问用户要什么，出示交付物菜单
- **预防**：见[零、需求分类]

### Pitfall 4: 双环境下的 skill 优先级冲突
- **症状**：修改了 `skill/cheat-score/SKILL.md` 后，`skill_view(name='cheat-score')` 仍然加载旧内容
- **根因**：Hermes 的 `scan_skill_commands()` 以 skill 的 `name` 字段作为唯一 key —— 同名 skill 只取**第一个**被 `os.walk` 扫描到的，后面的全部跳过（`seen_names` 去重）
- **扫描顺序**：`~/.hermes/skills/` 内的路径按字母排序
  - `creative/cheat-on-content/` < `creative/content-creator-pipeline/`
  - 所以原版（cheat-on-content）总是先被发现并注册，pipeline 里的副本永远不会被加载
- **这是双环境冲突，不是本包有问题**：
  - **仅装本包（独立用户）**→ `skill/` 是唯一来源，正常工作 ✅
  - **同时装了 cheat-on-content** → 原版优先，`pipeline/skill/` 里的副本被 shadow ❌
- **正确操作**：要修改子 skill 逻辑时，看你维护的是哪个包：
  - 你是 cheat-on-content 的维护者 → 编辑 `creative/cheat-on-content/skills/<name>/SKILL.md`
  - 你是 pipeline 整合包的维护者 → 编辑 `pipeline/skill/<name>/SKILL.md`，同时在 SKILL.md 的「来源声明」表里注明修改偏离了上游
- **给 pipeline 接收方**：本包是**整合包**——`skill/` 是主动源，放心直接修改

### Pitfall 5: Windows 多进程端口冲突
- **症状**：启动桌面工作台后，浏览器访问返回 404 或旧版本页面
- **根因**：Hermes Agent 或其他 Python 进程在后台自动重启，占用了同一端口（如 7860）
- **症状细节**：`netstat -ano | grep <port>` 显示多个 LISTENING 进程
- **修复**：换一个端口启动（如 7870），或在 main.py 中修改 `app.run(port=7870)`
- **预防**：开发期避免使用 7860 端口（Hermes Agent 默认端口），用 7870/8890 等备用端口
- **Windows 特有**：`taskkill /f /im python.exe` 会杀死所有 Python 进程包括 Hermes，导致进程自动重启更严重。应逐个 kill：`netstat -ano | grep <port> | awk '{print $5}' | xargs taskkill /f /pid`

