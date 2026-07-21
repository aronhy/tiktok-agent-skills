# TikTok Agent 独立 CLI 与跨 Agent Skills 设计

**日期：** 2026-07-21

**状态：** 已确认，待实施

**仓库：** `aronhy/tiktok-agent-skills`

**首个目标版本：** `0.1.0`

## 1. 目标

把现有 Codex 可用的 TikTok Skills 扩展为一套可独立运行、可跨 Agent 复用的 TikTok 运营能力包：

1. 保留 `SKILL.md` 作为唯一运营方法来源，使 Codex、WorkBuddy 和独立 CLI 复用同一套流程。
2. 新增一个不依赖 Codex 或 WorkBuddy 的 Node.js/TypeScript Agent Runtime。
3. 同时提供结构化命令和自然语言对话入口。
4. 通过 OpenAI-compatible API 使用用户自行选择的模型服务。
5. 通过 MCP、用户文件和只读浏览器获取可追溯信息。
6. 覆盖 TikTok Shop 研究、公开账号诊断、类目策略、增长规划，以及非 Shop 日常内容运营。
7. 所有结果默认前置结论和优先动作，并保留来源、范围、缺失信息和可信度。

本项目是**只读研究与规划工具**。它只获取、分析、整理信息并生成报告、排期、脚本和回复草稿，不执行 TikTok 发布或账号写操作。

## 2. 已确认的产品决策

| 决策 | 选择 |
| --- | --- |
| 运行形态 | 完全独立 CLI，不依赖 Codex 或 WorkBuddy |
| 模型接口 | OpenAI-compatible API，可配置 Base URL、模型名和 API Key |
| CLI 交互 | 结构化子命令与自然语言对话同时支持 |
| 首版范围 | 现有 4 个 Skill 加 5 个非 Shop 日常运营 Skill |
| 技术栈 | Node.js + TypeScript，以 npm/npx 分发 |
| 浏览器 | Playwright；支持可选本地持久化 Profile 和用户手动登录 |
| 数据动作 | 只读；只做规划、信息获取、分析、整理和草稿生成 |
| 写操作 | 不发布、不回复、不删除、不私信、不改账号、不投放 |
| 仓库组织 | 在现有公开仓库中采用分层 workspace 架构 |
| npm 包与命令 | `@aronhy/tiktok-agent` / `tiktok-agent` |

## 3. 非目标

- 不发布、编辑或删除 TikTok 视频、图文、LIVE 或 Series。
- 不发送、编辑或删除评论、私信或达人邀约。
- 不修改账号资料、隐私设置、广告设置、店铺或投放计划。
- 不通过浏览器模拟点击执行任何外部写操作。
- 不自动输入、读取或保存 TikTok 用户名和密码。
- 不绕过登录、验证码、反爬、地域、年龄或其他访问控制。
- 不建立托管云服务、共享账号系统或远程浏览器服务。
- 首版不实现 Anthropic、Gemini 等原生 Provider；它们可通过后续适配器加入。
- 首版不承诺任意 OpenAI-compatible 模型都具备稳定工具调用或视觉能力。
- 不把普通网络搜索、模型记忆或搜索摘要冒充 TikTok、KSS 或官方数据。

## 4. 用户与账号范围

CLI 面向个人创作者、内容团队、品牌账号和运营人员。账号类型可以由用户明确提供，也可以复用 `tiktok-account-audit` 的证据规则识别为内容型、品牌型、带货型或混合型。证据不足时写“暂无法判断”，不能猜测。

典型任务包括：

- 分析一个公开 TikTok 账号或竞争对手账号。
- 研究一个市场和类目的内容机会。
- 生成 7/14/30 天内容排期。
- 为单条创意生成 Hook、脚本、镜头表和发布素材草稿。
- 读取 TikTok Studio、CSV、JSON 或截图并复盘表现。
- 整理评论主题、FAQ、回复草稿和评论转视频选题。
- 研究 TikTok Shop 商品、店铺、带货视频、达人和字幕。

## 5. 总体架构

采用“同仓库、分层运行时、通用 Skills”的结构：

```text
tiktok-agent-skills/
├── skills/                    # 通用 Agent Skills，唯一运营规则来源
├── packages/
│   ├── core/                  # Agent 循环、会话、Skill 路由和依赖加载
│   ├── cli/                   # chat、run 和结构化子命令
│   ├── provider-openai/       # OpenAI-compatible Provider
│   ├── mcp-client/            # MCP 发现、Schema、调用、分页与错误归一化
│   ├── browser/               # Playwright 只读浏览器与本地 Profile
│   ├── artifacts/             # Markdown、JSON、CSV 与来源账本
│   └── safety-policy/         # 只读工具策略、凭据脱敏与运行限制
├── adapters/
│   ├── codex/                 # Codex 安装与可选 UI 元数据
│   └── workbuddy/             # WorkBuddy 导入或安装适配
├── skillpack.json             # Skill 清单、版本和依赖关系
├── package.json               # npm workspace 与 CLI 入口
└── mcp.example.json           # 不含真实凭据的 MCP 示例
```

