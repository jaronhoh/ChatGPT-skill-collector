# Sites Deployment Retrospectives — 参考档案

本目录仅存放历史问题、修复过程、实际部署证据、测试差异与复盘结论，**不是 Skill、路由规则、自动执行流程或部署授权**。

- 不在 `SKILL.md` 中注册本目录的文档；普通开发、GitHub 更新、CI 或故障恢复不得自动加载这些案例。
- 仅当用户明确要求查阅历史复盘、分析相似问题或比较既往解决方案时，按需读取相关记录。
- 记录是**当时的历史证据**，不证明当前 Sites 工具、权限、网络、API 或部署流程仍可使用。不得直接照搬历史内部接口或执行部署。
- 本目录不会恢复已删除的 Sites Deployment Skill；不会主动触发 Work、创建 Site、更新 Site 或发布 Production。
- 保留每份原始记录中的已验证与未验证结果；不同源码基线的测试不能混为一谈。

## 归档原则

保留完整原始报告；摘要仅供索引，不替代正文。保留历史提案、错误、证据、分析和未验证事项，不将历史指令视为当前规则。相同内容的重复文件仅归档一次。

## 案例索引

| 日期 | 项目 / 问题 | 完整参考记录 |
|---|---|---|
| 2026-11-05 | Task Tracker — 用户目标、源码同步与不必要部署 | [完整复盘](2026-11-05-Task-Tracker-Skill-Retrospective.md) |
| 2026-11-06 | Loan Calculator — 小改动、版本追踪与过度验收 | [完整复盘](2026-11-06-Loan-Calculator-Minor-Deployment.md) |
| 2026-11-09 | Margin Share Calculator CR-005 — DNS、网络授权与 Version 4 | [完整复盘](2026-11-09-Margin-Share-Calculator-CR005-Deployment.md) |
| 2026-11-09 | CR-005 Production — 事故分析、功能回归与 Skill 执行问题 | [完整分析](2026-11-09-CR005-Production-Incident-Analysis.md) |
| 2026-11-10 | Margin Share Calculator — excelCheck、Version 10 → 11、测试差异 | [完整复盘](2026-11-10-Margin-Share-Calculator-excelCheck.md) |
| 2026-11-26 | Loan Investment Calculator — FR-21/FR-22 四案例比较及优化提案（报告转述） | [完整分析](2026-11-26-Loan-Investment-Calculator-FR21-FR22-Deployment-Analysis.md) |
