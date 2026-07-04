# koyospec — Spec 驱动开发的 Claude Code 插件

> 把「拍脑袋就写代码」改成「四份递进文档 + 硬门禁审批后才能动代码」。

## 它解决什么问题

写过代码的人都有过这种经历：需求还没想清楚就开干，写到一半发现假设错了，推倒重来。koyospec 用一套硬流程逼你在写代码前先把「做什么 / 怎么做 / 按什么顺序做 / 做对了没」四份文档过一遍，每份都要你点头才进下一阶段。四份全过，才允许动代码。

## 工作流

```
想法 → /koyospec:propose → spec.md → plan.md → tasks.md → checklist.md
                           （每份经你审批才进下一阶段）
     → /koyospec:dev      → 按 tasks.md 逐任务实现，每个任务跑验证
     → /koyospec:archive  → 按 checklist.md 逐项验收，全绿后归档
```

三个命令构成 propose → dev → archive 三阶段，产出统一放在 `koyospec/<name>/`。

## 三个命令

| 命令 | 什么时候用 | 输入 | 产出 |
|------|-----------|------|------|
| `/koyospec:propose` | 开始任何功能/模块/章节开发前 | 一个想法 | `koyospec/<name>/` 下四份文档（spec/plan/tasks/checklist） |
| `/koyospec:dev` | 四份文档审批后 | 上述文档 | 按 tasks.md 实现的代码，tasks 上标 `[x]` |
| `/koyospec:archive` | 开发完成后 | 上述文档 + 代码 | 验收报告，全绿后整个文件夹移入 `archive/` |

## 安装

通过 Claude Code plugin marketplace 一键安装：

```
/plugin marketplace add koyomi315/koyo-spec
/plugin install koyospec
```

装好后输入 `/koyospec:` 就能看到三个命令的自动补全。

<details>
<summary>手动安装（不走 plugin）</summary>

把 `plugins/koyospec/skills/` 下的三个文件夹复制到 `~/.claude/skills/`：

```bash
cp -r plugins/koyospec/skills/{propose,dev,archive} ~/.claude/skills/
```

</details>

## 快速上手

1. 在你的项目根目录运行 `/koyospec:propose`。
2. 它先问你一句话描述要做什么，再提议一个 kebab-case 名字（如 `add-file-upload`），你确认。
3. 进入四阶段，每阶段逐段呈现并等你审批：
   - **spec.md** — 做什么（背景、目标、功能需求、非功能需求、不做的事、验收标准）
   - **plan.md** — 怎么做（架构、组件、接口、数据结构、技术决策）
   - **tasks.md** — 按什么顺序做（文件清单、有序任务、每个任务带验证方式）
   - **checklist.md** — 做对了没（可观测的行为检查、集成检查、端到端场景）
4. 四份都过审后，建议运行 `/koyospec:dev` 按 tasks.md 实现。
5. 开发完跑 `/koyospec:archive` 验收，全绿后归档到 `koyospec/archive/<日期>-<name>/`。

## 四份文档的职责

| 文档 | 回答什么 | 包含什么 |
|------|---------|---------|
| spec.md | 做什么 | 背景、目标、功能需求、非功能需求、边界、验收标准 |
| plan.md | 怎么做 | 架构概览、组件划分、核心接口与数据结构、模块交互、技术决策 |
| tasks.md | 按什么顺序做 | 文件清单、有序任务列表、每个任务的步骤和验证方式 |
| checklist.md | 做对了没 | 可观测的行为检查、集成检查、端到端场景 |

## 核心设计原则

- **硬门禁（HARD GATE）** — 四份文档全部生成并经你批准前，禁止写任何实现代码。再简单的需求也走完整流程。
- **一次一个问题** — 澄清需求时每次只问一个，优先用选择题。
- **YAGNI 铁律** — 只设计和实现 spec 提到的内容，不提前设计「以后可能要」的东西。
- **先有证据再下结论** — 每个任务的验证要真跑命令看输出，「应该没问题」不算证据。
- **聚焦行为描述** — spec 和 checklist 描述系统做什么（与语言/实现解耦），plan/tasks 才进入怎么做的细节。

## 目录约定

```
你的项目/
└── koyospec/
    ├── <name>/                 ← 活跃 spec（4 份文档 + 开发产物）
    │   ├── spec.md
    │   ├── plan.md
    │   ├── tasks.md
    │   └── checklist.md
    └── archive/
        └── <YYYY-MM-DD>-<name>/ ← 已验收归档的 spec
```

- **活跃 spec**：`koyospec/` 下直接子目录，含齐全的四份文档，未归档。
- **归档**：`/koyospec:archive` 验收全绿后把整个 `<name>/` 移入 `archive/<日期>-<name>/`，原路径消失。
- **多活跃 spec 可并行**：`/koyospec:dev`、`/koyospec:archive` 在多于 1 个时让你选哪个。

## 要求

- Claude Code（支持 plugin 的版本）
- 跨平台：macOS / Windows / Linux

## 本仓库结构

```
koyo-spec/
├── .claude-plugin/
│   └── marketplace.json        # marketplace 清单
├── plugins/koyospec/
│   ├── .claude-plugin/
│   │   └── plugin.json         # plugin 清单
│   └── skills/
│       ├── propose/
│       │   ├── SKILL.md
│       │   └── templates/
│       │       ├── spec.md
│       │       ├── plan.md
│       │       ├── tasks.md
│       │       └── checklist.md
│       ├── dev/SKILL.md
│       └── archive/SKILL.md
├── README.md
├── LICENSE
└── .gitignore
```

## License

MIT。三个 SKILL.md 欢迎拿去改、用、二次分发。

---

> 反模式提醒：「这个太简单了，不需要写 spec」——越「简单」的项目，未被审视的假设越多，返工概率越高。文档可以写得短，但必须存在、必须被审批。
