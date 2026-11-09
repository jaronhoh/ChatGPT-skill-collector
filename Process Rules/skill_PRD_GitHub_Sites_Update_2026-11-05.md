---
name: prd-github-sites-update
description: 用户要求更新现有 PRD 并同步 GitHub，或核验、执行、交接既有 Sites 发布时使用。
version: '2.2'
created: '2026-10-16'
updated: '2026-11-09'
---

# PRD → GitHub → ChatGPT Work → Sites

> 通用执行边界、按需加载与完成标准以仓库根目录 `AGENTS.md` 为准；不必为普通任务预先读取其他 Skill。

## 0. Work 部署指令自动交付（强制）

**触发条件**：用户的交付目标包含「部署 ChatGPT Sites」「更新原 Production Site」「修复并上线」「继续完成 Production」，即使没有单独说「生成 Work 指令」，也必须主动处理发布。仅当剩余步骤确实需要 Work 且其能力已核实，才主动交付部署指令。

1. 在 Chat 内完成并核验本次适用的需求/代码/测试/GitHub 工作；先复用已验证 SHA、CI 与历史成功部署路径，不无条件重跑完整测试。
2. **当剩余任务是 Sites 发布或线上验证、且需要已核实的 Work 能力时，在同一次 Chat 回复主动给出完整、可直接复制到 Work 的指令**。至少包括：项目、仓库/分支、精确 GitHub SHA、已验证测试、未验证项、现有 Site URL/ID（若已知）、历史成功部署证据、保护 URL/Owner-only/数据/配置、只部署未完成步骤、Production 验证、部署失败诊断与安全恢复。
3. 路径失败时保留 checkpoint，按 Recovery §0.2 继续适用备选路径。仅交接 Work 可执行的剩余步骤。Commit、Deployment ID、工具能力和测试结果均须有证据。
4. **不得自动切换 Work、不得让 Work 重做开发、不得把生成指令当作 Production 已上线。**
5. 如果用户只要求 PRD、GitHub 或文档更新，没有 Sites 发布意图，则不强制生成 Work 指令。

## 1. 目标与执行边界

本 Skill 适用于用户要求「更新 PRD → 更新 GitHub → 给 ChatGPT Work 一条更新 Sites 的指令」。**首先判断用户实际业务终点；以下链路是可选路径，不是每次必须全部执行的硬性门槛。若现有 Site 已满足需求且用户不要求额外发布，应直接进入状态收尾。** 参考链路：

`变更需求 → 影响分析 → 用户确认 → 更新 PRD → GitHub Commit/PR → 验证来源 → 生成 Work 指令 → Work 仅部署已验证版本 → 更新现有 Site → 生产验证 → 更新项目状态`

**生成 Work 指令不等于执行 Work，也不等于 Sites 已部署。** 若用户只要求指令，不得擅自部署。PRD 文档更新也不代表代码已实现。

本 Skill 保留 PRD→GitHub→Sites 的发布范围与证据规则。遇到发布预检无法闭合、发布失败，或用户明确要求自动恢复、CI、Release Manifest 或 Recovery Checkpoint 时，按需读取 [Development & Deployment Recovery](skill_Development_Deployment_Recovery_2026-11-08.md)，不要每次发布都强制加载。

## 2. 项目文件结构

沿用现有仓库目录；没有既有约定时采用：

```text
<ProductName>/
├── 01-产品定义/
│   └── 06-最终定型稿.md
├── 02-PRD/
│   ├── PRD文档.md
│   ├── 模块规划.md
│   ├── 待决策事项.md
│   └── 追加日志.md
├── 03-Prototype/
├── 04-Development/
├── 05-Release/
└── docs/
    └── PROJECT_STATUS.md
```

如已有等效状态文件则复用，不重复创建。**不要在 GitHub 或 Google Drive 项目目录中新增 `skills/`、`templates/` 文件夹。** 不将本 Skill 放进项目仓库，除非用户明确要求。

## 3. 启动时的必需检查

