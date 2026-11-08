---
name: skill-collector-router
description: 用户明确要求使用本仓库某个 Skill 或选择具体工作流时，用于定位对应文件。
version: '1.0'
updated: '2026-11-08'
---

# Skill Collector — 按需路由

仅选择**一个最贴近当前明确任务**的 Skill；确实跨工作流时再加载第二个。不要把所有文件当成常驻 Prompt。一般聊天、普通代码修改或测试无需触发本路由。

| 明确任务 | 仅在需要时读取 |
|---|---|
| 把尚未定型的产品想法整理为定型稿 | [PM Make](PRD/skill_PM_Make_2026-11-05.md) |
| 根据已确认方案撰写或续写 PRD | [PRD Make](PRD/skill_PRD_Make_2026-11-05.md) |
| 明确使用 Codex with ChatGPT (C2C) 只读连接规划/审查 | [C2C](Process%20Rules/skill_Codex_With_ChatGPT_2026-11-05.md) |
| 明确导出、恢复或校验对话交接 JSON；单独输入「继续对话」 | [Continue Conversation](Process%20Rules/skill_Continue_Conversation_2026-11-05.md) |
| 更新既有 PRD/GitHub 并准备既有 Sites 的部署指令 | [PRD → GitHub → Sites](Process%20Rules/skill_PRD_GitHub_Sites_Update_2026-11-05.md) |
| 明确输入 Ponytail 模式或 ponytail-review/audit/debt | [Ponytail](Process%20Rules/skill_Ponytail_ChatGPT_2026-11-05.md) |
| 明确要求故障自动恢复、断点续跑、部署失败诊断，或安装 CI/Release Manifest/Checkpoint | [Development & Deployment Recovery](Process%20Rules/skill_Development_Deployment_Recovery_2026-11-08.md) |
| 明确查询、更新项目状态文件或跨项目交接 | [Project Handoff](Process%20Rules/skill_Project_Handoff_2026-11-05.md) |
| 企业系统架构设计、设计文档或跨模块设计评审 | [Enterprise System Design](System%20Design/skill_Enterprise_System_Design_2026-11-05.md) |

## 加载规则

1. 根文件只做路由；选定后才读取目标 Skill 的任务相关章节、模板或引用文档。
2. 不因为 Skill 里提到 GitHub、测试、UI、NocoBase 或 Sites 就额外激活其他 Skill。
3. 遵守 [AGENTS.md](AGENTS.md) 的执行边界和完成标准；如某个旧 Skill 仍包含过时的全局要求，以 AGENTS.md 的通用边界及用户当前明确指令为准。
4. 保持原有八个文件路径，方便已建立的引用继续使用；恢复机制为独立第九个 Skill，不因一般代码任务自动触发。