### 5.1 运行链路

```text
用户输入
  ↓
CLI（对话或结构化命令）
  ↓
输入检查与 Skill Router
  ↓
按需加载 SKILL.md + references + 依赖
  ↓
Agent Runtime
  ├─ OpenAI-compatible Provider
  ├─ 只读 MCP 工具
  ├─ 用户文件
  └─ 只读浏览器
  ↓
来源账本与完整性检查
  ↓
结果前置输出 + 本地 Artifact
```

### 5.2 单一事实来源

CLI 不把 TikTok 运营规则重新硬编码成另一套实现。`SKILL.md` 定义触发条件、工作流和边界；`references/` 保存详细方法、模板和工具事实；Runtime 只实现通用的加载、路由、工具调用、证据记录和输出能力。

若结构化子命令需要专门参数，子命令只把参数转换为标准任务输入并显式指定 Skill，不复制 Skill 的业务规则。

## 6. 运行时组件

### 6.1 CLI

负责解析命令、启动对话、读取文件路径、展示进度、输出结果和返回稳定退出码。交互模式和非交互模式必须共用同一 Runtime。

### 6.2 Skill Loader 与 Router

- 启动时只读取 Skill 的 `name`、`description`、路径和依赖摘要。
- 任务匹配后再完整加载 `SKILL.md` 和其直接引用的参考文件。
- 显式子命令直接选择一个主 Skill；自然语言模式由模型和确定性路由规则选择最小必要 Skill 集。
- 多 Skill 任务按依赖图执行，防止循环依赖和重复加载。
- 缺失 Skill 或引用文件时在调用模型前失败，并指出确切路径。

### 6.3 Agent Runtime

Runtime 维护消息、工具 Schema、步骤预算、工具调用预算、来源账本和 Artifact 状态。一次运行默认最多 24 个 Agent 步骤、64 次工具调用和 15 分钟总时长，并提供可取消信号。用户可降低预算，或在最多 64 个步骤、256 次工具调用和 60 分钟的硬上限内提高预算；不能取消硬上限或无限自动重试。

### 6.4 OpenAI-compatible Provider

首版实现可配置 Base URL 的 OpenAI-compatible Chat Completions 工具调用接口。Provider 对 Runtime 暴露统一的文本、流式输出、工具调用和可选视觉输入能力。

远程 Base URL 必须使用 HTTPS；仅 `localhost`、`127.0.0.1` 和 `[::1]` 允许 HTTP。Provider 拒绝 URL 内嵌凭据，不把 Authorization Header 转发到不同 Origin 的重定向目标。`doctor` 和首次运行必须显示将接收提示、用户文件摘录与工具结果的模型主机，密钥只发送到用户配置的同一 Origin。

发送给模型的数据遵循最小化原则：优先发送完成当前步骤所需的字段和摘录，不默认上传完整原始文件、浏览器页面或历史 Artifact；发送前仍执行凭据和 Cookie 脱敏。

`doctor` 必须探测当前模型是否实际支持：

- 基本文本生成；
- JSON/结构化输出；
- 工具调用；
- 视觉输入（可选）；
- 流式输出（可选）。

缺少工具调用能力时，纯文本规划任务仍可工作；需要 MCP 或浏览器的任务必须明确停止，不能让模型伪造工具结果。

首版不内置 OCR。`doctor` 必须报告当前模型不支持视觉；输入包含截图且当前模型不支持视觉时，任务返回 `PROVIDER_CAPABILITY_MISSING`，提示用户改用 CSV、JSON 或文本。Runtime 不得凭文件名、上下文或模型猜测截图中的事实。

### 6.5 MCP Client

- 使用服务器实时 Schema，不猜测参数、字段或端点。
- 首版内置 KSS MCP 配置模板，但 Runtime 不与 KSS 专用逻辑耦合。
- 远程 HTTP MCP 端点遵循与模型 Provider 相同的 HTTPS、Loopback 例外、Origin 和凭据重定向限制；本地 `stdio` Server 只从用户显式指定的配置启动。
- 仅注册安全策略允许的只读工具。
- 记录工具、时间、输入范围、页数、结果数量、停止原因和错误类型。
- 处理分页、稳定 ID 去重、限流、401、配额耗尽和部分完成。

### 6.6 Browser

