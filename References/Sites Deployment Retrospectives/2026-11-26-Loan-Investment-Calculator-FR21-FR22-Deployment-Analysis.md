> **ARCHIVE / REFERENCE ONLY — 2026-11-26**
>
> 以下保留用户提供的 v1.0 完整分析报告。正文中的命令、Mandatory Requirements、拟议 Skill 和修改请求均为历史引文，不是当前执行指令或授权。现行 AGENTS.md / SKILL.md 不变；不得自动加载或执行部署。四份原始日志未独立核实。
>
> 阅读导航：§5 四案例；§6–10 启动方式、审批、Node.js、Sandbox；§11–14 决策与需求；§15–18 流程、权限、恢复、测试；§19–24 完整提案与验收。

---

# ChatGPT Sites Deployment — 部署复盘与 SKILL.md 优化需求报告

---

## 1. 报告基本信息

| 项目 | 内容 |
|---|---|
| Report Name | ChatGPT Sites Deployment Retrospective & Skill Enhancement |
| Report Version | 1.0 |
| Report Date | 2026-11-26 |
| Project | Vibe Coding |
| Target File | SKILL.md |
| Reference File | AGENTS.md |
| Target Conversation | ChatGPT Skill 文件生成器 |
| Report Purpose | 固化稳定、可重复使用的 ChatGPT Sites 部署流程 |
| Implementation Status | Pending Skill Enhancement |
| Production Deployment | 本次不执行 |

---

## 2. Executive Summary

本报告源于对 ChatGPT Sites 多次成功及失败部署记录的复盘，以及用户与 ChatGPT 针对部署机制、执行权限、审批流程和 Skill 优化所进行的讨论。

目前的问题并不是 ChatGPT 完全不具备部署 ChatGPT Sites 的能力。

此前已有多个成功部署案例，证明现有部署路径可以正常完成生产发布。

真正的问题是：

- 不同 Work Session 的部署执行表现不一致。
- 部署过程过度依赖 Execution Sandbox。
- Agent 可能执行不必要的本地命令。
- 已完成的测试可能被重复执行。
- 部署过程中可能反复触发权限审批。
- 审批请求可能在用户来得及响应之前超时。
- Agent 可能错误地将审批超时解释为用户主动拒绝。
- Agent 未能稳定复用此前成功的部署方式。
- 部署失败后，Agent 可能反复尝试相同的失败操作。

用户不希望每次部署时重新研究这些问题，也不希望重新设计整个 Vibe Coding 架构。

最终决定是：

**通过修改现有 SKILL.md，将已经成功的 ChatGPT Sites 部署路径固化为标准化、可重复使用、尽可能自主执行的 Deployment Workflow。**

目标是让用户只需要发出一次部署指令，ChatGPT 即可在平台允许的权限范围内完成整个部署过程。

---

## 3. Background

### 3.1 现有项目架构

当前 Vibe Coding 项目主要涉及以下平台：

| Component | Responsibility |
|---|---|
| ChatGPT | 需求分析、开发、修改、部署协调 |
| GitHub Connector | GitHub Repository 读取及写入 |
| GitHub | Application Source Code、Version Control |
| ChatGPT Work | 必要的执行及部署操作 |
| ChatGPT Sites | Application Hosting、Production Deployment |
| AGENTS.md | 项目级执行规范 |
| SKILL.md | 标准化操作流程及执行技能 |

用户希望继续使用现有架构，不引入不必要的新平台或额外技术依赖。

### 3.2 参考部署项目

本次复盘主要参考 Loan Investment Calculator 项目。

| 项目 | 内容 |
|---|---|
| Application | Loan Investment Calculator |
| GitHub Repository | jaronhoh/loan-calculator |
| Branch | main |
| Application Source | development/index.html |
| Hosting Platform | ChatGPT Sites |
| Sites Project ID | appgprj_6ad0f21a67c88191ba4abfa0761bc3c0 |
| Production URL | https://loan-investment-calculator.jaron-hoh.chatgpt.site/ |
| Access Mode | Owner Only |
| Hosting Provider | cloudflare_artifact |

上述项目资料用于说明此前部署案例，不应将这些项目专属参数硬编码至通用 Skill。

---

## 4. Reference Documents

本次讨论涉及以下四份部署复盘文档：

| ID | Document | Result |
|---|---|---|
| CASE-A | FR21_本对话部署过程详细复盘.md | Failed |
| CASE-B | FR21-deployment-retrospective.md | Failed |
| CASE-C | loan-calculator-deployment-retrospective.md | Successful |
| CASE-D | FR-22-Sites-Deployment-Retrospective.md | Successful |

同时涉及现有项目规范：

| Document | Previously Referenced Version |
|---|---|
| AGENTS.md | V4.1 |
| SKILL.md | V4.4 |

本报告根据本次对话及此前复盘结论整理，并未重新执行部署或重新核实所有原始日志。

修改 Skill 时，应以目标对话中实际提供的最新 SKILL.md 为准。

---

## 5. Deployment Case Analysis

