---
name: dev
description: "按 koyospec/<name>/tasks.md 逐任务执行开发，每个任务跑验证后提交。需先用 /koyospec:propose 生成 4 份文档。"
---

# Spec 驱动开发 - 开发（Dev）

本命令承接 `/koyospec:propose` 的产物——按 `koyospec/<active>/tasks.md` 执行开发。开发完成后用 `/koyospec:archive` 执行验收并归档。

<HARD-GATE>
4 份文档（spec.md / plan.md / tasks.md / checklist.md）已生成并经用户审批后方可开发。若尚未审批，请回 `/koyospec:propose` 完成。
</HARD-GATE>

## 前置检查：发现活跃 spec

**活跃 spec 定义**：`koyospec/` 下直接子目录，同时含 `spec.md`、`plan.md`、`tasks.md`、`checklist.md` 四个文件，且不在 `archive/` 下。半成品（4 份未齐全）不算活跃 spec。

扫描 `koyospec/`（排除 `archive/`）找出所有活跃 spec，按下表处理：

| 活跃 spec 数 | 行为 |
|---|---|
| 0 | 输出「未发现活跃 spec。请先运行 `/koyospec:propose` 生成 4 份文档。」并停止。 |
| 1 | 默认采用，宣告采用的 spec 名与路径，继续后续流程。 |
| >1 | 列出所有活跃 spec 名（带编号），让用户选一个。**一次一个问题，选择题形式。** 选定后宣告，继续。 |

## 前置检查：4 份文档齐全

选定活跃 spec 后，再次确认该文件夹下 `spec.md`、`plan.md`、`tasks.md`、`checklist.md` 齐全。

- 缺 `tasks.md` → 输出「该 spec 缺 tasks.md，无法开发。请回 `/koyospec:propose` 完成阶段三（任务拆解）。」并停。
- 缺其它任一 → 列出缺失清单，指引回 `/koyospec:propose` 补齐，停。

## 阶段五：开发

4 份文档全部通过审批，开始实现。

**宣告：** 「四份文档已全部通过审批，按 `koyospec/<name>/tasks.md` 开始开发。」

### 流程

1. 读 `tasks.md`，为所有任务创建进度追踪
2. 按执行顺序逐个完成任务：
   - 按步骤执行
   - 运行该任务的验证步骤
   - **先有证据再下结论**——先跑命令、看输出，再报状态
   - 验证通过后才标记完成
3. 如果被阻塞：停下来问，不要猜
4. 所有任务完成后建议运行 `/koyospec:archive`

### 进度标记

直接在 `tasks.md` 上把完成的任务标记 `[x]`，不新增其它进度文件。`/koyospec:archive` 验收时也会在 `checklist.md` 上标 `[x]`。

### 规则

- 按 `tasks.md` 的步骤执行，除非被阻塞否则不自由发挥
- 每个任务完成后必须跑验证，「应该没问题」不算证据
- 验证不通过就先修，修好再往下走
- 每个任务或每组逻辑相关的任务完成后提交代码
- 若开发中发现 spec 需要变更：回 `/koyospec:propose` 修改对应文档并重新审批，再回 dev。dev 不擅自修改 spec/plan/tasks/checklist 的设计性内容（进度标记除外）。

## 核心原则

- **按 tasks.md 执行**——不自由发挥，不越界设计
- **YAGNI 铁律**——只实现 spec 提到的内容
- **先有证据再下结论**——先跑验证，再报告结果
- **一次一个问题**——被阻塞就停下问

## 危险信号

| 想法 | 现实 |
|------|------|
| 「跳过 tasks.md 直接写代码」 | HARD GATE：4 份文档全过了才能动代码，且要按 tasks.md 顺序 |
| 「应该没问题了」 | 跑一下。先有证据再下结论 |
| 「测试过了就说明没问题」 | 测试验证代码，checklist 验证需求。验收在 `/koyospec:archive` |

## 完成后指引

所有任务完成并跑过验证。建议运行 `/koyospec:archive` 执行验收并归档（按 `checklist.md` 逐项检查，通过后移入 `koyospec/archive/<日期>-<name>/`）。
