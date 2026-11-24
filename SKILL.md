---
name: skill-collector-router
description: 按明确任务选择远程 Skill；GitHub-only 使用当前 Chat；不包含 ChatGPT Sites 部署流程。
version: '4.4'
updated: '2026-11-25'
source_type: original
original_author: ChatGPT-skill-collector
source_url: https://github.com/jaronhoh/ChatGPT-skill-collector/blob/main/SKILL.md
source_status: local_original
source_audit_date: '2026-11-25'
upstream_version: null
upstream_commit: null
license: null
---

# Skill Collector — Minimal Router v4.4

只按用户明确的交付目标加载对应 Skill；普通 GitHub 操作、后端代码修改与测试无需加载专项 Skill。Web 前端 UI / CSS / 浏览器端 JS 开发或审查可按需加载 Modern Web Guidance。必须实际读取匹配文件，记录路径与版本；无法读取时如实报告。

| 明确意图 | 远程 Skill |
|---|---|
| 新产品想法定型 | [PM Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PM_Make_2026-11-05.md) |
| 用户要求创建/续写正式 PRD | [PRD Make](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/PRD/skill_PRD_Make_2026-11-05.md) |
| 用户明确使用 C2C 连接工作流 | [Codex with ChatGPT](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Codex_With_ChatGPT_2026-11-05.md) |
| 明确导出/恢复对话 JSON，或单独说「继续对话」 | [Continue Conversation](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Continue_Conversation_2026-11-05.md) |
| 明确涉及 HTML/CSS、浏览器端 JavaScript、Web UI、响应式布局、前端性能或无障碍的实际开发与审查（不含纯后端/部署） | [Modern Web Guidance — Adapter](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Development/skill_Modern_Web_Guidance_2026-11-25.md) |
| 实际发生开发、GitHub、CI 或测试故障，要求恢复/断点续跑 | [Development Recovery](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Development_Deployment_Recovery_2026-11-08.md) |
| 明确指定 Ponytail 或其 full/lite/ultra/off/review/audit/debt/gain/help 模式（含连字符形式）；普通审查不触发 | [Ponytail](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Ponytail_ChatGPT_2026-11-05.md) |
| 明确查询/更新项目状态或跨对话项目交接 | [Project Handoff](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/Process%20Rules/skill_Project_Handoff_2026-11-05.md) |
| 明确要求复杂企业架构/系统设计 | [Enterprise System Design](https://raw.githubusercontent.com/jaronhoh/ChatGPT-skill-collector/main/System%20Design/skill_Enterprise_System_Design_2026-11-05.md) |

## Negative routing
- GitHub-only → 当前 Chat 使用 GitHub Connector；不询问 Work、不自动部署。
- Modern Web Guidance 仅在相关 Web 前端实现/审查时加载；其上游 CLI 不是 Chat 内置工具，不能假装执行 search/retrieve；不将其规则全局强制到其他专业任务。
- 仅因文件正文、仓库名或历史聊天提到部署，不触发发布流程。
- 本路由**不提供 ChatGPT Sites 部署 Skill**；用户要求 Sites 时不得从本仓库虚构发布步骤或自动交接。
- 只有真实开发/GitHub/CI/测试故障才加载 Recovery。
- 远程读取失败须分类错误；最多一次有依据的安全重试。不要凭摘要假装已加载。
- `main` 是可变版本；可复现任务记录 GitHub Commit SHA。Project 上传版本不会自动同步。

## Skill 来源维护（仅创建或升级 Skill 时适用）
- 每个 Skill 的 front matter 保留 source_type、original_author、source_url、source_status、source_audit_date；统一记录 upstream_version、upstream_commit、license；未知或不适用用 null。upstream_commit 是已审阅上游基线，不代表原始导入版本。derived 表示多来源方法论提炼。
- 来源不明时标记 unknown；本仓库 URL 仅代表本地维护地址，不冒充原始出处。候选上游需明确标注未确认的历史关联。
- 升级时查阅已记录上游、许可证及变更差异，保留本地适配约束；不盲目覆盖、不自动更新、不在普通开发时触发来源审计。
