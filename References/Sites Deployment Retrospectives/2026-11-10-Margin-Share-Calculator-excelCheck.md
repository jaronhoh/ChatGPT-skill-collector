# 复盘记录：Margin Share Calculator `excelCheck` 修复与部署

**日期：** 2026-11-10  
**项目：** [jaronhoh/margin-share-calculator](https://github.com/jaronhoh/margin-share-calculator)  
**Production：** [margin-share-calculator.jaron-hoh.chatgpt.site](https://margin-share-calculator.jaron-hoh.chatgpt.site/)

## 一、目标

在现有 ChatGPT Sites Production 上修复 `excelCheck` 初始化时报错。以 Production Version 10 为基准，做最小改动，同时保留 UI-008、Version 6 业务逻辑、现有 URL、Owner-only 权限和站点配置。禁止创建、删除或重建 Site。部署后需在真实浏览器检查控制台、默认日期、图表图例、复选框、Yahoo 回测和 JSON 导入，并将实际结果记录到 GitHub PRD。

## 二、阻塞及处理

前期本地可用仓库副本与 Version 10 不匹配：有的缺少 `excelCheck` 源码，有的只有旧 `dist/index.html`；另一个工作副本包含未提交修改。不能安全地在这些副本上直接修改或发布，因此当时没有改代码、测试或部署，也没有覆盖现有改动。

本轮通过 Sites 提供的源码工作流打开绑定到现有 Site 的源码仓库，核对 Version 10 基线后继续。未创建或重建 Site，也未修改 URL、访问权限或运行时配置。

## 三、代码修复与发布

### 发布流水（按实际执行顺序）

| 阶段 | 操作与依据 | 结果／可核对证据 |
|---|---|---|
| 1. 解除源码阻塞 | 放弃用不匹配或带未提交改动的本地副本发布；改用 Sites 源码工作流定位到绑定现有 Production Site 的源码仓库。先核对绑定关系和 Version 10 基线，再开始编辑。 | Site ID 为 `appgprj_6af072845a7c8191bed2cd50b876fb54`；Version 10 基线提交为 `40a3e901b1153db58414cce7121e5b591a5b96c9`。既有 Site 未被创建、删除或重建。 |
| 2. 应用最小修复 | 在 Version 10 的 `dist/index.html` 中移除 `excelCheck()` 对页面不存在的 `#diff` 节点的初始化访问，并把启动序列从 `setupUI();excelCheck();render();` 改为 `setupUI();render();`。在 `tests/cr005-session-ui.test.mjs` 增加断言，防止相关函数、调用及 `$('diff').innerHTML` 访问回归。 | 修复限定在初始化报错；没有恢复废弃 Version 7/BUG-007，也没有改 UI-008、Version 6 业务逻辑、数据配置或站点权限。 |
| 3. GitHub CI | 修复提交推到 GitHub `main`，等待对应 GitHub Actions workflow 完成。 | 修复提交 `50e3c4dd3fcfdc0eb18bb84a9145598970fd7cd9`；run `42792469094`（#274）于 2026-11-10 09:38:55–09:39:04 UTC 完成，结论 `SUCCESS`。 |
| 4. 准备 Sites 发布源 | 使用 Sites 绑定源码仓库中的 `main`，把修复后的站点源文件提交为 Sites 源码提交；由该提交生成发布归档，并核对归档摘要，确保待发布内容对应刚提交的源码。 | Sites 源码提交 `79e332cf7178eb2405ab3c689a753044ad2ea862`；归档 `/private/tmp/margin-share-v11.tar`，SHA-256 `317e320059f236cbde06a2cbb9f179e59e0bceebc7593157c0e579045a8bec7e`。构建 Worker 资源清单共 18 项。 |
| 5. 保存并部署到原 Site | 对原 Site 执行 Sites 的合并保存/部署操作；没有调用创建、删除或重建 Site 的流程。 | 部署 API 返回 `succeeded`；部署 ID `appgdep_6af2e9f536308191967e4fa0511c70fa`；新版本号 11，版本标识 `appgprj_6af072845a7c8191bed2cd50b876fb54~appgver_17c6c76c7d6c8191bb819585de476b2f`。完成时间 `2026-11-10T09:55:20.086258+00:00`（马来西亚时间 17:55）。 |
| 6. 核对部署元数据 | 重新读取 Site 状态和配置，确认发布目标、URL、权限策略及最新版本。 | Site 状态 `active`，最新版本 11；原 URL 不变；访问模式 `custom`，允许名单仍只有 Owner、没有群组；运行时配置未改。 |
| 7. Production 浏览器验收 | 在真实浏览器打开原 Production URL 并刷新，查看页脚版本和更新时间，检查控制台，再逐项执行本记录第四节的页面回归。 | 页脚显示 Version 11，更新时间 `2026-11-10 17:54 (MYT)`；error/warn 控制台为空。功能检查结果见第四节。 |
| 8. 记录与收尾 | 将发布元数据和线上验收写入 GitHub PRD；随后检查 GitHub main 的完整自动化测试，并另行记录 Sites 内部源码快照的测试差异。 | PRD 首次发布记录提交 `9129f80aacc3bcc2eda16cb2f2fb02fe09dc1b5c`；测试差异补记提交 `88aac1c6a2ef0945ae34ace99460c562e4854e46`。测试细节见第五节、第六节。 |

### 发布边界

本次从 Version 10 的绑定源码起步，修复后在既有 Site 上产生 Version 11。GitHub 的修复提交、Sites 内部源码提交、Sites 部署 ID 和最终版本号是不同层级的标识；它们分别用于追踪源代码、Sites 发布源、一次部署操作和用户实际访问的版本。没有更改自定义域名、Owner-only 访问名单、运行时配置或业务数据配置。

- **Production 基线：** Version 10，Sites 源码提交 `40a3e901b1153db58414cce7121e5b591a5b96c9`。
- **最小代码修复：** 删除 `excelCheck()` 对不存在 DOM `#diff` 的访问，并从初始化序列移除该调用。
- **回归断言：** 在 `tests/cr005-session-ui.test.mjs` 中断言源码不再包含 `excelCheck` 初始化访问。
- **GitHub 修复提交：** `50e3c4dd3fcfdc0eb18bb84a9145598970fd7cd9`。
- **GitHub Actions：** [run 42792469094](https://github.com/jaronhoh/margin-share-calculator/actions/runs/42792469094)，workflow `Site source tests`，run #274，结论 `success`。
- **Sites Production：** Version 11，Sites 源码提交 `79e332cf7178eb2405ab3c689a753044ad2ea862`，部署 ID `appgdep_6af2e9f536308191967e4fa0511c70fa`，状态 `succeeded`。
- **Site ID：** `appgprj_6af072845a7c8191bed2cd50b876fb54`。
- **站点与权限：** URL 保持不变；读取到的访问策略为 custom，仅 Owner 一人、无群组。没有创建、删除或重建 Site，没有修改运行时配置。

## 四、真实 Production 浏览器验收

部署后重新载入 Production 页面，页脚显示 Version 11。

| 验收项 | 结果 |
|---|---|
| `excelCheck` 控制台错误 | 页面重新载入后，控制台 error/warn 为空 |
| 首次打开默认状态 | 股票代码为空；开始日期为 `2015-01-02` |
| Yahoo Finance 回测 | 输入 `1155.KL` 后成功回测，首日为 `2015-01-02`；生成 4,327 个日历日流水，至 Yahoo 返回的 `2026-11-06` |
| 桌面图例 | 1600 CSS px 页面宽度下，五项图例同一行并居中于图表区域 |
| 窄屏图例 | 图例自动换成两行 |
| 复选框 | “日终贷款余额”项从选中切为未选中，再恢复选中；未逐项操作其余四项 |
| JSON 导入 | 从 Production 导出 JSON，再通过导入控件导入；相同回测仍显示，24 项唯一付款日期提示重新出现 |

## 五、自动化测试复核

完成线上验收后，分别针对 GitHub main 和 Sites 内部源码快照执行了 `npm test`，结果不同，需区分记录。

### GitHub main：通过

- 从 GitHub 最新 main 干净检出代码；当时 HEAD 为 `9129f80aacc3bcc2eda16cb2f2fb02fe09dc1b5c`，包含 Version 11 部署 PRD 记录，应用代码包含修复提交 `50e3c4d`。
- `site-source` 下执行 `npm test`：**12 项通过、0 失败、0 跳过**。
- 执行后清理了测试生成的临时 Worker 文件，临时检出恢复干净。

### Sites 内部源码快照：3 项失败

- 在 Sites 源码提交 `79e332cf7178eb2405ab3c689a753044ad2ea862` 下执行同一命令：**9 项通过、3 项失败**。
- 两项测试（`board-lots`、`p0-audit`）引用的 `dist/maybank-prices.json` 在该快照中不存在。
- 一项 `cr005-session-ui` 断言期待的股票代码 placeholder 与该 Version 10 来源 HTML 不一致。
- 这份快照与 GitHub main 的测试基线不同。没有为消除这三项基线差异而扩大 Version 11 的最小修复范围；也没有把该结果描述成全套测试通过。
- 测试运行修改了 Sites 工作副本内生成的 `dist/server/index.js`，随后已恢复至提交原状，工作副本状态干净。

## 六、PRD 与 GitHub 记录

- 首次部署记录写入 PRD，提交：`9129f80aacc3bcc2eda16cb2f2fb02fe09dc1b5c`。
- 后续补充 Actions #274、GitHub main 本地测试 12/12 通过，以及 Sites 内部快照 9/12 的差异诊断，PRD 提交：`88aac1c6a2ef0945ae34ace99460c562e4854e46`。
- PRD 路径：`prd/PRD文档.md`。

## 七、复盘结论与后续事项

1. 修复已发布到原 Production；Version 11 的目标浏览器验收已完成。
2. GitHub main 自动化测试和对应 Actions 均通过。
3. Sites 内部源码快照的测试基线与 GitHub main 不一致，存在 3 项本地测试失败。若后续希望 Sites 内部源码也能完整运行同一测试套件，需要单独对齐测试数据文件和 placeholder 断言，再评估是否要修复；这不属于本次 `excelCheck` 最小修复。
4. 本次仅实际切换并恢复了一项图表复选框；PRD 中已明确未逐项操作其余四项。窄屏确认了自动换行，未单独断言横向溢出。
5. 本轮没有再次部署；任何后续发布仍应以现有 Site 为对象并保持 URL、Owner-only 权限和业务基线。