- 使用 Playwright。
- 公开页面可使用临时 Context；需要 TikTok Studio 时启动可见浏览器和本地持久化 Profile。
- 持久化 Profile 是显式启用的本地能力，不是默认条件；保存在操作系统标准应用数据目录，不放进仓库或 Artifact。
- 登录、二次验证和验证码始终由用户在独立的可见窗口手动完成。Runtime 不键入凭据、不点击登录提交按钮，也不读取登录表单内容。
- `browser login` 只负责打开可见浏览器并等待用户完成登录；CLI 不读取输入框内容。非交互任务若需要登录，返回稳定错误并提示用户先执行该命令。
- Agent 只能调用浏览器适配器暴露的高层只读动作：导航、等待、滚动、读取可见内容、截图、打开已验证的只读详情，以及用户主动要求的报表下载；不向模型暴露原始通用点击或任意 JavaScript 执行能力。
- 报表下载只允许已配置的官方导出路径、GET 下载或经适配器验证不改变远端业务状态的导出动作；未知按钮和表单一律拒绝。
- 禁止发布、回复、删除、修改设置、关注、点赞、私信、普通表单提交以及任何未列入白名单的点击。
- 任何访问限制都写入来源账本，不能绕过。

### 6.7 Artifacts

默认将完整运行结果保存到项目根目录。项目根目录取当前目录向上找到的最近 `.git` 或 `package.json` 所在目录；都不存在时使用当前工作目录：

```text
.tiktok-agent/runs/<YYYYMMDDTHHmmssZ>-<短任务名>/
```

时间戳使用无冒号的 UTC 文件名，保证 macOS、Windows 和 Linux 路径兼容。

每次运行可以产生：

- `report.md`：默认完整报告；
- `result.json`：机器可读结果和元数据；
- `tables/*.csv`：适合表格的数据；
- `sources.json`：来源账本；
- `run.json`：运行参数、版本、步骤和完整性，不含凭据。

用户可用 `--output` 指定其他目录，用 `--format markdown|json` 控制标准输出。CSV 只在存在表格数据时生成。

`--no-save` 只输出到标准输出，不创建运行目录或恢复状态，因此不能与 `--resume` 或 `--allow-partial` 同时使用。所有输出路径先规范化并解析父目录的真实路径；拒绝文件系统根目录、用户主目录本身、仓库 `.git`、越界符号链接和设备文件。默认不覆盖现有文件；只有显式 `--overwrite` 才可替换目标文件。Artifact 使用同目录临时文件加原子重命名写入，并在操作系统支持时使用仅当前用户可读写的目录和文件权限。

默认单个输入文件上限为 50 MiB、单次浏览器下载上限为 100 MiB、结构化文件解析上限为 200,000 行。超过上限时停止并要求用户缩小范围。CSV 输出把来自外部来源且以 `=`, `+`, `-` 或 `@` 开头的字符串转义为文本；经过类型验证的数值仍按数值输出。

## 7. Skill 可移植格式

所有 Skill 采用开放 Agent Skills 目录结构：

```text
skill-name/
├── SKILL.md
├── references/       # 可选
├── scripts/          # 可选
├── assets/           # 可选
└── agents/openai.yaml # Codex 可选元数据，不属于核心运行要求
```

可移植性规则：

- `SKILL.md` 核心 Frontmatter 只依赖 `name` 和 `description`。
- 正文使用“当前 Agent”“可用 MCP”“可用浏览器能力”等能力名称，不把 Codex 当作唯一主机。
- 宿主专用安装路径、配置格式和 UI 元数据放入 `adapters/` 或安装器，不写进通用工作流。
- 跨 Skill 链接必须由 `skillpack.json` 声明依赖；安装器自动安装完整依赖闭包。
- 脚本使用 Node.js 或明确声明的跨平台运行时，不依赖只在单一操作系统存在的命令。
- Codex 和 WorkBuddy 适配器不得改写核心业务规则。

## 8. CLI 命令

### 8.1 自然语言入口

```bash
tiktok-agent chat
tiktok-agent run "分析这个 TikTok 账号：<URL>"
```

`chat` 维护本地会话；`run` 是适合脚本、CI 和其他 Agent 调用的单次非交互命令。

### 8.2 结构化任务入口

```bash
tiktok-agent audit <账号URL>
tiktok-agent shop --market US --category beauty
tiktok-agent category --market US --category beauty
tiktok-agent growth --account <URL> --market US --category beauty --goal "提升自然流量"
tiktok-agent trends --market US --category beauty
tiktok-agent calendar --account <URL> --days 7
tiktok-agent calendar --brief positioning.json --days 30
tiktok-agent video --idea "三种适合新手的自然光拍摄方法"
tiktok-agent review --input analytics.csv
tiktok-agent community --input comments.csv
```

结构化命令和主 Skill 的固定映射与最低输入如下：