### 5.1 CASE-A — FR-21 Deployment Failure

此前复盘记录显示：

- GitHub Source Code 已完成开发。
- GitHub Connector 可以正常操作。
- Sites Management API 可以正常访问。
- 可以取得 Sites 短期认证资料。
- 自动化测试已执行。
- Node.js VM 检查约 80,436 项。
- Sites Workflow Helper 执行过程中出现等待及中断。
- 多次权限审批请求未能成功完成。
- 部分审批结果显示 aborted by user。
- 未完成新的 Sites Commit。
- 未完成 Deployment Artifact。
- 未完成 Production Deployment。
- Production 仍停留在原有版本。

该案例说明：

应用程序开发完成、测试通过以及 Sites API 可访问，并不代表最终部署一定能够完成。

部署仍可能受到执行环境及权限机制阻塞。

需要注意，该案例没有明确记录 DNS Resolution Error，因此不能直接认定其失败原因与 CASE-B 完全相同。

### 5.2 CASE-B — FR-21 Deployment Failure

此前复盘记录显示，执行 Git 网络操作时出现以下错误：

```text
Could not resolve host: github.com
```

```text
Could not resolve host: git.chatgpt-team.site
```

同时：

- GitHub Connector 仍可正常操作。
- 已有自动化测试通过。
- Work Sandbox 内的 Git 网络操作失败。
- Sites Repository 未成功完成 Checkout。
- 多次审批请求未成功完成。
- 未提交新的 Production Deployment。

该案例说明：

GitHub Connector 的可访问性与 Work Sandbox 的 Git 网络访问能力属于不同的执行路径。

GitHub Connector 正常，不代表 Sandbox 内的 Git CLI 一定具备网络权限。

因此，不能因为 Sandbox 内 Git 命令失败，就直接认定 GitHub 服务或 ChatGPT Sites 服务不可用。

### 5.3 CASE-C — FR-21 Successful Deployment

该案例最初也出现网络连接问题。

但随后成功获得所需网络执行权限，并完成：

- GitHub Source Retrieval。
- Sites Repository Checkout。
- 自动化测试。
- Source Synchronization。
- Sites Commit。
- Deployment Artifact Generation。
- Production Deployment。
- Production QA。

此前复盘记录显示：

| Production Version | Release | Result |
|---|---|---|
| V15 | FR-21 | Successful |
| V16 | Bilingual Footer Update | Successful |

该案例证明：

初始网络错误并不必然导致部署失败。

当执行环境具备必要权限时，现有 ChatGPT Sites 部署路径可以正常完成生产发布。

### 5.4 CASE-D — FR-22 Successful Deployment

该案例同样出现初始网络及部署过程中的技术问题，但最终通过恢复机制成功完成部署。

| Production Version | Result |
|---|---|
| V17 | Successful |
| V18 | Successful |
| V19 | Successful |

成功恢复的问题包括：

- Sites Authentication Token Expiration。
- Deployment Archive Structure Error。
- Stale Sites Commit SHA。
- Deployment Artifact 与 Commit 不匹配。
- HTTP 403 Authentication Error。
- Git Synchronization Error。

其中具有重要参考价值的恢复方法包括：

1. 自动刷新过期的 Sites Authentication Token。
2. 重新取得 Sites Repository 的最新 Remote HEAD。
3. 使用正确的 Sites Commit SHA。
4. 根据对应 Sites Commit 生成 Deployment Artifact。
5. 确保 Archive 包含正确的 Hosting Manifest 及 Build Output。
6. 修正已知错误后继续部署，而不是重新启动整个项目开发流程。

该案例证明，部分部署错误属于可恢复错误，可以通过标准化 Skill 自动处理。

---

## 6. Work Session Launch Method

### 6.1 用户观察

用户发现：

成功的部署案例主要来自现有 Chat 对话直接连接至 Work。

失败的部署案例主要来自将 Work 指令复制到一个新建立的 Work 对话中执行。

| Work Launch Method | Observed Result |
|---|---|
| Continue existing Chat into Work | Successful cases |
| Start independent Work using copied instructions | Failed cases |

### 6.2 分析结论

根据此前四个案例的分类：

- 两个失败案例。
- 两个成功案例。
- 成功案例累计完成五次 Production Release。

这些记录支持将直接延续现有 Chat 对话进入 Work 作为优先执行方式。

但是，现有证据不足以证明：

- 独立 Work Session 一定无法部署。
- 直接连接 Work 一定不会出现权限问题。
- Work 启动方式必然决定 Sandbox Approval Policy。
- OpenAI 内部存在已经确认的相关系统缺陷。

因此，Skill 应优先使用已经成功的 Work Continuation Path，但不应将未经证实的因果关系写成确定事实。

用户不要求为此重新执行 A/B Test。

---

## 7. User Correction — Approval Timeout

### 7.1 原有错误解释

此前 ChatGPT 曾将以下工具返回结果：

```text
aborted by user
```

解释为用户可能主动中断或拒绝审批。

