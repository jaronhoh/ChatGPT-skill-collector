---
name: skill-collector-router
description: 按用户明确目标路由远程 Skill；GitHub-only 全程 Chat；Sites 发布独立触发。
version: '4.0'
updated: '2026-11-09'
---

# Skill Collector — Minimal Router v4

**只按明确交付目标路由**。默认不加载任何专项 Skill；匹配时读取一个，只有实际跨工作流才读取第二个。必须通过 GitHub Connector 或已授权读取工具取得文件正文，记录真实路径/版本；不能凭路由表声称已加载。

| 明确意图 | 远程 Skill |
|---|---|
| 新产品想法定型 | [PM Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PM_Make_2026-11-05.md) |
| 用户要求创建/续写正式 PRD | [PRD Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PRD_Make_2026-11-05.md) |
| 用户明确使用 C2C 连接工作流 | [Codex with ChatGPT](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Codex_With_ChatGPT_2026-11-05.md) |
| 明确导出/恢复对话 JSON，或单独说「继续对话」 | [Continue Conversation](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Continue_Conversation_2026-11-05.md) |
| **明确要求部署/更新实际 ChatGPT Site** | [Sites Deployment](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_PRD_GitHub_Sites_Update_2026-11-05.md) |
| **实际发生故障、要求恢复/断点续跑/CI 恢复** | [Recovery](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Development_Deployment_Recovery_2026-11-08.md) |
| 明确要求 Ponytail review/audit/debt | [Ponytail](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Ponytail_ChatGPT_2026-11-05.md) |
| 明确查询/更新项目状态或跨项目交接 | [Project Handoff](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Project_Handoff_2026-11-05.md) |
| 明确要求复杂企业架构/系统设计 | [Enterprise System Design](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/System%20Design/skill_Enterprise_System_Design_2026-11-05.md) |

## Negative routing and mode rules
- **GitHub-only**（仓库、文件、Commit、PR、Review、CI、合并）→ 直接在当前 Chat 使用 GitHub Connector；**不加载 Sites Skill，不询问 Work，不自动部署**。
- 普通代码修复、测试、审查 → Chat 直接执行，不自动触发 PM/PRD/C2C/Ponytail/Recovery。
- 仓库名称、文件正文或历史聊天提到 Sites/Work **不构成** Sites 部署意图。
- Sites 发布 → Sites Skill；仅真实故障时再读 Recovery。已完成阶段不得重做。
- 用户明确拒绝 Work → 不再次询问；如工具不支持剩余动作，准确报告而非虚构完成。
- 远程读取失败 → 说明未加载，检查连接器/分支/权限，最多一次有证据的安全重试；不得猜测正文。
- GitHub main 可变；必要时记录 Commit SHA。Project 已上传的入口文件不会随 GitHub 自动更新。