| 命令 | 主 Skill | 最低输入 |
| --- | --- | --- |
| `audit` | `tiktok-account-audit` | 一个公开账号 URL |
| `shop` | `tiktok-shop-operator` | 市场，以及类目、关键词、商品、店铺、达人或视频中的至少一项 |
| `category` | `tiktok-category-strategy` | 市场和类目 |
| `growth` | `tiktok-growth-plan` | 公开账号 URL 或可验证的账号诊断 Artifact，以及市场、类目和增长目标 |
| `trends` | `tiktok-trend-radar` | 市场，以及类目或主题；未给时间窗时默认近 7 天 |
| `calendar` | `tiktok-content-planner` | 公开账号 URL 或符合第 9.4 节的定位简报，以及 `7`、`14` 或 `30` 天周期 |
| `video` | `tiktok-video-workbench` | 创意、视频 URL 或本地素材中的至少一项 |
| `review` | `tiktok-performance-review` | Analytics 文件、截图目录或已授权 Studio 范围 |
| `community` | `tiktok-community-operator` | 评论文件、截图或公开视频 URL |

输入不足时，交互式命令进入第 13 节的反问状态机；非交互 `run` 和 CI 调用绝不等待终端输入，而是返回结构化缺失项和稳定退出码。

### 8.3 系统入口

```bash
tiktok-agent skills list
tiktok-agent doctor
tiktok-agent config show
tiktok-agent browser login
tiktok-agent browser status
tiktok-agent browser clear-profile
```

`config show` 只显示配置来源和脱敏状态，不回显密钥。首版不提供把明文 API Key 写入配置文件的命令。

`browser clear-profile` 只删除本地持久化浏览器状态，执行前必须显示确切路径并要求交互确认；非交互环境还必须显式传入 `--yes`。它不修改 TikTok 账号或远端数据。

### 8.4 非交互与部分输出

`run` 在缺少关键输入、需要人工登录或需要用户判断时立即结束，并在 JSON 错误中返回 `code`、`message`、`missing[]`、`nextAction`，以及保存模式下可恢复的 `runId`。第一次失败不自动生成部分结论。用户补充信息后可用 `--resume <runId>` 继续；只有在一次缺失报告之后，用户再次运行同一任务并同时显式传入 `--allow-partial`，才允许生成 C 级有界报告。`--no-save` 模式没有 `runId`，调用方必须在新命令中补齐信息，不能请求部分恢复。

### 8.5 退出码

| 退出码 | 含义 |
| --- | --- |
| `0` | 完整成功，或用户在二次运行中明确允许的有界部分成功 |
| `1` | 未分类的内部错误 |
| `2` | 输入缺失或无效 |
| `3` | 配置、模型或能力不满足 |
| `4` | 认证、权限、登录或访问限制 |
| `5` | 数据源不可用，且尚未获准输出部分报告 |
| `6` | 只读安全策略拒绝 |
| `7` | 用户取消、预算耗尽或超时 |

机器可读错误同时包含稳定字符串错误码，例如 `INPUT_MISSING`、`PROVIDER_CAPABILITY_MISSING`、`AUTH_REQUIRED`、`SOURCE_UNAVAILABLE`、`POLICY_DENIED` 和 `RUN_TIMEOUT`。

## 9. Skill 清单与边界

### 9.1 现有 Skills

| Skill | 边界 |
| --- | --- |
| `tiktok-shop-operator` | 只处理商品、店铺、销量、销售额、带货视频发现/排名、商业达人和 Shop 字幕检索等明确 Shop/commerce 研究任务。 |
| `tiktok-account-audit` | 输入公开账号主页链接，完成账号、竞品和公开内容诊断；不替代私有 Analytics 复盘。 |
| `tiktok-category-strategy` | 输入类目与市场，完成类目进入和定位研究；不承担短周期日常排期。 |
| `tiktok-growth-plan` | 输入账号、类目、市场和商业目标，生成阶段性 30 天增长路线。 |

`tiktok-account-audit` 保留已确认的双层路径：优先通过 MCP 的实时 Schema 调用 `creator_profile` 与 `creator_videos` 能力；工具不可用、返回不完整或字段不足时，再使用只读浏览器读取公开主页和公开视频。账号按证据自动识别为内容型、品牌型、带货型或混合型；证据不足时不得强行归类。用户自述的账号类型只能标为“用户提供，非测量证据”，不能冒充自动识别结果。

### 9.2 新增 Skills