该解释需要修正。

### 7.2 用户明确纠正

用户表示：

“不是我中断，只是没有反应过来而超时了。而且这个应该要一步到位啊，为什么一定要我多次的批准呢？这很奇怪，而且尤其是也是要note.pl的批准。”

用户明确说明：

- 没有主动拒绝审批。
- 没有主动中断审批。
- 审批出现后，未能及时响应。
- 审批请求因此超时。
- 不接受 Agent 将此情况直接解释为用户拒绝执行。
- 不希望部署过程中反复要求人工批准。

### 7.3 修正后的 Root Cause Interpretation

| Item | Correct Interpretation |
|---|---|
| User Deployment Authorization | 已授权 |
| Explicit User Rejection | 用户明确表示没有拒绝 |
| Approval Timeout | 用户报告未及时响应而超时 |
| Tool Result | aborted by user |
| Exact Platform Mechanism | 未确认 |
| Repeated Approval | 需要尽可能减少 |
| User Responsibility | 不应认定用户主动阻止部署 |

此前部分工具记录显示约 0.1 至 0.8 秒的中断时间。

但是，无法确认这些时间是否代表完整的 UI Approval Waiting Window。

Skill 不应将这些工具计时直接定义为平台的实际审批超时设置。

### 7.4 Mandatory Interpretation Rule

Skill 必须明确区分：

- Explicit User Rejection。
- Approval Timeout。
- Automatic Permission Denial。
- Unavailable Approval Mechanism。
- Expired Permission。
- Unknown Approval Failure。

不得仅根据 aborted by user 错误信息，认定用户主动拒绝或取消部署。

---

## 8. Repeated Approval Problem

### 8.1 用户的核心问题

用户不理解为什么已经明确批准生产部署后，Agent 仍然需要反复要求批准底层操作。

例如：

- Git Clone。
- Git Fetch。
- Git Push。
- Network Access。
- Node.js Execution。
- Sites Workflow Helper。
- Deployment-related Command Execution。

### 8.2 Authorization 与 Execution Permission

需要区分以下两类授权：

| Authorization Type | Description |
|---|---|
| Business Authorization | 用户批准执行指定生产部署 |
| Platform Execution Permission | 平台对网络、命令、文件或工具执行的安全权限控制 |

用户发出明确部署指令，代表该部署范围内的 Business Authorization 已经成立。

Agent 不应重复询问用户是否同意执行同一个生产部署。

但用户的业务授权不能自动覆盖平台强制要求的执行权限。

Skill 无法通过文字指令取消平台安全机制。

### 8.3 Approval Granularity

理论上可能存在以下审批粒度：

| Approval Scope | Description |
|---|---|
| Command-level | 单一命令授权 |
| Turn-level | 当前执行轮次授权 |
| Session-level | 当前 Session 授权 |
| Task-level | 当前任务授权 |

用户期望尽可能采用 Task-level 或 Session-level 的权限使用方式。

但本报告没有确认 ChatGPT Work 当前一定支持这些审批粒度。

因此，Skill 应规定：

Where supported, consolidate permission requests and reuse valid permissions throughout the authorized deployment task.

不得假设平台必然提供永久权限或任务级权限。

---

## 9. Node.js and Execution Sandbox

### 9.1 用户疑问

用户特别指出，为什么连 Node.js 或相关脚本执行都需要批准。

用户原话使用了 note.pl。

此前 ChatGPT 暂时将其理解为 Node.js Sites Workflow Helper，例如：

```bash
node site-workflow.mjs
```

由于没有进一步确认实际程序名称，Skill 不应将 note.pl 硬编码为具体工具或程序。

### 9.2 技术分析

普通 Node.js 本地计算，与涉及外部网络和远端资源写入的 Node.js Helper，并不是完全相同的操作。

| Operation | Potential Permission Requirement |
|---|---|
| Local Calculation | 本地计算 |
| Read Workspace File | 文件读取 |
| Generate Archive | 文件写入 |
| Sites Workflow Helper | 网络、文件及子进程 |
| Git Clone | 外部网络 |
| Git Push | 外部网络及远端写入 |
| Sites Authentication | 认证及资源访问 |

此前成功的普通 Node.js 测试说明，并非所有 Node.js 操作都一定需要额外审批。

相关审批可能来自脚本所涉及的网络或执行权限，而不是 Node.js 本身。

该解释属于技术推断，尚未获得 OpenAI 内部审批规则的确认。

### 9.3 Skill Enhancement Requirement

- 不要为了部署而执行不必要的 Node.js 脚本。
- 不要为了检查工具是否可用而反复执行无意义命令。
- 只有在实际部署机制要求时，才使用必要的 Helper。
- 优先使用现有已授权的工具能力。
- 不要绕过强制安全控制。

---

## 10. Local Testing and Cloud Architecture

### 10.1 用户提出的问题

用户表示：

“为什么需要本地的测试呢？因为一切都是在于云端的 GitHub 跟 Sites 而已嘛，关本地什么事情呢？每次都需要我的批准。”

