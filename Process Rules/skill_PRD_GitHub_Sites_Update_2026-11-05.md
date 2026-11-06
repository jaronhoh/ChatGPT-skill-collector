---
name: prd-github-sites-update
description: 用于更新既有 PRD、将已确认修改提交 GitHub，并生成完整的 ChatGPT Work 指令以更新现有 Sites；包含版本验证、交接、发布及状态追踪。
version: '1.4'
created: '2026-10-16'
updated: '2026-11-05'
---

# PRD → GitHub → ChatGPT Work → Sites

## 强制执行模式：Chat 开发，Work 仅用于 ChatGPT Sites 部署（2026-11-05）

> **最高优先级项目工作流约束：所有开发工作必须始终在 ChatGPT 普通 Chat 模式进行。ChatGPT Work 只允许用于 ChatGPT Sites 的实际部署、发布以及与该次部署直接相关的发布后验证；不得用于需求分析、PM/PRD、系统设计、UI 设计、编写/修改代码、修复 Bug、测试、代码审查、GitHub 提交或合并。** 本规则覆盖本文件中任何较宽松的“Chat First”“必要时 Work 开发/测试”“Work 可处理浏览器或本地代码”表述。

- **Chat 阶段**：需求、设计、开发、调试、测试、回归、GitHub 同步及审核全部在普通 Chat 完成。可以在 Chat 中使用当前授权的工具和连接器；Codex 等独立开发执行端若需配合，也必须由 Chat 主导，不把开发任务交给 Work。
- **能力不足时**：在 Chat 中如实说明无法执行的操作、缺失证据和阻碍，继续寻求 Chat 可用的工具或请求用户提供环境；**不得以工具不足为理由改用 Work 开发或测试**，不得把未执行的测试写成通过。
- **唯一 Work 入口**：仅在源码已准备、必要测试完成且用户明确要求部署/更新 ChatGPT Sites 时，交给 Work **最小部署范围**：定位原 Site → 同步已验证的 GitHub 源码/构建产物 → 保存/发布 → 检查发布后实际运行。Work 不得重新设计、开发、修复功能或替代 Chat 完成预发布测试。
- **部署遇阻**：如果 Work 发现需要代码修改或修复，停止该部分并把问题返回 Chat；Chat 修复和验证后，才重新启动部署。若 Sites 工具不可用，如实报告，不假装发布成功。
- **不需要部署**：任务终点只是设计、代码、GitHub 更新或用户确认无需发布时，整个流程都不进入 Work。
- **状态分离**：Chat 开发完成、GitHub 提交、CI、Sites 发布及生产验证必须分别有证据；GitHub Commit/Merge 不等于 Sites 部署。

**固定流程：PM Make → PRD Make → 系统/UI 设计 → Chat 开发 → Chat 测试 → Chat GitHub 更新与核验 → [用户要求部署 Sites 时] Work 部署与发布验证。**

## 默认开发执行策略：ChatGPT Chat First（历史规则；以以上强制模式为准）

> **用户已确认的新规则：开发与测试优先在 ChatGPT 普通聊天中完成；只有确实需要时才使用 ChatGPT Work；通过验证后才部署到 ChatGPT Sites。** 此规则适用于新项目和既有项目，并覆盖本 Skill 中任何“默认先交给 Work 开发”的旧建议；不覆盖用户针对单次任务的明确指令。