| Skill | 边界 |
| --- | --- |
| `tiktok-content-planner` | 定位已明确时生成选题池、内容系列和 7/14/30 天排期；不重新做账号诊断或类目进入判断。 |
| `tiktok-video-workbench` | 处理单条创意或视频，拆解或生成 Hook、口播、镜头表、字幕、封面、Caption、关键词、CTA 和发布素材检查清单；不发布。 |
| `tiktok-performance-review` | 读取 TikTok Studio、CSV、JSON 或截图，复盘内容表现并输出 `Keep / Stop / Test`；只有公开 URL 时转给账号诊断。 |
| `tiktok-trend-radar` | 按市场、类目和时间窗收集当前搜索需求、内容缺口、趋势和账号适配信号；不做类目进入决策。未给时间窗时默认近 7 天，数据源不支持时使用最接近的可用窗口并披露。 |
| `tiktok-community-operator` | 整理评论主题、问题、情绪、FAQ、回复草稿和评论转视频选题；不回复、删除或屏蔽。 |

### 9.3 确定性路由提示

```text
商品/店铺/销量         → tiktok-shop-operator
公开账号链接           → tiktok-account-audit
类目进入/定位/竞争版图  → tiktok-category-strategy
账号适配/转型/增长路线  → tiktok-growth-plan
既定定位下选题与内容排期→ tiktok-content-planner
单条视频/脚本/创意     → tiktok-video-workbench
Analytics/CSV/复盘     → tiktok-performance-review
近期趋势/内容缺口      → tiktok-trend-radar
评论/FAQ/回复草稿      → tiktok-community-operator
```

现有 Skill 的 `description` 必须同步收窄，防止普通视频、普通达人或非 Shop 字幕任务被 `tiktok-shop-operator` 抢占。

路由按主意图而非周期天数判断。进入类目、定位、竞争格局、商品或达人版图属于 `tiktok-category-strategy`；带“当前”“本周”“近期”“热度”“搜索需求”或“内容缺口”的短期机会属于 `tiktok-trend-radar`。账号适配、转型、商业目标和阶段路线属于 `tiktok-growth-plan`；既定定位下的选题、系列和排期属于 `tiktok-content-planner`，即使排期为 30 天也不转给 Growth。

视频任务按研究对象分流：按商品、店铺、销量或销售额发现和排名多个 commerce 视频属于 `tiktok-shop-operator`；对一个已知视频做字幕提取、结构拆解、改写或脚本生产属于 `tiktok-video-workbench`；账号样本集合的整体诊断属于 `tiktok-account-audit`。Shop 或 Audit 选中具体视频后可以把资产级处理委托给 Workbench，但不得复制其流程。

### 9.4 跨 Skill 交接

- `calendar --account <URL>` 先运行 `tiktok-account-audit`，再把合格的定位摘要交给 `tiktok-content-planner`；Planner 不自行复制账号诊断。
- 可验证账号简报使用 `account-brief.v1`，至少包含 `sourceAccountUrl`、`generatedAt`、采样窗口、字段覆盖、账号类型、定位摘要、可信度、`sourceLedgerRef` 和内容哈希。只有本 CLI 或同一 Skill 规则生成且完整性校验通过的 Artifact 才可作为已测量账号证据；超过 30 天默认视为过期，继续使用必须披露。
- 没有现有账号时，用户可以提供 `positioning-brief.v1`，至少包含市场、类目、目标受众、内容主张和目标。它始终标记为“用户提供”，只能支持 Planner，不能替代 Account Audit 或作为测量证据。
- `growth --account <URL>` 先取得或更新 `account-brief.v1`；`growth` 接受 Artifact 时校验 Schema、来源、哈希和时效，不接受任意自由文本冒充账号诊断结果。

## 10. 数据来源与可信度

### 10.1 来源顺序

以下顺序表示来源优先级与标注要求，不强制所有 Skill 使用相同调用顺序；例如账号诊断仍按第 9.1 节执行 MCP 双层路径与浏览器降级。

1. 用户明确提供的 URL、参数、CSV、JSON、截图和 Analytics 导出。
2. 已连接 MCP 的实时 Schema 与真实返回。
3. 用户手动登录后的 TikTok Studio 可见信息。
4. 公开 TikTok 页面、Creative Center 和 TikTok 官方政策页面。
5. 其他公开网页只可作为明确标记的补充背景，不得代替 TikTok、KSS 或官方数据。

用户陈述与用户文件仍需单独标为“用户提供”，不能伪装为 MCP 或平台直接取得的数据。

### 10.2 来源账本

每项主要结论必须可以追溯到来源账本。账本至少记录：

- 来源类型、工具或 URL；
- 获取时间和时区；
- 市场、账号、类目、关键词和日期窗口；
- 请求范围、页数、样本数和去重后数量；
- 实际字段覆盖、币种和统计周期；
- 缺失字段、错误、访问限制和停止原因；
- 对结论的影响。

### 10.3 可信度

- **A：** 关键结论有完整、可比且可追溯的多层证据。
- **B：** 证据可用，但存在披露的样本、字段、浏览器或口径限制。
- **C：** 只能形成范围有限的初步观察、条件计划或部分报告。