用户希望避免部署流程过度依赖所谓的 Local Execution。

### 10.2 Local Environment Definition

此前讨论中的 Local Environment，主要指 ChatGPT Work Execution Sandbox，而不是用户个人 Windows 电脑。

| Environment | Responsibility |
|---|---|
| User Computer | ChatGPT 操作界面 |
| GitHub Cloud | Source Code |
| Work Sandbox | 必要命令、测试、打包及部署执行 |
| ChatGPT Sites | Production Hosting |

因此，不能将此前 Sandbox 网络错误直接归因于用户个人电脑或家庭网络。

### 10.3 Unnecessary Execution

用户希望避免：

- 重复下载已经可以通过 Connector 访问的资料。
- 重复 Clone Repository。
- 重复执行相同 Commit 的测试。
- 重复执行已完成的 Build。
- 重复生成没有变化的中间产物。
- 为了不必要的本地执行而触发审批。

### 10.4 Testing Policy

本次优化不等于取消所有测试。

正确的执行原则是：

- 已有有效测试结果应当复用。
- 测试结果必须对应实际 Release Commit。
- 如果 Source Code 已发生相关变更，不能错误复用旧测试结果。
- 必要的 Deployment Validation 不应取消。
- 必要的 Production Acceptance Check 不应取消。
- 不应仅为了部署而重新执行已经完成且仍然有效的全部测试。

---

## 11. Previously Discussed Alternative Architecture

此前 ChatGPT 曾建议：

- GitHub Actions。
- Cloud-native CI/CD。
- GitHub Runner 执行测试。
- GitHub Runner 执行 Build。
- GitHub Actions 生成 Deployment Artifact。
- 调查 GitHub Actions 直接部署 ChatGPT Sites 的可行性。
- 修改 AGENTS.md 的架构原则。
- 执行额外的部署环境 A/B Test。

用户最终明确表示：

“我不要什么需要再重新测试或者是改原则。我要一劳永逸的方案。或者是要修改那个 skill file，而让达到那个部署的过程平顺安稳。”

因此，上述架构建议不属于本次实施范围。

### 11.1 Rejected or Out-of-Scope Items

| Item | Decision |
|---|---|
| Introduce GitHub Actions | Out of Scope |
| Redesign CI/CD Architecture | Out of Scope |
| Introduce New Hosting Platform | Out of Scope |
| Conduct New Deployment Experiments | Out of Scope |
| Conduct Work Session A/B Testing | Out of Scope |
| Rewrite Overall Vibe Coding Principles | Out of Scope |
| Repeat Existing Valid Tests | Not Required |
| Modify Existing SKILL.md | Selected |
| Standardize Proven Deployment Path | Selected |
| Reduce Redundant Approval | Selected |
| Automatic Known Error Recovery | Selected |

不得将本报告重新解释为一个新的 CI/CD Architecture Project。

---

## 12. Final User Decision

本次讨论最终确定：

**优先修改现有 SKILL.md，而不是重新设计整个开发及部署架构。**

具体目标：

1. 固化此前成功的 ChatGPT Sites Deployment Workflow。
2. 减少不必要的 Sandbox Execution。
3. 减少不必要的 Node.js Execution。
4. 减少重复测试。
5. 减少重复业务授权确认。
6. 在平台支持时复用有效执行权限。
7. 自动处理此前已经遇到的可恢复部署错误。
8. 避免 Agent 反复尝试相同的失败操作。
9. 完成必要的 Production Acceptance。
10. 用户只需提供一次明确部署指令。

用户所要求的一劳永逸，应理解为：

不需要每次重新制定部署策略、重新研究相同问题或重复进行没有必要的人工干预。

不能将其解释为允许绕过平台安全机制，或者保证未来所有部署绝对不会出现任何错误。

---

## 13. SKILL.md Enhancement Scope

### 13.1 Target Section

建议新增或整合以下章节：

ChatGPT Sites Deployment Workflow

应由 Skill 文件生成器根据现有 SKILL.md 的实际结构，决定最适合的章节位置及编号。

### 13.2 Existing Skill Compatibility

此前 AGENTS.md 与 SKILL.md 主要采用 GitHub-only 的开发执行范围。

因此，新增部署流程时，需要特别检查：

- GitHub-only 限制。
- Work Mode 使用限制。
- Deployment Authorization。
- Existing Skill Scope。
- Instruction Priority。
- Tool Execution Restrictions。

不能直接新增一个与现有强制规则冲突的部署章节。

建议将 ChatGPT Sites Deployment 定义为用户明确触发时才启用的专项执行流程。

正常开发任务继续遵守现有 GitHub-only 规则。

当用户明确要求部署 ChatGPT Sites 时，才进入授权范围内的 Deployment Workflow。

如果现有 AGENTS.md 存在无法通过 SKILL.md 合法解决的上级规则冲突，必须明确报告该冲突，不得擅自忽略。

本次不要求重新设计 AGENTS.md。

### 13.3 Preservation Requirement

