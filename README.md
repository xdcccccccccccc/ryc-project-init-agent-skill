# project-init-SKILL-v5

这是一个用于 Codex 长周期协作的项目初始化 skill。

它只负责“第一次初始化”，不负责日常功能开发。它会为项目建立可持久化的状态目录、初始计划、初始 backlog，以及后续线程继续工作的最小引导规则。

## 这个版本的目标

- 把项目初始化流程固定下来。
- 把关键状态存到 `~/.codex-state/<project-key>/`。
- 避免 `using-superpowers` / `writing-plans` 把计划文件写进 git 仓库。
- 要求后续线程继续按 one-feature-per-cycle 方式推进。
- 默认用中文做用户侧沟通，除非用户明确要求其他语言。

## v5 的关键变化

和前一版相比，v5 已经不再使用全局共享路径 `~/.codex-state/plan/plan.md`。

现在改为项目隔离结构：

- 主计划文件：`~/.codex-state/<project-key>/plan/plan.md`
- 后续专题计划：`~/.codex-state/<project-key>/plan/xxx_plan.md`

这样做的目的，是避免多个项目共用同一个 `plan.md` 时互相覆盖。

## 持久化文件布局

项目专属状态目录：

- `~/.codex-state/<project-key>/state.json`
- `~/.codex-state/<project-key>/feature_list.json`
- `~/.codex-state/<project-key>/progress.md`
- `~/.codex-state/<project-key>/plan/plan.md`
- `~/.codex-state/<project-key>/plan/xxx_plan.md`

项目根目录最小引导文件：

- `AGENTS.md`
- `.gitignore`

## 初始化阶段会做什么

1. 识别项目根目录和 git 状态。
2. 生成稳定的 `project-key`。
3. 创建 `~/.codex-state/<project-key>/`。
4. 创建 `~/.codex-state/<project-key>/plan/`。
5. 扫描项目结构、README、构建文件、测试配置、已有 workflow 文件等。
6. 向用户确认目标、第一阶段交付物、边界和约束。
7. 写入初始 `plan.md`、`feature_list.json`、`progress.md`、`state.json`。
8. 在项目根目录创建或最小更新 `AGENTS.md`。
9. 在必要时最小更新 `.gitignore`，避免 `AGENTS.md` 被提交。

## 初始化后的常规线程规则

后续线程在开始工作前，应先读取：

- `~/.codex-state/<project-key>/state.json`
- `~/.codex-state/<project-key>/plan/plan.md`
- `~/.codex-state/<project-key>/feature_list.json`
- `~/.codex-state/<project-key>/progress.md`

然后：

- 使用 `using-superpowers`
- 一次只处理一个未完成 feature
- 完成后更新 `feature_list.json`
- 如果总体计划、范围、执行顺序发生变化，更新 `plan/plan.md`
- 如果使用 `write-plan` 或 `writing-plans` 生成专题计划，把新文件写到 `plan/` 目录下
- 追加更新 `progress.md`

## write-plan 文件规则

v5 明确要求：

- `write-plan` / `writing-plans` 不得把计划写入仓库内默认路径，例如 `docs/superpowers/plans/`
- 所有专题计划文件必须写到 `~/.codex-state/<project-key>/plan/`
- 新文件名必须以 `_plan.md` 结尾

推荐命名方式：

- `auth-flow_plan.md`
- `phase1-api_plan.md`
- `deploy-checklist_plan.md`

不推荐：

- `new_plan.md`
- `temp_plan.md`
- `plan2.md`

如果是在更新同一个专题，优先复用原有专题文件，而不是重复创建近似文件。

## 与 using-superpowers 的关系

这个 skill 不取代 `using-superpowers`，而是负责：

- 第一次初始化时建立持久化状态
- 把后续线程的行为边界写进 `AGENTS.md`
- 用项目级偏好覆盖上游 `writing-plans` 的默认仓库内落盘行为

换句话说，这个 skill 负责“立规矩”，后续常规开发还是交给 `using-superpowers`。

## 适用场景

适合：

- 一个项目第一次接入 Codex
- 希望后续线程依赖持久化状态，而不是聊天记忆
- 希望计划文件不要污染 git 仓库
- 希望未来线程持续按 feature 粒度推进

不适合：

- 已经初始化完毕且状态健康的项目
- 日常功能开发或 bug 修复
- 不希望把状态写入 `~/.codex-state` 的场景

## 已知设计取舍

v5 已经把计划文件从“全局共享单文件”升级成“按项目隔离目录”，所以最主要的串项目风险已经显著下降。

当前仍然有一些设计取舍需要注意：

- `AGENTS.md` 是通过项目根目录引导后续线程遵守规则的，不是直接修改上游 superpowers skill 本身
- 如果用户明确要求把计划写回仓库，这个 skill 应该允许用户指令覆盖默认规则
- `xxx_plan.md` 的命名仍然依赖 agent 选择合适的 topic slug，因此需要在规范里持续强调命名约束

## 当前版本摘要

- 版本：v5
- 默认语言：中文
- 默认日常执行 skill：`using-superpowers`
- 主计划路径：`~/.codex-state/<project-key>/plan/plan.md`
- 专题计划路径：`~/.codex-state/<project-key>/plan/xxx_plan.md`
- 项目专属状态路径：`~/.codex-state/<project-key>/`