1. 识别产品、GitHub repository、默认分支、目标分支、现有 Sites 项目及环境。
2. 从实时 GitHub 读取 PRD、模块规划、待决策事项、追加日志、冻结版产品定型稿（若存在）和 `docs/PROJECT_STATUS.md`。
3. 检查 HEAD SHA、相关 PR、最近提交、CI 和上次发布记录。
4. 交叉核对用户提出的变更与已确认需求、既有代码和部署记录。
5. 标注每条重要结论的证据等级：`GitHub verified`、`Sites verified`、`User reported`、`Not verified`。
6. 如果缺少仓库或 Sites 访问权限，不假装读取成功；先完成可执行部分，其他使用 `【待提供】` 占位符。
7. 在动手前说明当前状态、最后一次已验证发布、阻塞事项和下一步。
8. 先确认用户的实际业务终点与现有 Site 是否已满足；不得默认 GitHub 有新提交就必须发布。
9. 核对精确 PR Head 的 GitHub Actions（若适用），并验证测试证据适用于待发布 SHA。Legacy statuses 空列表不足以判断 Actions 缺失。

### 3.1 既有 Sites 发布路径预检（开发前）

仅在用户目标包含 Sites 发布时执行。新变更在开发前预检，已实现的变更从 checkpoint 续跑。以只读证据验证每一环，实际发布时再核对待发布 SHA 和权限。

1. 读取实时 Sites 项目与当前 Production 记录，核对用户指定 URL、Site ID、环境、当前版本和访问策略。分别记录 GitHub 仓库/分支/基线 SHA，以及 Sites 实际绑定的 source repo 或受支持的产物入口、分支/HEAD 和已发布版本的来源。独立绑定仓库需单独验证。历史记录与 `hosting.json` 只提供线索，不能单独证明绑定关系、Site ID 或写权限。
2. 验证 GitHub 精确 SHA 如何同步到该 Site 的绑定源码，或如何经平台支持的入口交付可追溯产物。核对当前环境的工具参数、目标与权限，并查证该 Site 历史成功入口是否仍可用。读取 GitHub 或拥有 GitHub 写权限不足以证明能写 Sites 绑定源码。
3. 核对该原 Site 的 Save Version、Deploy、发布状态查询及 Production 验收能力（包括按项目需要的受保护访问及只读数据检查）。明确每一步的执行环境、已确认能力与尚需授权的动作。预检不写源码、不保存版本、不部署、不触碰生产数据。
4. 记录链路 `GitHub → 实际 Sites source/产物 → Save Version → Deploy → Production 验收` 的各环证据与缺口。发现缺口时先按 Recovery §0.2 排查备选路径；未闭合时继续用户授权的独立工作，将发布标为待核验，禁止把 GitHub 合并或盲目交接当作可发布的证明。

## 4. 阶段 A：更新 PRD

### A1. 影响分析

根据用户变更、现有 PRD、冻结稿、模块规划，形成：

| 变更 ID | 变更描述 | 影响模块/功能 | 页面/流程/数据/权限 | 验收标准影响 | 确认状态 |
|---|---|---|---|---|---|
| CR-001 | | F01 / F01-01 | | AC-F01-01-01 | Pending |

发现冲突、范围变化或关键规则不明确时，记录在 `待决策事项.md`，不得用模型假设代替用户确认。

### A2. 用户确认门槛

- 对既有 PRD，先识别实际影响。用户明确要求的可逆文档修改直接完成；重大范围、权限、生产数据或不可逆操作的未决选择再询问。
- 对新 PRD，先形成模块规划；已明确的功能可继续写作，不因形式性确认停工。
- 纯排版或拼写修改可在用户授权下直接处理；语义性修改仍须确认。
- 用户要求自动继续时可以处理低风险步骤，但高风险或冲突决策必须暂停。

### A3. 修改文件

- `PRD文档.md`：按既有模板修改功能目标、页面与交互、状态、业务规则、数据要求、验收标准、权限。
- `模块规划.md`：同步模块范围、功能列表、依赖、开发顺序。
- `待决策事项.md`：记录 D001、D002……，包括来源、问题、影响、临时假设、负责人、状态和关闭日期；已关闭事项不删除。
- `追加日志.md`：追加时间、章节、动作、评审状态和备注。
- 保持既有编号：模块 `F01`、功能 `F01-01`、验收 `AC-F01-01-01`。不随意重排已发布 ID。
- 不覆盖与本次修改无关的已确认内容。

### A4. PRD 质量检查

- 来源可追溯，模块规划与正文一致。
- 需求、页面、状态、权限、数据、异常和验收标准一致且可测试。
- 编号无重复、无断裂、无错误引用。
- 未决事项没有隐藏在正文中。
- 追加日志记录每次修改及确认。

完成后在聊天中给出**修改摘要、影响模块、验收标准、待决策事项和需要确认的内容**，而不是仅让用户打开文件。

## 5. 阶段 B：更新 GitHub

