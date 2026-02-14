---
title: Claude Code 预加载机制深度解析
date: 2026-02-14
tags:
  - claude-code
  - AI-tooling
  - dev-workflow
status: draft
---

# Claude Code 预加载机制深度解析：理解你的 AI 编程助手如何"看见"你的项目

> Claude Code 启动时到底加载了什么？子目录的指令为什么没生效？团队 Agent 能不能自动读取本地规则？这篇文章一次讲清楚。

## 为什么要了解预加载机制？

使用 Claude Code 做项目开发时，你可能会写 `CLAUDE.md` 来给 AI 注入项目上下文，也可能配置了 `.claude/rules/`、自定义 Agents 和 Skills。但你有没有想过——**这些配置到底是什么时候、以什么方式被加载的？**

理解预加载机制，能帮你：
- 避免"写了指令却不生效"的困惑
- 合理组织多目录项目的上下文
- 在团队 Agent 协作中正确注入信息

## 五大组件一览

Claude Code 的上下文系统由 5 个核心组件构成：

| 组件 | 位置 | 加载方式 | 触发条件 |
|------|------|----------|----------|
| **CLAUDE.md** | 项目根目录及祖先目录 | 自动 | 会话启动 |
| **CLAUDE.md（子目录）** | 子目录 | 按需 | 访问该子目录文件时 |
| **.claude/rules/\*.md** | 项目根 `.claude/rules/` | 自动 | 会话启动 |
| **.claude/agents/\*.md** | `.claude/agents/` | 按需 | `Task` 工具调用 |
| **SKILL.md** | `~/.claude/skills/` 或项目级 | 按需 | `/command` 触发 |

关键点：只有 **CLAUDE.md（根目录）** 和 **Rules** 是启动时自动加载的，其他都是按需触发。

## CLAUDE.md：双向目录遍历

### 向上遍历（祖先目录）—— 自动加载

会话启动时，Claude Code 从当前工作目录（CWD）向上逐级查找 `CLAUDE.md`，直到根目录 `/`：

```
/
└── Users/carrick/
    └── .claude/CLAUDE.md        ← 用户级（如存在）
    └── myproject/
        ├── CLAUDE.md            ← ✅ 自动加载（项目根）
        └── src/components/      ← CWD
```

所有祖先目录中找到的 `CLAUDE.md` 都会被自动加载。

### 向下遍历（子目录）—— 按需加载

子目录中的 `CLAUDE.md` 在启动时**只发现、不加载**。只有当你让 Claude 读取、搜索或操作该子目录中的文件时，对应的 `CLAUDE.md` 才会被加载到上下文中。

这个设计很合理——一个大型 monorepo 可能有几十个子目录各自的 `CLAUDE.md`，全部自动加载会浪费宝贵的上下文窗口。

### 优先级（低 → 高）

1. 系统托管策略
2. 用户级 `~/.claude/CLAUDE.md`
3. 祖先目录（向上遍历）
4. 当前目录 `./CLAUDE.md`
5. **`./CLAUDE.local.md`** —— 最高优先级，适合放个人覆写，且默认被 gitignore

## Rules：项目根目录的自动规则

`.claude/rules/` 下的所有 `.md` 文件在会话启动时**递归自动加载**：

```
.claude/rules/
├── git-workflow.md      ← ✅ 自动加载
├── documentation.md     ← ✅ 自动加载
└── frontend/
    └── react.md         ← ✅ 也会自动加载（递归）
```

**但有一个重要限制**：只有**项目根目录**（git root）下的 `.claude/rules/` 会被识别。子目录中的 `.claude/rules/` **永远不会被加载**，即使加了 `alwaysApply: true` 的 frontmatter 也不行。

## Agents 与 Skills：按需调用

- **Agents**（`.claude/agents/*.md`）：通过 `Task` 工具的 `subagent_type` 参数触发。每个 Agent 是一个独立的子会话，有自己的系统提示。
- **Skills**（`SKILL.md`）：通过 `/command` 斜杠命令触发。分为全局 Skills（`~/.claude/skills/`）和项目级 Skills。

两者都不会在启动时自动加载，只在需要时按需注入，节省上下文空间。

## 团队 Agent 的上下文隔离

这是最容易踩坑的部分。通过 `Task` 工具生成的团队 Agent（Teammate）：

- 拥有**独立的上下文窗口**
- **只能看到** Leader 注入的 prompt
- **不会**自动加载目标目录的 `CLAUDE.md`
- **不会**看到其他 Agent 的对话

实测验证了主会话和 Teammate 的行为对比：

| 行为 | 主会话 | Teammate |
|------|:------:|:--------:|
| 项目根 CLAUDE.md | ✅ 自动 | ✅ 自动 |
| 项目根 .claude/rules/ | ✅ 自动 | ✅ 自动 |
| 子目录 CLAUDE.md | ⏳ 按需 | ⏳ 按需 |
| 子目录 .claude/rules/ | ❌ 永不 | ❌ 永不 |

### 解决方案：手动 Prompt 注入

如果需要让 Teammate 获得特定子目录的上下文，Leader 需要：

1. **先读取**目标目录的 `claude.md` / rules 内容
2. **将内容注入**到 Teammate 的 spawn prompt 中
3. Teammate 在隔离环境中使用注入的上下文工作

## 实用建议

1. **持久化配置**：重要的 Agent 行为指令写在 `CLAUDE.md` 或 `.claude/rules/` 中，确保 `/compact` 后和新会话中依然生效
2. **善用 `CLAUDE.local.md`**：个人偏好和覆写放在这里，不污染团队的 git 仓库
3. **Rules 分类**：利用 `.claude/rules/` 下的子文件夹组织规则，反正会递归加载
4. **Team Agent 协作**：别指望 Agent 自动读取子目录上下文，Leader 负责注入
5. **Agent 模型选择**：`Task` 工具的 `model` 参数（`haiku`/`sonnet`/`opus`）是硬性指定，不是建议

## 总结

Claude Code 的预加载机制遵循一个清晰的设计哲学：**自动加载核心配置，按需加载扩展内容，严格隔离 Agent 上下文**。理解这套机制，你就能更高效地组织项目指令，避免"配了却没生效"的坑，也能在多 Agent 协作中游刃有余。

---

*本文基于 2026 年 2 月的实际测试结果撰写，Claude Code 的机制可能随版本更新而变化。*