缺失信息不是零。模型推断、用户假设和实际取得的事实必须分开。

## 11. 配置、凭据与本地状态

### 11.1 环境变量

```bash
TIKTOK_AGENT_BASE_URL=
TIKTOK_AGENT_MODEL=
TIKTOK_AGENT_API_KEY=
TIKTOK_AGENT_MCP_CONFIG=
KSS_MCP_KEY=
```

CLI 允许配置文件保存非敏感默认项和“从哪个环境变量读取凭据”，但不把明文密钥写入仓库、运行目录、日志或报告。后续可增加操作系统 Keychain 适配器，首版不要求。

配置优先级固定为：命令行参数 > 环境变量 > 项目级 `.tiktok-agent/config.json` > 操作系统标准用户配置目录 > 内置默认值。用户配置只能保存 Base URL、模型名、MCP 配置路径、浏览器 Profile 名及密钥环境变量名；API Key、Cookie 和 Token 仍只能从环境变量或本地浏览器 Profile 取得。

### 11.2 本地状态

- 聊天历史、运行元数据和浏览器 Profile 只保存在本机。
- 项目级 `.tiktok-agent/` 应加入 `.gitignore`；`doctor` 在未忽略时告警并给出文本，但 CLI 不自动改写用户的 `.gitignore`。
- 持久化浏览器 Profile 默认关闭；启用后放在操作系统标准应用数据目录，并在系统支持时设置为仅当前用户可访问。Runtime 不复制、压缩、备份或上传其中的 Cookie、Local Storage 和 Session Storage。
- 日志、来源账本和 Artifact 只能记录 Profile 名称、路径脱敏值和登录状态，不得记录 Cookie、Storage 内容或 Authorization Header。
- 默认不启用产品遥测。
- Debug 日志仍必须经过凭据、Cookie、Authorization Header 和个人信息脱敏。
- 删除本地历史或浏览器 Profile 必须是用户显式命令，不能自动清理有价值的用户数据。

## 12. 只读安全策略

Runtime 对工具执行进行独立策略检查，不能只依赖模型判断或 MCP 注解。

本设计中的“只读”特指**不改变 TikTok 或其他第三方系统的业务状态**。本地生成 Artifact、用户手动登录产生的会话状态，以及经过白名单验证的报表下载属于明确披露的本地副作用；它们不授权发布、互动、修改账号或向第三方发送数据。

### 12.1 允许

- 查询、搜索、分页、读取、下载用户主动要求的报表；
- 浏览公开页面或用户手动登录后可见的分析页面；
- 读取用户指定的本地文件；
- 在用户指定输出目录生成本地报告、表格和草稿。

### 12.2 拒绝

- 发布、上传到 TikTok、回复、删除、编辑、私信、关注、点赞、投放或修改设置；
- 未在只读白名单中的未知 MCP 工具；
- 需要自动填写账号密码、验证码或绕过访问限制的操作；
- 将凭据、Cookie 或浏览器存储写入 Artifact；
- 未经用户指定向第三方系统发送报告或数据。

若用户请求写操作，CLI 说明当前产品是只读规划工具，生成可人工执行的草稿或清单，并停止在外部动作之前。

### 12.3 外部内容与提示注入

- MCP 返回、网页、字幕、评论、CSV、JSON、截图及视觉模型返回的提取文本和下载文件全部视为不可信数据，不视为系统指令、Skill 指令或用户授权。
- 外部内容中的“忽略规则”“调用某工具”“上传数据”“显示密钥”等文本只能作为待分析内容，不能改变工具白名单、目标域、预算、输出路径或数据发送范围。
- Runtime 用类型化数据块和来源 ID 隔离外部内容；模型产生的每个工具调用仍要通过 Schema 校验、Skill 允许范围和独立只读策略。
- 页面或文件不得授权读取额外本地文件、访问新域名、调用新 MCP 工具或把结果发送给第三方。

### 12.4 本地输出保护

- Artifact Writer 统一执行第 6.7 节的路径规范化、符号链接检查、权限、大小限制、CSV 转义、默认不覆盖和原子写入规则。
- 浏览器下载先进入专用临时目录，通过大小、类型和文件名检查后再移动到目标 Artifact；失败文件不混入正式结果。
- 临时文件中同样不得出现明文密钥、Cookie 或浏览器 Storage；异常退出只保留通过完整性检查的中间 Artifact。

## 13. 信息不足与错误处理

