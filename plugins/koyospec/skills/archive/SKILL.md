---
name: archive
description: "按 koyospec/<name>/checklist.md 逐项验收，全绿后将文件夹移入 koyospec/archive/<YYYY-MM-DD>-<name>/。开发完成后使用。"
---

# Spec 驱动开发 - 归档（Archive）

本命令承接 `/koyospec:dev` 的产物——按 `koyospec/<active>/checklist.md` 执行验收，全绿后把整个 spec 文件夹归档。

## 前置检查：发现活跃 spec

**活跃 spec 定义**：`koyospec/` 下直接子目录，同时含 `spec.md`、`plan.md`、`tasks.md`、`checklist.md` 四个文件，且不在 `archive/` 下。

扫描 `koyospec/`（排除 `archive/`）找出所有活跃 spec，按下表处理：

| 活跃 spec 数 | 行为 |
|---|---|
| 0 | 输出「无活跃 spec 可归档。」并停止。 |
| 1 | 默认采用，宣告 spec 名与路径，继续后续流程。 |
| >1 | 列出所有活跃 spec 名（带编号），让用户选一个。**一次一个问题，选择题形式。** 选定后继续。 |

## 前置检查：4 份文档齐全

选定活跃 spec 后，再次确认该文件夹下 `spec.md`、`plan.md`、`tasks.md`、`checklist.md` 齐全。

- 缺 `checklist.md` 尤其致命（没法验收）。
- 缺任一 → 输出缺失清单，指引回 `/koyospec:propose` 补齐，停。

## 阶段六：验收

### 流程

1. 读 `checklist.md`
2. 逐项执行：
   - 运行验证方式
   - 记录实际结果和证据（命令输出、观察到的行为）
   - 标记通过/不通过（在 `checklist.md` 上标 `[x]` / `[ ]`，附证据）
3. 如果有不通过的：修复、重新验证、报告
4. 进入验收门槛判断

### 规则

- **先有证据再下结论。** 先跑命令，看输出，然后再报告。
- 报告**实际结果**，不是预期结果。
- 有不通过的条目不丢人——说明 checklist 发挥了作用。修好重跑即可。

### 验收报告

```
## 验收报告

### 通过（N/M）
- [x] 条目 1 — 证据：...
- [x] 条目 2 — 证据：...

### 未通过（如有）
- [ ] 条目 3 — 预期：X，实际：Y，修复方案：...

### 端到端
- [x] 场景 1 — 结果：...
```

## 验收门槛

- **全部通过** → 进入归档移动。
- **有未通过项** → **不归档**。输出未通过清单 + 修复建议，建议修复后重跑 `/koyospec:archive`，停止。

归档里只能放真正完成的需求。验收不过硬不归档。

## 归档移动

仅当验收全部通过时执行。

1. **日期生成**：取今天日期，格式 `YYYY-MM-DD`（运行当日）。可用跨平台命令 `date +%Y-%m-%d`（bash/zsh；Windows 上走 Git Bash 或 WSL）。
2. **目标路径**：`koyospec/archive/<YYYY-MM-DD>-<name>/`，`<name>` 与活跃期一致。
3. **创建 `koyospec/archive/`** 若不存在。
4. **校验目标不存在**：若 `koyospec/archive/<YYYY-MM-DD>-<name>/` 已存在（极少见：同日同名归档过），提示冲突，默认加 `-2` 后缀，让用户确认。
5. **移动整个 `koyospec/<name>/` 目录**到目标（移动而非复制，原路径归档后不存在）。
6. **不改文件内容**：spec/plan/tasks/checklist 原样保留，含 checklist 上标的 `[x]` 与验收证据。
7. **宣告**：「已归档至 `koyospec/archive/<date>-<name>/`。」

## 核心原则

- **先有证据再下结论**——先跑验证，再报告结果
- **报告实际结果**，不是预期结果
- **归档门槛硬**——验收不过不归档

## 危险信号

| 想法 | 现实 |
|------|------|
| 「应该没问题了」 | 跑一下。先有证据再下结论 |
| 「验收差不多了，先归档吧」 | 有未通过项不归档。修好重跑 |
| 「测试过了就说明没问题」 | 测试验证代码，checklist 验证需求。逐项过 checklist |

## 完成后指引

已归档至 `koyospec/archive/<date>-<name>/`。可运行 `/koyospec:propose` 开始下一个需求。