1. **规划与设计优先 Chat**：需求澄清、PM/PRD、架构讨论、UI 设计、代码方案和审查优先在普通聊天完成。Web UI 默认优先评估 [shadcn/ui](https://ui.shadcn.com/) 和兼容的 Tailwind CSS；不适用时解释原因。
2. **开发优先 Chat**：先使用当前聊天实际可用的工具、连接器及代码能力推进实现、修复与迭代。能在 Chat 完成的，不因任务属于“开发”就主动转交 Work；但不得假设 Chat 一定具备本地仓库写入、终端或运行时能力。
3. **测试优先 Chat**：先在聊天环境中进行可实际执行的验证、代码审查和回归检查。区分 `实际执行并通过`、`执行失败`、`仅静态审查`、`测试方案未执行`；不得将生成代码或建议测试命令称为已测试通过。
4. **仅在必要时使用 Work**：当需要的本地文件/仓库编辑、浏览器交互、运行命令、端到端测试或部署无法由当前聊天工具完成时，才将**最小必要范围**交给 Work 或其他具备权限的执行端。Work 只处理尚未完成的步骤，不重复已验证工作；遵守当前平台的工具路由限制。
5. **GitHub 源码管理**：优先用 GitHub 保存与核对代码、PRD、Commit、PR、测试证据；提交或合并不等于已发布。未真正写入或验证时标记未完成。
6. **Sites 最后部署**：开发完成且必要测试有真实通过证据后，才进入 ChatGPT Sites 部署。先验证 Sites 技术兼容性；更新既有项目时保留原 Site、URL、数据、权限和配置，必要时准备回滚与取得授权；部署后实际验证运行状态。
7. **阻塞处理**：若聊天无法执行必要测试或缺乏真实环境，明确列出缺口；不可因“Chat 优先”而跳过测试或假称具备执行能力。必要时按上述最小范围升级到 Work，再部署。

**默认顺序：** PM Make → PRD Make → shadcn/ui（适用时）→ Chat 开发 → Chat 测试与审查 → GitHub 同步与核验 → Sites 部署 → 上线验证。Work 不是默认阶段，而是按需的执行补充。


## 默认 Web UI 与发布平台（2026-10-21 新增）

> 本节适用于**新开发的 Web 项目**；如果用户明确指定其他 UI 技术、平台，或既有项目已有经确认的技术决策，以用户明确要求和项目既定约束为准。不要把“默认优先”理解成无条件强制。

- **UI 首选**：优先评估并使用 [shadcn/ui](https://ui.shadcn.com/) 的适用组件，配合与项目兼容的 Tailwind CSS；优先复用组件而非重复造轮子，保持一致的视觉规范、响应式布局、键盘操作和无障碍体验。
- **适用性检查**：确认前端框架、组件依赖、构建方式、许可、维护成本及既有设计系统是否兼容。若不适合，说明原因、影响与替代方案，不未经确认更换既有项目 UI 技术栈。
- **默认发布目标**：新 Web 项目优先规划发布到 **ChatGPT Sites**；开发前实际核实目标 Sites 环境对框架、构建产物、路由、API、数据库、鉴权、环境变量、密钥、持久化及自定义域名等能力的支持。未经验证不得承诺某项 Sites 能力可用。
- **源码管理**：优先将 GitHub 作为源码与版本追踪来源；项目若有已确认的其他仓库策略，遵循既有约定。
- **部署边界**：规划、提交、合并、测试、Sites 部署、上线验证是不同阶段；只有真实执行且有证据时才标记完成。需要发布权限或操作工具时，应说明实际能力边界，不能将生成部署指令等同于已部署。
- **更新现有 Site**：默认沿用现有 Site 与 URL，保护生产数据、权限、配置、Secrets 和既有功能；破坏性更改、数据清空或高风险生产发布需明确授权及回滚方案。
- **项目记录**：在产品定型稿、PRD、技术方案或交接指令中，分别注明 UI 选择、Sites 可行性检查结果、尚未验证的限制及实际发布状态。

**本 Skill 的具体应用：**PRD → GitHub → Sites 交接指令必须明确：优先沿用兼容的 shadcn/ui 组件；在实施前核验 Sites 支持情况；测试与发布分开报告；部署必须以现有 Site 为目标并核实结果。

## 1. 目标与执行边界

本 Skill 适用于用户要求「更新 PRD → 更新 GitHub → 给 ChatGPT Work 一条更新 Sites 的指令」。**首先判断用户实际业务终点；以下链路是可选路径，不是每次必须全部执行的硬性门槛。若现有 Site 已满足需求且用户不要求额外发布，应直接进入状态收尾。** 参考链路：

`变更需求 → 影响分析 → 用户确认 → 更新 PRD → GitHub Commit/PR → 验证来源 → 生成 Work 指令 → Work 仅部署已验证版本 → 更新现有 Site → 生产验证 → 更新项目状态`

**生成 Work 指令不等于执行 Work，也不等于 Sites 已部署。** 若用户只要求指令，不得擅自部署。PRD 文档更新也不代表代码已实现。

本 Skill 自包含关键流程，不依赖其他 Skill 文件运行；原则继承 PRD Make 的模块化评审、待决策和追加日志机制，以及 Project Handoff 的跨对话状态和证据规则。

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
9. 在安排 Work 前确认原 Site 托管源码编辑/同步、版本保存/发布及 D1 只读工具是否存在；检查精确 PR Head 的 GitHub Actions，不能以 legacy statuses 空列表判 CI 缺失。

## 4. 阶段 A：更新 PRD

### A1. 影响分析

根据用户变更、现有 PRD、冻结稿、模块规划，形成：

| 变更 ID | 变更描述 | 影响模块/功能 | 页面/流程/数据/权限 | 验收标准影响 | 确认状态 |
|---|---|---|---|---|---|
| CR-001 | | F01 / F01-01 | | AC-F01-01-01 | Pending |

发现冲突、范围变化或关键规则不明确时，记录在 `待决策事项.md`，不得用模型假设代替用户确认。

### A2. 用户确认门槛

- 对既有 PRD，先给出修改范围、关键业务规则、影响和待决策清单；涉及产品行为、权限、数据模型、MVP 范围、状态流转、优先级或验收标准时，必须取得用户确认。
- 对新 PRD，先完成 `模块规划.md` 并取得确认，才开始正式功能正文。
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

1. 用户确认变更后，重新读取 GitHub 目标分支 HEAD 和文件 SHA，避免覆盖并发修改。
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
- 失败/冲突/高风险修改时暂停并询问用户。
- 交付证据与 `PROJECT_STATUS.md` 回写要求。
- **Work 对话命名（必须置于完整 Work 指令的最末尾）**：要求将当前 Work 对话标题设为以精确前缀 `Work | ` 开头，建议格式 `Work | 【项目名称】 | 【任务摘要】`；若已有此前缀，不得重复添加。仅在界面或工具实际允许时执行重命名；无法操作时明确报告，不能声称已完成。

如果 GitHub 只更新了 PRD，**不得直接部署**；返回 Chat 完成代码实现与必要测试，再考虑最小部署交接。

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
现有 Site：【Site URL / Project ID】
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
3. 若代码尚未实现 PRD 修改，停止部署并返回 Chat 实现；Work 不得修改源码。
4. 核验 Chat 已完成的按风险分级的必要测试证据；缺失关键测试则停止部署并返回 Chat，不得在 Work 补做开发阶段测试。
5. 只核验现有 PR、Merge 和 CI 证据；Work 不创建或更新 PR、不提交或合并代码。未合并 PR 不得擅自作为生产发布来源。
6. 部署前说明源 Commit、修改范围、测试结果、生产风险和回滚方案。破坏性迁移或高风险生产变更须先获得我明确批准。
7. 在具备权限且满足部署条件后，更新【现有 Site】，保留原 URL、生产数据、数据库标识与序列、配置、Secrets、访问权限和既有功能。
8. 部署后实际验证 Site URL、功能行为、验收标准、数据完整性、关键配置和访问权限；不得仅凭 GitHub merge 推断部署成功。
9. 将 Sites 实际发布和验证证据返回 Chat，由 Chat 负责按项目状态规则更新 GitHub PROJECT_STATUS.md；Work 不提交 GitHub。
10. 最终报告：Commit/PR 链接、测试结果、实际部署源 Commit、Site 版本、部署时间和时区、Site URL、验证证据、未完成事项与下一步。

【禁止事项】
- 不得将 GitHub 更新或合并等同于 Sites 已部署。
- 不得把未确认需求变成已批准功能。
- 不得无授权删除生产数据、重置编号、替换 Site、修改访问权限。
- 不得编造测试、部署或验证结果。
- 遇到权限不足、工具不支持、冲突或重大风险时，暂停相应操作并说明原因。

【Work 对话命名｜最后一条指令】
请将当前 ChatGPT Work 对话的标题修改为以 `Work | `（含前后空格规则中的末尾空格）开头，格式为：`Work | 【项目名称】 | 【任务摘要】`。如果标题已以 `Work | ` 开头，不要重复添加前缀。若当前界面或工具不支持实际重命名，请明确说明无法修改，不得声称已修改成功。
```

## 7. Project Handoff：状态文件规范

默认复用 `docs/PROJECT_STATUS.md`，至少包含：

```markdown
# <Project Name> — Project Status & Handoff

> Last updated: YYYY-MM-DD HH:mm (timezone)

## Project identity
- Repository:
- Default branch:
- Canonical status file:
- Production Site / environment:

## Current status
| Area | Status | Evidence | Updated |
|---|---|---|---|
| Requirements / PRD | | | |
| Development | | | |
| Automated tests | | | |
| Pull request / merge | | | |
| Production deployment | | | |
| Production verification | | | |

## Latest release
- Version:
- Source commit:
- Release date/time and timezone:
- Deployment status:
- Production URL:
- Test summary:
- Data / configuration / permissions verification:
- Evidence classification:

## Open work and blockers
| Priority | Item | Owner | Next action | Evidence |
|---|---|---|---|---|

## Handoff notes
- Important decisions:
- Do-not-change items:
- Relevant document paths:
- Known risks:
- Next action:

## Change log (append-only)
| Date/time | Change | Result | Commit / PR / deployment | Evidence source |
|---|---|---|---|---|

## Next update
- Trigger:
- Responsible party:
```

更新触发：PRD 范围获批、开发里程碑、测试、PR、合并、部署、生产验证、阻塞变化。只记录真实成功的动作；`Latest release` 保留最后一次已验证成功发布，不能被未部署开发版本覆盖。追加日志不可擦除；不得保存密码、API keys、tokens。更新前读取最新 SHA，处理并发修改。

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
- [ ] 明确风险确认、失败暂停、回滚与状态回写。
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

## 10. 2026-11-05 Task Tracker 复盘：结果优先与发布控制（强制）

> 来源：用户提供《2026-11-05 Task Tracker 工作复盘与 Skill 改进规范》。本章覆盖与之冲突的旧线性模板。复盘不是新的生产部署授权；不自动修改 Task Tracker Site、D1 或业务仓库。

### 10.1 业务结果优先于流水线
- **MUST** 在规划发布前确定用户真实终点：`需求/代码更新`、`GitHub 合并`、`既有 Site 功能验收`、`必须发布新 Sites 版本` 或 `问题修复`。
- **MUST** 对既有 Site 先检查当前功能或取得用户确认。若用户确认现有 Site 满足需求，且没有明确要求强制同步或发布，**STOP RELEASE**：只做适当状态收尾，不继续源同步、保存版本或部署。
- 用户验收仅记 `User confirmed`，不得推导 `Sites deployed`、`Source SHA matched`、`Production QA complete`。用户明确要求实际发布时尽力执行，但不能伪报工具能力。

### 10.2 独立记录五种状态
| 状态维度 | 可用值 | 证据 |
|---|---|---|
| Requirement / Functional acceptance | Pending / User confirmed / Tested | 用户原话、验收记录 |
| GitHub Code / PR | Branch / Ready / Merged | PR、Commit SHA |
| Automated CI | Passed / Failed / Not checked | 精确 Head 的 Actions Run ID |
| Sites deployment | Deployed / Not deployed / Not requested / Unknown | Sites 原生发布记录 |
| Sites source mapping | Verified / Unverified / Unsupported | 原 Site 的源码入口、分支及 HEAD 映射 |

`Merged` ≠ `Deployed`；`User confirmed` ≠ `Merged source running on Site`；`Not deployed` 不表示用户功能不可用。证据来源分别标注 `GitHub verified`、`Sites verified`、`Work reported`、`User confirmed`、`Not verified`。

### 10.3 Work 交接前的工具能力探查
- **MUST** 先核实原 Site 的托管源码编辑入口、源分支查看与同步、保存版本、发布版本及 D1 只读访问是否真实可用；区分查看/发布已保存版本与实际更新托管源码。
- **MUST** 固定正确的 Repo、Source subdirectory、Sites Project ID 和 Site URL；不得误入其他项目或创建新 Site。
- `sites/react-d1/.openai/hosting.json` 中 `project_id` 只证明目标标识，不能证明 GitHub→Sites 已关联、可编辑或可自动部署。
- 同一工具缺口不得重复发送同质 Work 指令；只有出现新的工具、权限、入口或授权才重试。交接最小、明确可执行的剩余工作。

### 10.4 GitHub CI 与合并验证
- **MUST** 读取 PR 最新 `head_sha`，核对**精确 Head** 的 GitHub Actions workflow runs、jobs、conclusions 和必要测试摘要。
- Legacy combined commit `statuses: []` 不代表 Actions 未运行；不得据此否认绿色 Actions。
- 合并前检查 Draft/Ready、mergeable、最新 Head 测试和 diff；合并后记录 Merge SHA，必要时比较 Head 与 Merge 的文件差异。零内容差异仍不等于 Sites 已发布；不为通过虚构检查而改代码或无意义重测。

### 10.5 Sites SHA 与发布真实来源
- **MUST** 分开记录 `GITHUB_HEAD_SHA`、`GITHUB_MERGE_SHA`、`SITES_SOURCE_SHA`、`SITES_DEPLOYMENT_VERSION`，不得互换。
- Sites Source SHA 不在 GitHub 中，只能标记 `Source relationship unverified`，不能断言绑定错误；不得拿任意 GitHub SHA 冒充 Sites 托管分支 HEAD 传入保存版本 API。
- 只有实际通过原 Site 支持的工具保存并发布成功，才可记录新的 Sites Version / Deployment ID。Work Prompt、GitHub Merge、只读检查均不算发布。

### 10.6 风险检查按影响分类
- **必须阻止**：可能覆盖/删除/迁移/暴露生产数据且未获授权；Site/DB/Owner-only 目标不明；必需工具不可用；必要回归失败；待发布代码来源无法证明。
- **按风险记录并另行安排**：无 Schema Migration 的普通功能修改中无法完成完整备份、非关键长期监控、灾备恢复演练、未约定为发布门槛的实体手机测试。不得假称这些已完成，也不得一律升级为硬阻碍。
- D1 只读行数、字段快照和可恢复离线备份是不同证据。用户确认无需额外发布时，不应启动生产变更。

### 10.7 阻塞处理必须收敛
每个阻塞只记录：**缺失的具体操作／已试工具与真实错误／已完成部分／真正需要的权限或平台能力／用户是否仍需此操作**。
- 无新事实时停止重复交接和改写同质 Work Prompt；不虚构工具能力。
- 用户变更目标、确认功能已满足或决定不发布时，立即重算剩余范围，清理过期阻塞。需要决策时只问一个真正改变下一步的问题。

### 10.8 GitHub 收尾与 Issue 处理
- **MUST** 在 `docs/PROJECT_STATUS.md` 顶部记录最新有效结论，保留旧尝试在追加日志，并注明旧部署阻碍何时被用户新决定取代。
- 取消的额外部署 Issue 可标记 `Not Planned`，不能标记 `Completed / Deployed`；混合 Issue 中未验收的安全、备份或手机测试须明确标记未验证，必要时单独追踪。
- 报告分别陈述 GitHub verified、Sites verified、Work reported、User confirmed、Not verified；不得因源映射未核实否认用户实际验收。

### 10.9 回归场景（Skill 自检）
- 用户称现有网站已有所需功能 → 若不要求强制发布，停止额外部署、记录 User confirmed。
- Legacy Commit Status 空 → 查询精确 Head 的 Actions，不直接判 CI 失败。
- PR 已合并但 Sites 版本旧 → 分开记录状态，确认是否需要发布。
- Work 无原 Site 托管源码编辑工具 → 明确缺口并收敛，不重复交接。
- Sites SHA 不在 GitHub → 映射未核实，不推断绑定错误。
- hosting.json 有 project_id → 仅确认目标标识，不推断同步能力。
- D1 有只读行数 → 不是备份；按实际风险处理。
- 用户只要求 GitHub 状态更新 → 不自动发布 Sites。

## 11. 本 Skill 变更记录
- 2026-11-05 · v1.3 · 根据 Task Tracker 工作复盘增加业务终点、五维状态、工具能力检查、精确 Head CI、跨系统 SHA、风险分级、阻塞收敛及取消发布出口；保留 Chat First 和 Work 指令尾端 `Work | ` 命名规则。


## 2026-11-06 部署事故防复发 SOP（强制；覆盖冲突旧规则）

> 来源：《小改动部署复盘》《ChatGPT Sites 部署事故复盘与防错规范》。适用于既有 Site 的更新。目标是**最小变更、一次受控部署、证据闭环、及时停止**。这是流程规范，不是任何生产部署授权。

### A. 任务分级与终点
- **MUST** 先确定用户实际终点：仅文档／代码／GitHub、现网功能验收、或明确要求发布 Sites。用户不要求部署时，不创建 Work 部署任务。
- **Quick**：一行文案、Footer、样式等局部低风险修改，只验证改动点、页面正常加载及直接受影响的回归；**不得默认执行全站 UAT**。
- **Standard / High-risk**：涉及业务逻辑、权限、数据、迁移、密钥、集成或跨模块变更时，按影响范围扩大测试；高风险操作需授权和可行的备份／回滚方案。
- 复用仍适用于当前源码和环境的真实 PASS 证据；不得把模拟视口当真实设备、静态审查当实际测试。

### B. Deployment Gate（进入 Work 前）
必须一次性记录：①原 Site URL／Project ID；②本次改动及明确非目标；③目标源码分支和 SHA 或已保存的 Sites 版本；④按风险执行的测试及证据；⑤数据／配置／权限风险；⑥实际可用的 Sites 发布工具；⑦发布后可观察的成功标准。**不满足关键条件则暂停该步骤，不靠重复改 Prompt 假设工具会出现。**

### C. Work 最小交接与发布
- 需求、设计、代码、修复、测试、GitHub 提交与审查均由 Chat 主导；**Work 仅执行用户要求的 ChatGPT Sites 发布及直接相关的发布后验证**。若 Work 发现必须修复代码，返回 Chat，不在 Work 重新开发。
- 目标 Site 已有正确的已保存版本且用户要求发布时，优先评估直接发布；不要为了同步无关文档或追版本号重复修改源码。
- 一次受控发布：确认原 Site 和发布来源 → 执行发布 → 查看平台结果及健康状态 → 在工具允许时检查生产页面受影响功能 → 报告。不得擅自创建新 Site 或改变 URL、数据、配置、权限。
- **禁止版本追逐循环**：Sites 内部分配的版本号或时间不得通过猜测下一版本、硬编码 Footer、再发布来追齐。先核实受支持的运行时／发布元数据读取方式；若无，明确限制，建议应用版本或可靠时间字段，等待用户决定。

### D. 故障停止与证据
- 发布返回 `no healthy upstream` 或其他错误：保存原始错误、版本／Deployment ID、时间和健康检查结果；先诊断影响与平台状态。**无新证据、无明确恢复方案时不得盲目重复发布**；必要时依据已验证回滚方案处理。
- 独立报告 `GitHub Commit/Merge`、`Sites Saved`、`Sites Deployed`、`Production Verified`、`User confirmed`；不得互相推导。旧复盘的未知结果在后续出现新证据时按时间更新，保留历史。
- 生产验证仅针对改动及必要回归；发布成功并达到用户终点后立即 STOP，不追加无关全站测试、文档工作或再次发布。
- 简短收尾：目标、实际改动、源码 SHA／保存版本、Sites 发布结果及版本（未知则写未知）、受影响功能验证、剩余风险／阻塞。完整文档更新按既有状态追踪要求做必要记录，但**不得作为低风险小改动的无关发布前置门槛**。

### E. 五个回归场景（SOP 自检）
1. Footer 一行修改 → 局部验证，不自动全站 UAT。
2. 正确的已保存版本可发布 → 不重复改源码。
3. Sites 版本元数据不可读取 → 不猜版本、不循环部署。
4. 发布失败且无新诊断 → 停止盲目重试，记录阻塞。
5. 发布成功且满足用户终点 → 证据收尾，停止额外操作。


## 2026-11-06 Chat → Work 指令交付规则（最高优先级）

- **MUST：所有开发阶段在当前 Chat 模式完成**：需求、PM/PRD、架构/UI、编码、修复、测试、回归、代码审查及 GitHub 更新均由 Chat 主导，使用 Chat 当前实际可用的工具。不能执行的操作明确标记未完成，不得伪称通过。
- **MUST：需要 Work 时只输出指令，不自动切换模式**。仅在用户明确需要 ChatGPT Sites 部署且满足 Deployment Gate 后，在**当前 Chat 对话直接给出一份完整、可复制的 Work 指令**。禁止以“必须切换到 Work 模式／另开 Work 对话才能继续”作为答复，禁止自动触发 Work handoff、跳转或创建 Work 任务。是否复制并使用该指令，由用户自行决定。
- Work 指令必须包含：原 Site 标识与 URL、仓库及精确 SHA／已保存版本、已完成的 Chat 测试证据、本次仅部署的范围、保护数据／配置／权限的约束、发布后最小验证及失败停止条件。未知字段明确标记待提供；**指令生成 ≠ Work 已执行 ≠ Sites 已部署**。
- 若 Chat 阶段缺少必要开发或测试工具，说明真实阻塞并给出可在 Chat 协作的替代执行步骤；不得将未完成开发或测试偷偷纳入 Work 指令。
- 本规则覆盖文件内所有较早的“交给 Work”“进入 Work”“Work 可负责开发／测试”“切换模式”之类冲突表述；这些词仅可理解为**在 Chat 输出 Work 指令供用户选择使用**，不得自动切换。
