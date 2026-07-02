# Content Creator Pipeline

通配内容创作者工作流整合包。适用于任何内容形态（观点视频 / 口播 / 长文 / 播客 / 教程等）。

完整闭环：火花固化 → 深挖写稿 → 盲打分 → 预测 → 拍摄 → 发布 → 复盘 → 校准升级。

---

## 快速开始

### 安装

```bash
git clone https://github.com/SamuelKwj/content-creator-pipeline.git
cp -r content-creator-pipeline/ ~/.hermes/skills/creative/
```

然后在 Hermes 对话中说 **"初始化"** 开始。

### 其他 AI CLI（Claude Code / Codex / Cline 等）

把整个文件夹复制到你的项目目录，AI 读 `SKILL.md` + `skill/` 下的子 skill 即可工作。

---

## 包结构

```
content-creator-pipeline/
├── README.md                    # 本文件
├── SKILL.md                     # 主 skill 定义（流程总纲）
├── references/
│   ├── dependencies.md          # 依赖来源清单
│   ├── desktop-workbench.md     # 独立桌面程序安装指引
│   └── shared-references/       # 子 skill 共用参考文档
│
├── skill/                       # 所有子 skill（开箱即用）
│   ├── cheat-spark/             # 火花固化：灵感捕获 → 标题提炼 → 盲打分 → inbox
│   ├── cheat-seed/              # 选题深挖 → 写 draft
│   ├── cheat-score/             # 盲打分调度
│   ├── cheat-score-blind/       # 盲打分 sub-agent（隔离通道）
│   ├── cheat-predict/           # immutable 预测日志
│   ├── cheat-shoot/             # 拍摄登记
│   ├── cheat-publish/           # 发布登记
│   ├── cheat-retro/             # T+N 复盘
│   ├── cheat-bump/              # rubric 升级
│   ├── cheat-persona/           # 受众画像
│   ├── cheat-recommend/         # 选题推荐
│   ├── cheat-trends/            # 热点抓取
│   ├── cheat-status/            # 状态看板
│   ├── cheat-learn-from/        # 对标导入
│   ├── cheat-migrate/           # schema 迁移
│   ├── cheat-init/              # 首次初始化
│   ├── humanizer/               # 去 AI 味
│   └── dbs-hook/                # 短视频开头优化
└── PIPELINE_AUDIT.md            # 审查记录
```

---

## 技能总表

### 核心流程（16 个）

| Skill | 环节 | 说明 |
|-------|------|------|
| `cheat-spark` | 火花固化 | 灵感捕获 → 标题提炼 → 盲打分 → inbox.jsonl |
| `cheat-seed` | 选题深挖 | 引导式对话 → 写 draft |
| `cheat-score` | 盲打分调度 | 主 agent 调 sub-agent 出分 |
| `cheat-score-blind` | 盲打分 sub-agent | 隔离通道，只读脚本+rubric |
| `cheat-predict` | 预测日志 | 盲打分 → 锚点对比 → bucket → 反事实 → 落盘 |
| `cheat-shoot` | 拍摄登记 | 建 video folder |
| `cheat-publish` | 发布登记 | 写 URL/平台/时间到预测文件 |
| `cheat-retro` | 复盘 | T+N 数据回收 → 预测 vs 实绩 |
| `cheat-bump` | rubric 升级 | 校准池重打分 → 调整维度/权重 |
| `cheat-persona` | 受众画像 | 从评论数据派生 |
| `cheat-recommend` | 选题推荐 | 候选池按 rubric 排序 |
| `cheat-trends` | 热点抓取 | 抓热点 → 粗打分 → 写入候选池 |
| `cheat-status` | 状态看板 | 校准进度 / 待复盘 / buffer |
| `cheat-learn-from` | 对标导入 | 拆对标 pattern → benchmark |
| `cheat-migrate` | schema 迁移 | 升级 .cheat-state.json |
| `cheat-init` | 初始化 | 建项目脚手架 |

### 辅助工具（2 个）

| Skill | 说明 |
|-------|------|
| `humanizer` | 去 AI 味（MIT，来自 blader/humanizer） |
| `dbs-hook` | 短视频开头优化 |

---

## 业务流程

```
火花固化 → 选题深挖 → 盲打分 → 预测 → 拍摄 → 发布 → 复盘 → 校准升级
   ↑                                                          ↓
   └──────────────── 长期校准循环 ←────────────────────────────┘
```

---

## 依赖

| 依赖 | 说明 | 必须？ |
|------|------|--------|
| LLM API Key（OpenAI 兼容） | 所有子 skill 通过 LLM 执行 | ✅ 必须 |
| Hermes Agent / AI CLI | 运行环境 | ✅ 必须（任选其一） |
| 数据抓取 adapter | 自动回收播放/评论数据 | ❌ 可选 |

---

## License

MIT。各子 skill 可能携带不同许可证（详见具体文件）。