1. 在已获授权的范围内修改；写入前读取目标分支最新 HEAD 与文件 SHA，避免覆盖并发修改。
2. 遵守仓库现有分支保护与协作规范；优先在变更分支提交并创建 PR。
3. 仅提交本次已批准的 PRD、规划、待决策事项、追加日志和必要状态记录。
4. 检查 Markdown、相对链接、Mermaid、编号、diff、意外删除和敏感信息。
5. 使用清楚的 Commit 信息，例如 `docs(prd): update F01 requirements and acceptance criteria`。
6. 核实真实 Commit SHA、目标 branch、PR URL、审查/CI 状态、是否合并。不得把 PR 创建当成已合并。
7. 更新 `docs/PROJECT_STATUS.md` 的 PRD / GitHub 阶段状态，追加事实日志。开发、测试和 Sites 部署若未执行，应保持 `Not started`、`Pending` 或 `Not verified`。
8. 写入失败时提供补丁或待执行内容，并明确报告失败，不得声称已同步 GitHub。

**交接给 Work 必须包含可验证的 Commit SHA。** 若 PR 未合并，应明确注明；不能将待审查分支误作生产发布来源。

## 6. 阶段 C：生成 ChatGPT Work 指令

仅当用户仍需 Work 且已确认必要工具能力时，此阶段输出一份**可直接复制粘贴到 ChatGPT Work 的完整指令**，而不是一句「请更新 Sites」。必须包含：

- Repository、目标分支、已验证 Commit SHA、PR/合并状态。
- 现有 Sites 的项目标识、URL 和目标环境；未知则使用占位符。
- PRD、模块规划、待决策事项、追加日志、项目状态的真实路径。
- 已确认变更、明确不做范围、功能 ID、验收标准 ID。
- 已在 Chat 完成的代码、测试、审查证据，以及 Work 仅需执行的部署前确认与部署后验证。
- 保护现有 Site URL、数据、编号、配置、Secrets 和访问权限。
- 失败后按 Recovery §0.2 诊断与续跑。仅在需要新增授权或重大风险决策时询问用户。
- 交付证据与 `PROJECT_STATUS.md` 回写要求。
- **Work 对话命名（必须置于完整 Work 指令的最末尾）**：要求将当前 Work 对话标题设为以精确前缀 `Work | ` 开头，建议格式 `Work | 【项目名称】 | 【任务摘要】`；若已有此前缀，不得重复添加。仅在界面或工具实际允许时执行重命名；无法操作时明确报告，不能声称已完成。

如果 GitHub 只更新了 PRD，在代码实现与必要测试就绪前不生成发布交接。

### 标准 ChatGPT Work 指令模板（仅部署；以下步骤以本节强制约束为准）

生成时以真实已验证值替换方括号；无法确认则保留 `【待提供】`。