修改过程中应保留：

- 原有开发流程。
- 原有需求分析规则。
- 原有 GitHub 操作规范。
- 原有版本管理规范。
- 原有文档规范。
- 原有安全限制。
- 与部署优化无关的全部有效规则。

避免重复定义相同的执行规则。

---

## 14. Functional Requirements

| ID | Requirement | Priority |
|---|---|---|
| DEP-01 | Explicit Deployment Trigger | Mandatory |
| DEP-02 | Reuse Business Authorization | Mandatory |
| DEP-03 | Prefer Proven Deployment Path | Mandatory |
| DEP-04 | Prefer Existing Chat-to-Work Continuation | High |
| DEP-05 | GitHub as Source of Truth | Mandatory |
| DEP-06 | Reuse Valid Test Results | Mandatory |
| DEP-07 | Minimize Sandbox Dependency | Mandatory |
| DEP-08 | Minimize Redundant Node.js Execution | Mandatory |
| DEP-09 | Consolidate Permissions Where Supported | Mandatory |
| DEP-10 | Reuse Valid Execution Permissions | Mandatory |
| DEP-11 | Correct Approval Failure Interpretation | Mandatory |
| DEP-12 | Automatic Token Refresh | Mandatory |
| DEP-13 | Sites Commit SHA Validation | Mandatory |
| DEP-14 | Deployment Artifact Validation | Mandatory |
| DEP-15 | Automatic Known Error Recovery | Mandatory |
| DEP-16 | Prevent Identical Failed Retry Loops | Mandatory |
| DEP-17 | Production Deployment Verification | Mandatory |
| DEP-18 | Essential Production Acceptance | Mandatory |
| DEP-19 | Precise Blocker Reporting | Mandatory |
| DEP-20 | Preserve Platform Security Controls | Mandatory |

---

## 15. Recommended Standard Deployment Workflow

### 15.1 Deployment Trigger

用户发出明确部署指令，例如：

```text
Deploy FR-23 to ChatGPT Sites.
```

或者：

```text
部署最新已完成版本至 ChatGPT Sites。
```

Agent 应根据现有项目资料识别：

- Target Application。
- GitHub Repository。
- Release Commit。
- Sites Project。
- Deployment Scope。

如果这些资料已经存在，不应要求用户重复提供。

如果部署目标存在实质性歧义，则仅询问必要的缺失信息。

### 15.2 Execution Sequence

```text
User Deployment Instruction
        |
        v
Identify Target Site and Release
        |
        v
Retrieve Approved GitHub Commit
        |
        v
Reuse Existing Valid Test Results
        |
        v
Retrieve or Refresh Sites Credentials
        |
        v
Synchronize Sites Source Repository
        |
        v
Verify Sites Remote Commit SHA
        |
        v
Generate Required Deployment Artifact
        |
        v
Validate Artifact and Commit Alignment
        |
        v
Submit Production Deployment
        |
        v
Verify Active Production Version
        |
        v
Perform Essential Production QA
        |
        v
Record Release Outcome
        |
        v
Deployment Completed
```

### 15.3 Execution Priority

根据实际可用能力，采用以下优先顺序：

1. Existing Authorized ChatGPT Sites Capabilities。
2. GitHub Connector。
3. Established Sites Deployment Workflow。
4. Sandbox Command Execution，仅在技术上必要时使用。

不得为了遵循优先顺序而跳过实际部署机制要求的必要步骤。

### 15.4 Source Synchronization

必须区分：

- GitHub Application Repository。
- ChatGPT Sites Source Repository。

GitHub Application Commit SHA 不一定等于 Sites Source Commit SHA。

不能直接使用 GitHub Application Commit SHA 替代 Sites Deployment 所要求的 Sites Commit SHA。

### 15.5 Deployment Artifact

如果当前 Sites 部署机制要求 Artifact：

- 必须使用对应 Sites Commit 生成。
- 必须包含实际要求的 Hosting Manifest。
- 必须包含正确的 Build Output。
- 必须确保 Archive Structure 正确。
- 必须确保 Artifact 与提交部署的 Commit SHA 对应。

此前成功案例涉及：

```text
.openai/hosting.json
dist/index.html
```

上述路径属于此前项目的成功案例。

通用 Skill 应根据实际项目 Hosting Configuration 确定正确路径，而不是对所有项目硬编码相同目录结构。

---

## 16. Permission Management Rules

### 16.1 Business Authorization

用户明确要求部署后：

- 不重复确认相同部署意图。
- 不重复询问是否继续已授权步骤。
- 不重复询问是否执行已经明确要求的生产发布。

如果执行范围发生实质性变化，或者平台强制要求审批，则遵守相应要求。

### 16.2 Execution Permission

Agent 应：

- 优先使用已经授权的能力。
- 在平台支持时集中申请必要权限。
- 在平台支持时复用有效权限。
- 避免不必要的权限升级。
- 避免为了非必要命令触发审批。
- 避免反复申请已经明确不可用的权限。

### 16.3 Approval Timeout

