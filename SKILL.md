---
name: skill-collector-router
description: 根据明确任务选择远程 Skill；包含 Sites 发布目标时先核验发布路径，再按需执行部署或交接。
version: '2.2'
updated: '2026-11-09'
---

# Skill Collector — 按需路由

仅选择**一个最贴近当前明确任务**的 Skill；确实跨工作流时再加载第二个。不要把所有文件当成常驻 Prompt。一般聊天、普通代码修改或测试无需触发本路由。

| 明确任务 | 仅在需要时读取 |
|---|---|
| 把尚未定型的产品想法整理为定型稿 | [PM Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PM_Make_2026-11-05.md) |
| 根据已确认方案撰写或续写 PRD | [PRD Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PRD_Make_2026-11-05.md) |
| 明确使用 Codex with ChatGPT (C2C) 只读连接规划/审查 | [C2C](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Codex_With_ChatGPT_2026-11-05.md) |
| 明确导出、恢复或校验对话交接 JSON；单独输入「继续对话」 | [Continue Conversation](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Continue_Conversation_2026-11-05.md) |
| 用户目标包含 Sites 上线、更新生产、部署或发布（无需再次要求 Work 指令） | [PRD → GitHub → Sites](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_PRD_GitHub_Sites_Update_2026-11-05.md) |
| 明确输入 Ponytail 模式或 ponytail-review/audit/debt | [Ponytail](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Ponytail_ChatGPT_2026-11-05.md) |
| 明确要求故障恢复/断点续跑/CI，或发布预检、同步、部署需要恢复 | [Development & Deployment Recovery](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Development_Deployment_Recovery_2026-11-08.md) |
| 明确查询、更新项目状态文件或跨项目交接 | [Project Handoff](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Project_Handoff_2026-11-05.md) |
| 企业系统架构设计、设计文档或跨模块设计评审 | [Enterprise System Design](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/System%20Design/skill_Enterprise_System_Design_2026-11-05.md) |

## Sites 发布前置核验与按需 Work 交接

只要当前任务的**明确交付终点包含 ChatGPT Sites 部署**，即使用户只说「修复并上线」「继续完成 Production」「部署到原 Site」，也必须主动加载上表 PRD → GitHub → Sites Skill；如果涉及失败恢复，再按需加载 Recovery Skill。**不要求用户再次提醒「给我 Work 指令」。**

开发前按发布 Skill §3.1 核实原 Site ID、实际绑定源码与同步能力、Save Version、Deploy 和 Production 验收入口。已完成开发的任务从第一个未完成的发布阶段继续。Chat 负责已授权的开发、风险适配测试、GitHub 提交和证据核验。若当前 Chat 无法直接完成 Sites 发布，且 Work 有经核实的剩余步骤执行能力，则在这些步骤完成后**主动在当前回复输出完整可复制的 Work-only 指令**，包括精确源码 SHA、测试证据、现有 Site、保护约束、部署步骤、Production 验证与失败恢复；不自动创建 Work 任务，也不把 Work 指令称为已部署。

若发布路径失败，加载 Recovery §0.2，从 checkpoint 尝试实际可用且获授权的备选路径。仅在 Work 有可核实的不同能力时交接剩余步骤。Work 不因发布失败把同一任务推回 Chat 重做开发或测试。用户只要求 GitHub/文档而未要求发布时，不额外生成 Work 指令。

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


## v3 路由验证
路由命中不等于 Skill 已加载。执行者须实际读取对应文件，记录路径与版本；加载失败如实报告。Sites 正常发布只按 Sites Skill 执行；发生可证实的发布故障才加载 Recovery Skill，并从已有 checkpoint 继续。不得因关键词相似而加载全部 Skill。
