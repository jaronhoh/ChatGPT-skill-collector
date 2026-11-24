# Routing acceptance cases

Manual test fixtures only; not auto-loaded. Run in fresh chats with the current Project root files; record actual loaded path/version, tool actions and deviations. No production actions are required.

| Input | Expected route / behavior |
|---|---|
| 审计整个仓库 | No automatic Ponytail |
| ponytail-audit | Ponytail |
| ponytail lite | Ponytail |
| ponytail-help | Ponytail |
| 修复后端 Bug | No specialist by default |
| 修复响应式 CSS | Modern Web Guidance |
| 更新 GitHub README | No specialist; no deploy |
| GitHub CI 失败，恢复执行 | Recovery |
| 查询项目进度 | Handoff |
| 跨对话项目交接 | Handoff |
| 继续对话 | Continue Conversation |
| 继续开发 | No automatic JSON export |
| 创建正式 PRD | PRD Make |
| PRD 中修正错字 | No redundant confirmation |
| 新产品想法定型 | PM Make |
| 使用 C2C 审查 | C2C |
| 复杂企业权限架构设计 | Enterprise System Design |
| 发布 ChatGPT Sites | No Sites Skill; no invented deployment |

Status: fixtures defined; live behavioral tests not executed. Static verification must check all nine router targets exist, required metadata fields, and absence of obsolete trigger wording. Record results separately.