如果审批未完成：

- 不得直接认定用户主动拒绝。
- 不得错误归责于用户。
- 应根据实际工具结果识别失败类型。
- 如果无法确定原因，应标记为 Unknown Approval Failure。
- 如果平台允许恢复，应采用适当恢复方式。
- 不得进入重复提交相同审批请求的循环。

### 16.4 Security Boundary

禁止：

- Bypass Platform Security Controls。
- Disable Mandatory Security Approval。
- Unauthorized Privilege Escalation。
- 使用未经授权的 Credentials。
- 将 Short-lived Token 硬编码至项目文件。
- 将 Authentication Token 写入 GitHub Repository。
- 在部署报告中泄露认证资料。

---

## 17. Automatic Error Recovery

### 17.1 Recovery Matrix

| Error | Required Recovery |
|---|---|
| Expired Sites Token | 自动刷新或重新取得有效 Token |
| HTTP 403 caused by expired credentials | 重新认证后执行必要操作 |
| Stale Sites Commit SHA | 重新读取 Sites Remote HEAD |
| Artifact and Commit Mismatch | 使用正确 Commit 重新生成 Artifact |
| Incorrect Archive Structure | 根据实际 Hosting Configuration 修正 |
| Missing Hosting Manifest | 检查并修正必要 Manifest |
| Recoverable Git Synchronization Error | 使用已经成功的恢复路径 |
| GitHub CLI Credential Failure | 在适用时使用 GitHub Connector |
| DNS Resolution Error | 检查实际执行路径及必要网络权限 |
| Approval Timeout | 不认定为主动拒绝，避免重复审批循环 |
| Mandatory Permission Denied | 停止相同失败尝试并报告 Blocker |
| Deployment API Failure | 根据实际错误判断是否可恢复 |
| Production Version Mismatch | 核实实际部署状态及 Release Mapping |

### 17.2 Recovery Principle

优先使用此前成功的恢复方式。

不应在遇到一个可恢复错误后：

- 重新启动整个开发流程。
- 重新执行所有已经通过的测试。
- 创建新的 Repository。
- 创建新的 Sites Project。
- 改变现有 Hosting Platform。
- 引入新的 CI/CD 平台。
- 反复执行已经明确失败的相同命令。

### 17.3 Stop Condition

如果部署依赖的必要权限被平台明确拒绝，且没有已授权的替代执行路径：

- 停止重复尝试。
- 保留已完成的工作。
- 明确报告具体阻塞步骤。
- 说明尚未完成的部署操作。
- 不得虚报部署成功。

---

## 18. Testing and Validation Requirements

### 18.1 Reuse Existing Test Results

允许复用已有测试结果的条件：

- 对应相同 Release Commit。
- 测试范围仍适用于当前 Release。
- 相关 Source Code 未发生导致结果失效的变更。
- 已有测试结果可以确认。
- 没有新的技术证据表明必须重新测试。

### 18.2 Additional Testing

只有以下情况才执行必要的额外测试：

- Release Source Code 已变更。
- Existing Test Result 无法确认。
- Deployment Process 引入新的相关变更。
- 发现具体 Bug。
- 实际部署机制要求特定 Validation。
- Production Acceptance 需要验证关键功能。

### 18.3 Production Acceptance

Production Acceptance 应聚焦：

- Production Site 可访问。
- Production Deployment Status 为成功。
- 正确 Release Version 已激活。
- 实际部署 Source 与目标 Release 一致。
- 关键功能正常。
- 本次 Release 所涉及的主要变更正常。

不应为了形式上的完整性，在每次部署后重复整个应用程序的全部 Regression Test。

---

## 19. Proposed SKILL.md Content

以下英文内容作为 Skill 文件生成器的核心修改依据。

应根据现有 SKILL.md 的结构整合，而不是未经检查直接覆盖原文件。

---

# ChatGPT Sites Deployment Workflow

## 1. Deployment Trigger

When the user explicitly requests deployment to ChatGPT Sites, execute the deployment workflow autonomously within the authorized scope.

The user's explicit deployment instruction constitutes business authorization for the requested release.

Do not repeatedly request confirmation of the same deployment intent.

Activate this workflow only when ChatGPT Sites deployment is explicitly requested or otherwise authorized under the existing project instructions.

Preserve the existing development workflow for non-deployment tasks.

## 2. Execution Priority

Follow this execution priority where applicable:

1. Existing authorized ChatGPT Sites capabilities.
2. GitHub Connector.
3. Established ChatGPT Sites deployment workflow.
4. Sandbox command execution only when technically required.

Prefer continuing from the existing conversation into Work when this execution path is available.

Do not introduce alternative deployment architectures unnecessarily.

Do not create a new repository or Sites project when an existing authorized target is available.

## 3. Source Code and Testing

Use GitHub as the source of truth for application source code.

Identify the exact approved release commit.

Reuse existing valid test results associated with the release commit.

Do not repeat previously completed tests without technical justification.