- 初始必需输入缺失时，每次只问一个最高优先级问题，并说明其影响。
- 能通过规范化、分页、有限重试、关联或浏览器降级取得的信息先自行获取。
- 对账号诊断，`creator_profile`、`creator_videos` 和浏览器降级仍无法取得影响核心结论的信息时，必须转为反问，每次只请求一个最关键的 URL、截图、导出文件或业务背景。
- MCP 不存在、失败、为空或字段不足时，按 Skill 规则进入公开或已授权登录浏览器。
- 浏览器自动链路遇到登录阻断、验证码、反爬、地域或年龄限制时立即停止并返回对应原因；用户可以通过 `browser login` 手动完成允许的登录或验证后再恢复任务，Runtime 不自动处理或绕过。
- 模型、MCP 和浏览器只进行有限、带退避的重试；不可无限循环或重试轰炸。
- 收到限流、401、配额耗尽或服务端停止时保留已完成结果，并列出未完成部分。
- 数据仍不足时先问一个最高优先级问题。只有用户在后续一轮明确表示无法或不愿补充，并再次要求继续，才输出证据支持范围内的 C 级有界报告；“继续”本身不能跳过第一次反问。
- 非交互模式不模拟反问：第一次运行返回缺失项和 `runId`；只有后续 `--resume <runId> --allow-partial` 才等价于用户明确要求继续。
- 输出失败时保留已经安全写入的中间 Artifact，并返回稳定非零退出码。

## 14. 测试设计

### 14.1 单元测试

- Frontmatter、Skill 引用和依赖图解析；
- 路由与最小 Skill 集选择；
- Provider 消息、工具调用归一化、HTTPS/Origin 和重定向校验；
- MCP Schema、分页、去重和错误归一化；
- 只读工具策略、外部提示注入隔离与凭据脱敏；
- Markdown、JSON、CSV、来源账本、路径和符号链接安全输出；
- 配置优先级和跨平台路径。

### 14.2 Skill 路由压力测试

每个 Skill 至少包含正向、反向、重叠和缺失输入样本，验证：

- 普通视频或普通达人请求不误触发 Shop Skill；
- 公开账号诊断与私有 Analytics 复盘正确分流；
- 缺少关键输入时只问一个问题；
- 多 Skill 请求只加载最小必要集合；
- 结果前置、来源可追溯、缺失信息可见；
- 用户要求发布时只生成草稿或清单，不调用写工具。

固定路由 Fixture 必须覆盖全部 9 个结构化命令、每个 Skill 至少 10 个自然语言正例与 10 个近邻反例，以及 Shop/非 Shop、公开账号/私有 Analytics、类目策略/近期趋势、增长计划/内容排期四组重点冲突。正确路由的判定是主 Skill 完全匹配预期，附加 Skill 只能来自 Fixture 明示的允许集合。

账号诊断另设固定端到端 Fixture，验证 `creator_profile → creator_videos` 的正常链路，以及任一工具不存在、失败、为空或关键字段不足时进入只读浏览器降级；样本支持时识别混合型，样本不足时反问并保持“暂无法判断”。

### 14.3 模拟集成测试

使用本地假 OpenAI-compatible 服务和假 MCP Server 测试工具调用、分页、超时、限流、401、配额、空结果、部分结果和取消，不消耗真实额度。Fixture 还必须把恶意指令放入 MCP 字段、网页、CSV、JSON 和模拟视觉提取文本，验证它们不能扩大工具权限、读取额外文件、改变输出目录或泄露凭据。另设无视觉 Provider Fixture，验证截图任务确定性返回 `PROVIDER_CAPABILITY_MISSING` 且不生成事实结论。

### 14.4 浏览器测试

使用受控页面 Fixture 验证公开读取、可见浏览器登录流程、本地 Profile、下载、登录阻断、验证码、地区限制和只读策略。测试必须证明模型无法取得通用点击/脚本执行能力，危险下载和写按钮被拒绝，Profile 不进入日志或 Artifact。真实 TikTok 验证必须是可选且有界的测试，不把页面波动当成稳定 Fixture。

### 14.5 跨平台 CI

CI 覆盖：

- macOS、Windows、Linux；
- Node.js 20 和 22；
- npm 安装、构建、类型检查、单元测试、路由测试和 CLI smoke test；
- 无凭据环境下的安全失败；
- Secret、占位符、Markdown 链接和格式扫描；
- 全部退出码、非交互无阻塞、`--no-save`、默认不覆盖、原子写入和 CSV 公式注入测试。

CI 不保存真实 API Key、Cookie、浏览器 Profile 或 TikTok 数据。

### 14.6 宿主适配一致性测试

- Codex、WorkBuddy 和独立 CLI 安装后解析到相同版本、相同内容哈希的 `SKILL.md` 与 `references/`。
- Adapter 只生成宿主安装位置、清单、UI 或 MCP 配置模板，不改写通用 Skill 文件。
- 宿主缺少 MCP、浏览器、视觉或其他必需能力时显式报告缺失项，不静默替换工作流或伪造结果。

