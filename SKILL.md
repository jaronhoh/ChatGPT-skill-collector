---
name: skill-collector-router
description: 按明确任务选择远程 Skill；GitHub-only 使用当前 Chat；不包含 ChatGPT Sites 部署流程。
version: '4.1'
updated: '2026-11-10'
---

# Skill Collector — Minimal Router v4.1

只按用户明确的交付目标加载对应 Skill；普通 GitHub 操作、代码修改与测试无需加载专项 Skill。必须实际读取匹配文件，记录路径与版本；无法读取时如实报告。

| 明确意图 | 远程 Skill |
|---|---|
| 新产品想法定型 | [PM Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PM_Make_2026-11-05.md) |
| 用户要求创建/续写正式 PRD | [PRD Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PRD_Make_2026-11-05.md) |
| 用户明确使用 C2C 连接工作流 | [Codex with ChatGPT](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Codex_With_ChatGPT_2026-11-05.md) |
| 明确导出/恢复对话 JSON，或单独说「继续对话」 | [Continue Conversation](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Continue_Conversation_2026-11-05.md) |
| 实际发生开发、GitHub、CI 或测试故障，要求恢复/断点续跑 | [Development Recovery](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Development_Deployment_Recovery_2026-11-08.md) |
| 明确要求 Ponytail review/audit/debt | [Ponytail](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Ponytail_ChatGPT_2026-11-05.md) |
| 明确查询/更新项目状态或跨项目交接 | [Project Handoff](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Project_Handoff_2026-11-05.md) |
| 明确要求复杂企业架构/系统设计 | [Enterprise System Design](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/System%20Design/skill_Enterprise_System_Design_2026-11-05.md) |

## Negative routing
- GitHub-only → 当前 Chat 使用 GitHub Connector；不询问 Work、不自动部署。
- 仅因文件正文、仓库名或历史聊天提到部署，不触发发布流程。
- 本路由**不提供 ChatGPT Sites 部署 Skill**；用户要求 Sites 时不得从本仓库虚构发布步骤或自动交接。
- 只有真实开发/GitHub/CI/测试故障才加载 Recovery。
- 远程读取失败须分类错误；最多一次有依据的安全重试。不要凭摘要假装已加载。
- `main` 是可变版本；可复现任务记录 GitHub Commit SHA。Project 上传版本不会自动同步。