Do not execute unnecessary local builds, Git clones, Node.js scripts, or other sandbox operations.

Perform additional validation only when required by the release, source changes, deployment mechanism, or production acceptance criteria.

## 4. Permission Management

Reuse existing valid execution permissions wherever supported.

Consolidate required permission requests wherever supported.

Do not request redundant approval for operations already covered by valid authorization.

Minimize commands requiring elevated execution permissions.

Do not repeatedly submit identical failed approval requests.

Distinguish explicit user rejection, approval timeout, automatic permission denial, unavailable approval mechanisms, and unknown approval failures.

Do not interpret an aborted by user tool result as explicit user rejection without supporting evidence.

Never bypass mandatory platform security controls.

## 5. Standard Deployment Sequence

1. Identify the existing authorized ChatGPT Sites project.
2. Retrieve the current production version.
3. Identify the approved GitHub release commit.
4. Retrieve or refresh required Sites authentication credentials.
5. Synchronize the approved application source with the Sites source repository.
6. Verify the correct Sites repository commit SHA.
7. Generate the required deployment artifact from the corresponding Sites commit.
8. Validate the artifact structure and commit alignment.
9. Submit the production deployment.
10. Verify the deployment status and active production version.
11. Perform essential production acceptance checks.
12. Record the release outcome.

Do not confuse the GitHub application commit SHA with the Sites deployment commit SHA.

Use the actual hosting configuration and deployment requirements of the target project.

## 6. Automatic Error Recovery

Automatically resolve known recoverable deployment errors, including:

- Expired authentication tokens.
- Authentication failures caused by expired credentials.
- Stale Sites commit references.
- Deployment artifact and commit mismatches.
- Incorrect archive structures.
- Missing required hosting configuration.
- Recoverable Git synchronization errors.

Use the established successful deployment workflow before attempting alternative methods.

Do not restart completed development or testing activities solely because a recoverable deployment error occurs.

If mandatory platform permission is unavailable and no authorized alternative exists, stop repeated identical attempts and report the precise blocker.

## 7. Completion Criteria

Deployment is complete only when:

- Production deployment status is successful.
- The intended release version is active.
- The deployed source matches the intended release.
- Required deployment validation has passed.
- Essential production acceptance checks have passed.

Do not report deployment completion based solely on successful source code commits, completed tests, generated artifacts, or submitted deployment requests.

## 8. Scope and Security

Preserve all unrelated project instructions.

Do not redesign the existing development or deployment architecture unless explicitly requested.

Do not introduce additional CI/CD platforms solely to avoid existing deployment execution requirements.

Do not expose authentication credentials or persist short-lived tokens in project files.

Do not claim that platform-mandated execution approvals can be disabled or permanently eliminated through Skill instructions.

---

## 20. Expected User Experience

修改 Skill 后，用户期望只需要输入：

```text
Deploy FR-23 to ChatGPT Sites.
```

Agent 应自主完成：

```text
Identify Release
→ Retrieve Source
→ Reuse Valid Test Results
→ Authenticate Sites
→ Synchronize Source
→ Validate Sites Commit
→ Prepare Deployment Artifact
→ Deploy
→ Verify Production
→ Report Completion
```

用户不希望再次看到：

- 是否需要重新设计部署架构？
- 是否需要使用 GitHub Actions？
- 是否需要重新研究 Work Sandbox？
- 是否需要重新执行已经通过的全部测试？
- 是否同意继续已经明确授权的部署？
- 同一个失败命令是否需要再次批准？
- 是否需要重新创建现有项目？

如果确实存在平台强制审批，Agent 应尽量减少审批数量，并准确说明其必要性。

---

## 21. Expected Deployment Result Format

建议部署完成后提供简洁、可核实的 Release Summary。

| Item | Result |
|---|---|
| Application | Actual Application |
| Release | Actual Release |
| GitHub Commit | Actual Commit SHA |
| Sites Commit | Actual Sites Commit SHA |
| Production Version | Actual Active Version |
| Deployment Status | Actual Status |
| Test Result | Reused / Executed / Not Verified |
| Production QA | Passed / Failed / Not Verified |
| Deployment URL | Actual URL |
| Blocker | None / Actual Blocker |

所有字段必须根据实际执行结果填写。

不得生成未经验证的 Commit SHA、Deployment ID、Production Version 或成功状态。

---

## 22. Skill Enhancement Acceptance Criteria

以下验收标准用于检查 Skill 修改是否符合本报告，不要求为此重新执行生产部署实验。