## 15. 发布与兼容

### 15.1 npm

```text
Package: @aronhy/tiktok-agent
Binary:  tiktok-agent
Version: 0.1.0
```

发布前必须验证 `npx @aronhy/tiktok-agent doctor`、`tiktok-agent chat`、`tiktok-agent run` 和全部结构化命令的帮助与失败路径。

### 15.2 Codex

Codex 继续直接加载通用 Skill。适配器负责安装路径、可选 `agents/openai.yaml`、MCP 配置提示和仓库文档；不能维护第二套工作流。

### 15.3 WorkBuddy

WorkBuddy 通过其 Skill 导入或已验证的本地安装方式使用相同 Skill 目录。适配器在安装时检测可用位置和版本，不把未经验证的产品路径散落在 Skill 正文中。MCP 配置按 WorkBuddy 当前支持的配置格式生成无密钥模板。

## 16. 实施阶段

所有阶段进入同一个 `0.1.0` 目标，但按以下顺序完成和验证：

1. npm workspace、CLI 骨架、配置和 Artifact 基础。
2. Skill Loader、Router、Agent Runtime 和 OpenAI-compatible Provider。
3. 只读 MCP Client、安全策略和现有 4 个 Skill 接入。
4. 新增 5 个非 Shop 日常运营 Skill，并收窄现有触发描述。
5. Playwright 只读浏览器、用户文件输入和本地会话。
6. Codex/WorkBuddy 适配、README、安装器与跨平台测试。
7. 可选真实验证、发布检查和 npm 发布准备。

每个阶段必须在进入下一阶段前通过其单元、契约或压力测试；不得等到最后一次性验证安全和路由。

## 17. 验收标准

`0.1.0` 只有同时满足以下条件才可进入发布准备：

1. `npx @aronhy/tiktok-agent doctor` 能检查 Node、模型、工具调用、MCP、浏览器和 Skill 完整性。
2. `tiktok-agent chat`、`run` 和结构化命令共用同一 Runtime。
3. 9 个 Skill 均可发现、显式调用和正确自动路由。
4. Skill 依赖闭包可验证，不存在缺失或循环依赖。
5. MCP 不可用时按工作流自动降级或给出部分报告。
6. 每份分析都披露来源、范围、缺失、停止原因和可信度。
7. 所有 TikTok 外部写操作在模型调用前或工具执行前被确定性拒绝。
8. 工作区、Git 历史、npm 包、日志和 Artifact 中不存在真实凭据或 Cookie。
9. macOS、Windows 和 Linux 的支持矩阵测试通过。
10. Codex、WorkBuddy 和独立 CLI 使用同一套通用 Skill 业务规则。
11. 外部内容中的提示注入不能改变系统指令、工具白名单、数据范围或输出位置。
12. 非交互运行不会等待人工输入，并按第 8.5 节返回稳定退出码与结构化恢复信息。
13. Provider 端点、浏览器 Profile、下载和 Artifact 均通过传输、权限、路径、覆盖及脱敏测试。
14. Codex、WorkBuddy 和 CLI 安装后的通用 Skill 文件内容哈希一致，Adapter Conformance 测试通过。
15. 账号诊断固定 Fixture 证明 MCP 双层路径、浏览器降级、混合型识别和信息不足反问均按规则执行。

## 18. 延后事项

以下事项不进入 `0.1.0`：

- 原生 Anthropic、Gemini 或其他 Provider；
- Ollama/LM Studio 专用优化；
- 单文件 Go/Rust 可执行程序；
- GUI、Web 控制台或托管服务；
- 定时任务和后台自动运行；
- TikTok 发布、评论、私信、关注、投放或任何写操作；
- 跨设备同步聊天历史或浏览器 Profile；
- 云端遥测、账户体系和团队协作。

## 19. 参考资料

- Open Agent Skills specification: <https://agentskills.io/specification>
- OpenAI Codex — Build skills: <https://learn.chatgpt.com/docs/build-skills>
- OpenAI Codex CLI commands: <https://learn.chatgpt.com/docs/developer-commands?surface=cli>
- WorkBuddy 自定义 Skills: <https://www.codebuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Practice-Cases/Practice-Eight>
- TikTok Studio: <https://support.tiktok.com/en/using-tiktok/creating-videos/tiktok-studio>
- TikTok Creator Search Insights: <https://support.tiktok.com/en/using-tiktok/growing-your-audience/creator-search-insights>
- TikTok Comment Insights: <https://support.tiktok.com/en/using-tiktok/growing-your-audience/comment-insights-on-tiktok>
- TikTok Creative Center Trends: <https://ads.tiktok.com/help/article/how-to-use-trends>