```text
请在 ChatGPT Work 中更新【产品名称】的现有 ChatGPT Site，按照下列流程执行。不要创建新 Site，除非我明确批准。

【项目】
GitHub Repository：【Repository URL】
默认分支：【Default Branch】
目标分支：【Target Branch】
已验证 PRD Commit SHA：【Commit SHA】
PR / Merge 状态：【Merged / Pending merge / No PR / Not verified】
PRD 文件：【PRD Path】
模块规划：【Module Plan Path】
待决策事项：【Decision Log Path】
追加日志：【Append Log Path】
项目状态：【Status File Path】
现有 Site：【Site URL / 已核实 Site ID】
实际绑定源码或平台产物入口：【Repo / Branch / HEAD 或入口及证据】
可用能力与发布授权：【Sync / Save / Deploy / Read-back / Verify】
恢复 checkpoint：【最后成功阶段 / 证据 / 待执行步骤 / 已排除路径】
目标环境：【Development / Staging / Production】
上次已验证发布：【Last Verified Release / Unknown】

【本次已确认的修改】
【Change Summary】
涉及功能：【FXX / FXX-YY】
验收标准：【AC-FXX-YY-ZZ】
不在本次范围：【Out of Scope】

【执行步骤】
1. 先读取 GitHub 实时仓库、指定 Commit、PRD、模块规划、待决策事项和 PROJECT_STATUS.md；核对本次需求是否已获确认。
2. 检查当前代码、PR/合并状态、CI、现有 Site 和上次发布来源。报告差异、风险和阻塞事项。
3. 复用已验证的实现与 GitHub SHA。若证据确实表明当前 SHA 缺少目标实现，记录具体差异并停止发布该 SHA。Work 的源码操作限于授权的发布同步或打包，不改产品实现。
4. 核验 Chat 已完成的按风险分级的必要测试证据；若证据缺失，先从精确 SHA 对应 CI、已有测试产物或 checkpoint 找回。确实缺少必需证据则记录缺项，暂停该发布动作。禁止因 Sites 故障要求 Chat 重复开发或重测。
5. 只核验现有 PR、Merge 和 CI 证据；Work 不创建或更新 PR、不提交或合并代码。未合并 PR 不得擅自作为生产发布来源。
6. 部署前说明源 Commit、修改范围、测试结果、生产风险和回滚方案。破坏性迁移或高风险生产变更须先获得我明确批准。
7. 从第一个未完成的阶段继续。核实原 Site ID 和绑定来源，按授权同步精确源码或提交受支持的产物并回读对应关系，Save Version 后回读版本及来源，再 Deploy 到同一 Site 并确认激活状态。保留 URL、生产数据、数据库标识与序列、配置、Secrets、访问权限和既有功能。
8. 部署后实际验证 Site URL、功能行为、验收标准、数据完整性、关键配置和访问权限；不得仅凭 GitHub merge 推断部署成功。
9. 报告实际发布和验证证据。若约定由 Chat 维护 PROJECT_STATUS.md，仅交付证据供状态回写，发布任务留在 Work。Work 不提交 GitHub。
10. 最终报告：Commit/PR 链接、测试结果、实际部署源 Commit、Site 版本、部署时间和时区、Site URL、验证证据、未完成事项与下一步。

【禁止事项】
- 不得将 GitHub 更新或合并等同于 Sites 已部署。
- 不得把未确认需求变成已批准功能。
- 不得无授权删除生产数据、重置编号、替换 Site、修改访问权限。
- 不得编造测试、部署或验证结果。
- 单一路径失败时保存 checkpoint，按 Recovery §0.2 尝试其余合法路径。仅在全部适用路径经核实失败或缺少必要用户授权时标记剩余阶段 BLOCKED。冲突或发布状态不明时先只读核查，重大风险按授权边界暂停相应动作。

【Work 对话命名｜最后一条指令】
请将当前 ChatGPT Work 对话的标题修改为以 `Work | `（含前后空格规则中的末尾空格）开头，格式为：`Work | 【项目名称】 | 【任务摘要】`。如果标题已以 `Work | ` 开头，不要重复添加前缀。若当前界面或工具不支持实际重命名，请明确说明无法修改，不得声称已修改成功。
```

## 7. Project Handoff

项目状态使用 [Project Handoff](skill_Project_Handoff_2026-11-05.md) 的规范；仅在本任务涉及状态更新时读取，不重复维护状态模板。

## 8. 阶段验收清单

### PRD
- [ ] 读取既有文件与冻结版定型稿。
- [ ] 完成影响分析及必要的用户确认。
- [ ] 更新 PRD、模块规划、待决策事项和追加日志。
- [ ] 编号、业务规则、状态、权限、验收标准一致。

### GitHub
- [ ] 核对最新 HEAD/SHA，避免覆盖。
- [ ] 只提交已确认变更。
- [ ] 核实 Commit SHA、branch、PR 和合并状态。
- [ ] 项目状态真实反映 GitHub 阶段。
- [ ] 未成功的写入明确报告。

### Work 交接
- [ ] 指令包含 GitHub、PRD、Commit、现有 Site、功能与验收范围。
- [ ] 区分 PRD 更新、代码实现、测试、部署、验证。
- [ ] 保护生产数据、配置、URL、权限。
- [ ] 明确授权边界、备选路径、停止条件、回滚与状态回写。
- [ ] 不声称 Work 已执行或 Sites 已更新。
- [ ] 已确认真实业务终点；如 User confirmed — no deploy needed，停止额外发布。
- [ ] 已核对精确 Head 的 Actions（若适用），并分别标记 Sites source mapping unverified、No new Sites version published 等状态。
- [ ] 已核实 Work 对原 Site 托管源码的实际操作能力，避免重复同质交接。
- [ ] Work 指令最末尾附上 `Work | ` 对话标题重命名要求，并说明无法控制原生标题时应如实报告。

## 9. 最终回复格式

**PRD 更新**：修改文件、模块、验收变化、确认状态。

**GitHub 更新**：Repository、Branch、Commit SHA、PR URL、Merge 状态和验证等级。

**ChatGPT Work 指令**：给出完整可复制的指令。

**尚未完成的动作**：开发、测试、Sites 部署、生产验证的真实状态及下一步。

