---
name: skill-collector-router
description: 用户明确要求使用本仓库某个 Skill 或选择具体工作流时，用于定位对应文件。
version: '2.0'
updated: '2026-11-08'
---

# Skill Collector — 按需路由

仅选择**一个最贴近当前明确任务**的 Skill；确实跨工作流时再加载第二个。不要把所有文件当成常驻 Prompt。一般聊天、普通代码修改或测试无需触发本路由。

| 明确任务 | 仅在需要时读取 |
|---|---|
| 把尚未定型的产品想法整理为定型稿 | [PM Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PM_Make_2026-11-05.md) |
| 根据已确认方案撰写或续写 PRD | [PRD Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PRD_Make_2026-11-05.md) |
| 明确使用 Codex with ChatGPT (C2C) 只读连接规划/审查 | [C2C](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Codex_With_ChatGPT_2026-11-05.md) |
| 明确导出、恢复或校验对话交接 JSON；单独输入「继续对话」 | [Continue Conversation](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Continue_Conversation_2026-11-05.md) |
| 更新既有 PRD/GitHub 并准备既有 Sites 的部署指令 | [PRD → GitHub → Sites](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_PRD_GitHub_Sites_Update_2026-11-05.md) |
| 明确输入 Ponytail 模式或 ponytail-review/audit/debt | [Ponytail](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Ponytail_ChatGPT_2026-11-05.md) |
| 明确要求故障自动恢复、断点续跑、部署失败诊断，或安装 CI/Release Manifest/Checkpoint | [Development & Deployment Recovery](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Development_Deployment_Recovery_2026-11-08.md) |
| 明确查询、更新项目状态文件或跨项目交接 | [Project Handoff](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Project_Handoff_2026-11-05.md) |
| 企业系统架构设计、设计文档或跨模块设计评审 | [Enterprise System Design](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/System%20Design/skill_Enterprise_System_Design_2026-11-05.md) |

## GitHub Remote Skill Loader v2

本路由可单独上传到 ChatGPT Project；其余 Skill 不必上传。上表为**远程原文 URL**，不是本地相对路径。仅当用户任务与某行明确匹配时，使用当前会话**实际可用且已授权**的 GitHub 连接器或公开网页读取该 URL 的完整内容，确认文件路径与实际版本，再执行。不要凭路由摘要猜测 Skill 正文。

- GitHub 连接器优先读取 `jaronhoh/ChatGPT-skill-collector` 的对应路径；公开 raw URL 是备选，不要求用户复制九份文件。
- 使用 `main` 代表读取时的最新版本，不保证跨会话固定不变。需要可复现执行时记录读取到的 Git commit SHA，必要时改用该 SHA 的 raw URL 固定版本。
- 读取失败：先检查连接器是否可用、URL/分支是否有效、仓库是否公开；可做一次安全重试。仍失败则说明该 Skill **未加载**，保留可独立执行的通用工作，不编造规则，也不要求用户无意义地重启对话。
- Project 文件不会自动刷新 GitHub 内容；修改 GitHub 的**路由或 AGENTS.md 本身**后，已上传的两个 Project 文件需要重新上传才能获得新入口规则。
- 这是文档驱动的按需读取，不是后台自动同步服务，也不能保证每个聊天环境都能访问远程仓库。

## 加载规则

1. 根文件只做路由；选定后才读取目标 Skill 的任务相关章节、模板或引用文档。
2. 不因为 Skill 里提到 GitHub、测试、UI、NocoBase 或 Sites 就额外激活其他 Skill。
3. 遵守 Project 中上传的 `AGENTS.md` 的执行边界和完成标准；如某个旧 Skill 仍包含过时的全局要求，以 AGENTS.md 的通用边界及用户当前明确指令为准。
4. 保持原有九个 Skill 的 GitHub 路径；只有命中任务才读取对应远程文件。