| ID | Acceptance Criteria |
|---|---|
| AC-01 | 已包含明确的 ChatGPT Sites Deployment Workflow |
| AC-02 | 用户明确部署后不重复确认相同业务授权 |
| AC-03 | 优先采用已成功的部署路径 |
| AC-04 | 在可用时优先复用 GitHub Connector |
| AC-05 | 不强制执行不必要的 Sandbox Commands |
| AC-06 | 不强制重复相同 Commit 的有效测试 |
| AC-07 | 不必要的 Node.js Execution 已被限制 |
| AC-08 | 在平台支持时集中申请并复用权限 |
| AC-09 | Approval Timeout 不被错误解释为主动拒绝 |
| AC-10 | 包含已知部署错误恢复机制 |
| AC-11 | 包含 Sites Commit SHA 与 Artifact Alignment |
| AC-12 | 包含 Production Deployment Verification |
| AC-13 | 包含必要的 Production Acceptance |
| AC-14 | 不允许虚报部署成功 |
| AC-15 | 不引入新的 CI/CD Architecture |
| AC-16 | 不重新设计整个 AGENTS.md |
| AC-17 | 保留原有无关 Skill Rules |
| AC-18 | 已检查 GitHub-only 与 Deployment Scope 的冲突 |
| AC-19 | 不允许绕过平台安全机制 |
| AC-20 | 不要求重新进行部署实验 |

---

## 23. Known Limitations

修改 SKILL.md 可以改善 Agent 的执行一致性，但无法直接改变 OpenAI 平台内部的安全机制。

| Issue | Skill Control |
|---|---|
| Agent 选择不同部署路径 | 可以规范 |
| 重复执行有效测试 | 可以规范 |
| 不必要的 Sandbox Execution | 可以减少 |
| 不必要的 Node.js Execution | 可以减少 |
| 重复业务授权确认 | 可以规范 |
| 已知部署错误恢复 | 可以规范 |
| 错误解释 Approval Timeout | 可以规范 |
| 平台强制审批 | 无法取消 |
| 平台 Approval Timeout | 无法直接修改 |
| 平台 Sandbox Network Policy | 无法直接修改 |
| 平台 Permission Persistence | 取决于平台能力 |
| 平台服务故障 | 无法保证消除 |

本报告没有证明 Work 启动方式与权限失败之间存在确定的因果关系。

本报告也没有证明 ChatGPT Sites 支持完全不依赖 Work Sandbox 的外部自动部署机制。

因此，Skill 不应包含未经验证的保证。

---

## 24. Instructions for ChatGPT Skill 文件生成器

收到本报告后，请执行以下任务：

1. 读取当前实际使用的 SKILL.md。
2. 理解现有 Skill 的章节结构、版本、执行范围及约束。
3. 检查现有 AGENTS.md 中与部署相关的限制。
4. 识别现有 Skill 与本报告之间的冲突。
5. 将本报告中的部署需求整合至现有 SKILL.md。
6. 优先复用现有规则，避免重复定义。
7. 保留所有与本次部署优化无关的有效规则。
8. 将 ChatGPT Sites Deployment 定义为明确触发的专项流程。
9. 固化已经成功的部署方式及错误恢复机制。
10. 减少不必要的测试、Sandbox Execution 和重复审批。
11. 确保 Skill 不会要求绕过平台安全控制。
12. 检查修改后的 Skill 是否符合本报告的 Acceptance Criteria。
13. 按现有版本管理规范更新 Skill Version。
14. 提供完整修改后的 SKILL.md。
15. 提供 Change Summary。
16. 明确报告无法通过 Skill 解决的限制或上级指令冲突。

### 24.1 Mandatory Restrictions

- 不重新设计整个 Vibe Coding Architecture。
- 不引入 GitHub Actions。
- 不要求进行新的部署实验。
- 不要求执行 Work Session A/B Test。
- 不重新测试已有应用程序。
- 不执行实际 Production Deployment。
- 不擅自修改无关规则。
- 不擅自重写整个 AGENTS.md。
- 不将项目专属 Repository、Site ID 或 Commit SHA 硬编码至通用 Skill。
- 不承诺能够取消平台强制审批。

### 24.2 Required Deliverables

| Deliverable | Requirement |
|---|---|
| Updated SKILL.md | 完整文件，不只是新增片段 |
| Change Summary | 说明新增、修改及整合内容 |
| Conflict Review | 说明与现有规则是否冲突 |
| Acceptance Review | 检查本报告验收标准 |
| Version Update | 遵循现有版本管理规范 |
| Downloadable File | 提供实际可下载的 Markdown 文件 |

---

## 25. Final Conclusion

本次复盘的最终目标不是重新研究 ChatGPT Sites，也不是重新设计 CI/CD 架构。

现有成功案例已经证明 ChatGPT Sites 可以完成生产部署。

当前需要解决的是：

如何通过 Skill 将成功部署经验转化为稳定、标准化、可重复使用的执行流程。

用户希望：

- 一次明确部署指令。
- 尽可能自主执行。
- 不重复有效测试。
- 不执行不必要的本地命令。
- 不反复确认已经授权的事项。
- 自动恢复已知错误。
- 准确处理审批超时。
- 完成真实的 Production Deployment。
- 提供可核实的最终结果。

最终实施方向：

**Modify the existing SKILL.md to establish a standardized, reliable, autonomous ChatGPT Sites Deployment Workflow based on previously successful deployment practices.**

本次任务仅修改 Skill，不执行新的部署实验，不重新设计项目架构。

---

End of Report