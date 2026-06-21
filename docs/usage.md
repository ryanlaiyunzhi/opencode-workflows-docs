# 使用指导书 / Usage Guide

> 适用版本：**v0.1.3** · [简体中文](#简体中文) · [English](#english)
>
> 📦 npm: https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows
> 安装与配置见 [安装部署指导](./installation.md)。

---

## 简体中文

### 1. 7 条斜杠命令

生产运行入口是 TUI 的 **7 条 `/workflows-*` 斜杠命令**（每条带一个 `wf-*` 短别名）。命令从输入框
自动补全列表**选中触发**——不要敲「命令 + 空格 + 参数」，带空格的尾随文本会关闭补全面板、整段被当
普通对话发给模型。运行所需参数（如需求文本）由命令选中后**弹出的对话框**收集。

| # | 命令 | 别名 | 作用 |
|---|------|------|------|
| 1 | `/workflows-run` | `/wf-run` | 选 workflow → 输入需求 → 启动后台运行 |
| 2 | `/workflows-studio` | `/wf-studio` | Workflow **类型**增删改查：浏览 / 新建 / 改配置 / 改结构 / 删除 |
| 3 | `/workflows-resume` | `/wf-resume` | 选非 running 的 run → 断点续跑 |
| 4 | `/workflows-cancel` | `/wf-cancel` | 选 running/paused 的 run → 确认 → 取消 |
| 5 | `/workflows-pause` | `/wf-pause` | 选 running 的 run → 暂停 |
| 6 | `/workflows-delete` | `/wf-delete` | 选非 running 的 run → 确认 → 删除运行产物 |
| 7 | `/workflows-monitor` | `/wf-monitor` | 打开三层下钻全屏监控界面 |

### 2. 运行 Workflow：`/workflows-run`

选中后分两步：

1. **选 workflow**：弹出列表列出全部已发现的 workflow（4 个内置 `default-*` + 全局自定义，
   自定义带 `(user)` 标记）。
2. **输入需求**：弹出输入框收集本次运行的需求 / 提示词（如「读取 `C:/需求文档.md` 作为需求进行开发」）。
   这段文本以 `args.feature` 注入脚本，脚本里经 `ctx.args.feature` 取用；留空则脚本回退默认值。

启动后**立即返回 runId**（后台运行，主对话不阻塞），自动跳转 `/workflows-monitor` 全屏界面，
Sidebar 也出现该 run 的实时进度。

四个内置 Workflow：

| name | 阶段 | 说明 |
|------|------|------|
| `default-prd` | PRD | 并行检索 → 任务规划 → 概要设计/PRD 文档 → 审查门控 → 落盘 |
| `default-design` | Design | 同上 + 设计一致性审查维度 |
| `default-code` | Code | 检索 → 环境检测+规划 → TDD 红灯 → 实现 → TDD 绿灯 → 代码审查 |
| `default-test` | Test | 检索 → 规划 → 测试编写 → 执行+覆盖率门控 → 测试审查 → 报告落盘 |

### 3. 类型增删改查：`/workflows-studio`

进入全屏 Studio，首屏是意图菜单（`↑/↓` 选择、`Enter` 进入、`Esc` 逐层返回）：

- **📖 浏览类型 / 查看详情**：查看某类型只读详情（来源 / 描述 / 适用场景 / 阶段 / 脚本路径）。
- **✨ 新建 workflow**：引导式 7 步创建自定义 workflow，落盘到全局目录 `~/.opencode-workflows/`。
- **⚙ 改配置**：选一个类型（含内置 `default-*`）编辑其 `.config.json`。
- **🛠 改结构**：选一个带结构草稿的用户自定义 workflow 编辑完整结构。
- **🗑 删除类型**：选一个用户自定义 workflow，二次确认后删除其源文件（内置不可删）。

#### 3.1 ⭐ 创建自定义 Workflow（自主编排，最重要的能力）

这是 OpenCode Workflows 最核心的能力：**不写一行代码，用对话描述你的流程，框架替你生成可运行的
多 Agent 编排脚本**。适合把「需求→设计→编码→测试」这类反复执行的多步骤工作固化成一个可复用的
workflow。

**怎么启动**

在 `/workflows-studio` 意图菜单选 **「✨ 新建 workflow」**，会向当前对话注入一条触发消息，由模型
用内置的 `workflow-create` 工具**引导你对话式创建**。你只需用自然语言回答它弹出的提问框即可。

**对话里会问你什么**

创建分两种节奏，开头模型会先让你二选一：

| 模式 | 适合 | 流程 |
|------|------|------|
| **详细规划** | 想精确控制每个阶段、资源、产出 | 模型逐项问：阶段划分 → 各阶段资源 → 资源路径 → 审查轮次+产出形态 → 输出路径 |
| **直接生成** | 想快速拿到一个可用版本 | 模型自动推断阶段/资源/产出，直接生成 |

「详细规划」模式下你会被依次问到（都通过提问框选择或在「Type your own answer」直接输入）：

1. **名称**：kebab-case（如 `req-to-code`），模型会先建议、你确认或改名。
2. **描述**：一句话说明用途。
3. **阶段划分**：把流程拆成几个阶段（如 需求分析 → 概要设计 → 详细设计 → TDD 编码）。
4. **各阶段资源**：每个阶段要不要喂外部文件（模板 / 规约 / 审查清单），多选，无则跳过。
5. **资源路径**：上一步选了的类别，当场输入文件路径（相对项目根写 `docs/x.md`，绝对路径写完整盘符）。
6. **审查轮次 + 产出形态**：每阶段审查最多几轮（1–10，默认 3）；每阶段产出是哪类——
   - **文档**：生成一份 MD，由框架落盘（下一步问输出路径）；
   - **代码/文件**：由子 Agent 直接写进仓库（不问输出路径）；
   - **仅中间结果**：只传给下游阶段，不产生文件。
7. **输出路径**：选了「文档」形态的阶段，输入落盘路径模板（支持 `<FEATURE>`/`<RUNID>` 占位符）。

**生成前确认**：模型会给你一段**文字摘要 + ASCII 流程图**，你确认后才真正生成并落盘。

**生成后**

脚本落盘到全局目录 `~/.opencode-workflows/<name>.mjs`（跨项目复用），**自动注册**。随后用
`/workflows-run` 选中它、输入需求即可运行。想再调整：用 `/workflows-studio` 的「改配置」改资源/路径/轮次，
或「改结构」改阶段编排。

**实用建议**

- **路径填写**：要落到项目里就写相对路径 `docs/x.md`，开头**不要加 `/`**（会被当成绝对路径解析到盘符根）。
- **不确定就先「直接生成」**：拿到一个能跑的版本后，再用 studio 逐步改配置精修。
- **资源是「只读输入」**：你填的模板/规约文件只会被子 Agent 用 `read` 读取，不会被改写。
- **阶段别拆太碎**：3 步能完成的别硬拆成 7 步；门控（审查轮次）只在真正需要校验的阶段加。


### 4. 断点续跑与运行态控制

- **resume**：选非 running 的 run 续跑。已完成且 prompt 一致的 agent 调用复用缓存，从首个失配点起重跑。
- **pause**：选 running 的 run 暂停（非终态，可后续 resume）。
- **cancel**：选 running/paused 的 run，二次确认后取消，运行中子 Agent 级联终止。
- **delete**：选非 running 的 run，二次确认后删除全部产物。running 的 run 禁止删除。

### 5. 全屏监控：`/workflows-monitor`

三层下钻：L1 列表（所有 run）→ L2 phases 双栏（阶段 + agent 列表）→ L3 单 agent 详情
（Prompt 全文 / Outcome 输出 / 元信息）。`↑/↓` 选择、`Enter` 进入、`Esc` 返回。该界面直接读盘渲染，
runId 精确不经模型改写。

### 6. 企业配置：`workflows.config.json`

让你为每个 workflow 喂入外部模板 / 规约 / 审查清单并指定产出落盘路径，配置驱动而无需改脚本。

#### 6.0 内置 default workflow 的开箱默认

全新安装、尚未经 `/workflows-studio` 落盘任何 `<name>.config.json` 时，四个内置 workflow 自带以下默认：

| Workflow | 落盘路径 | 审查轮次 | 资源（template/rules/review） |
|----------|---------|---------|------------------------------|
| `default-prd` | `docs/prd.md` | 3 | `none`（占位，待填本地路径） |
| `default-design` | `docs/design.md` | 3 | `none` |
| `default-code` | 不落盘（代码由子 Agent 自行写文件） | 3 | `none` |
| `default-test` | `docs/test.md` | 3 | `none` |

- 资源占位 `none` 即「未配置」：审查门控对未配置资源的维度无对照基准 → 直接判过、不挑问题。
- 经 `/workflows-studio` 的「改配置」填入本地路径并落盘后，`~/.opencode-workflows/<name>.config.json`
  （优先级最高）即覆盖上述默认。
- 这些默认仅对 `default-*` 生效；自定义 workflow 无内置默认，未配置时按空配置运行。

**位置与三层合并**（优先级 项目 ← 全局 ← 脚本默认）：

| 层级 | 路径 | 优先级 |
|------|------|--------|
| 项目级 | `<projectRoot>/workflows.config.json` | 最高 |
| 全局级 | `~/.opencode-workflows/workflows.config.json` | 中 |
| 脚本默认 | 脚本内硬编码 | 最低 |

**纯 per-phase 格式**（按 workflow 名索引，再按阶段嵌套）：

```jsonc
{
  "default-prd": {
    "resources": {
      "PRD": {
        "template": ["./ai-agent/templates/PRD模板.md"],
        "rules": ["./ai-agent/rules/PRD工程规约.md"],
        "review": ["./ai-agent/review/概要设计审查清单.md"]
      }
    },
    "output_path": { "PRD": "docs/<FEATURE>/概要设计文档.md" }
  }
}
```

- `output_path` 支持占位符 `<RUNID>`（短 runId）和 `<FEATURE>`（从需求提取的标签），由引擎运行时替换。
- 框架只解析路径、不读文件内容；模板内容由子 Agent 用 `read` 工具读取。
- 任一文件缺失 / 非法 JSON → 该层视为空，**不报错**，回退脚本默认。

> 注意：旧的「全局扁平格式」已不被接受，必须用 per-phase 格式（推荐用 `/workflows-studio` 的
> 「改配置」重新保存自动写出正确格式）。

### 7. 脚本作者：编排 API

workflow 脚本默认导出 `execute(ctx)`，`ctx` 即编排 API：

| API | 语义 |
|-----|------|
| `ctx.agent(prompt, opts)` | 阻塞执行子 Agent，返回结果；被取消/失败返回 `null` |
| `ctx.parallel(thunks)` | barrier 扇出，全部完成才返回；异常位置置 `null` |
| `ctx.pipeline(items, ...stages)` | 每 item 独立过所有 stage；异常 item 置 null |
| `ctx.phase(title)` | 声明阶段，title 须与 `meta.phases[].title` 精确匹配 |
| `ctx.workflow(name, args)` | 内联调用子 workflow（最多嵌套一层） |
| `ctx.args` | 运行时注入参数（`/workflows-run` 收集的需求填入 `ctx.args.feature`） |
| `ctx.config` | 当前 workflow 名对应的已解析配置块 |
| `ctx.runId` | 当前 Run 的 runId |

`ctx.agent` 的 `opts` 可选：`label`、`phase`、`schema`（结构化输出 JSON Schema）、`model`、
`timeout`、`agentType`（指定自定义 Agent 类型，不指定走默认）。

消费配置示例：

```javascript
export default async function execute(ctx) {
  const feature = ctx.args?.feature || "当前需求"
  const cfg = ctx.config?.resourcesByPhase?.["Main"] ?? {}
  const tplHint = (cfg.template ?? []).length > 0
    ? `\n请用 read 工具读取模板：\n${cfg.template.join("\n")}` : ""
  const draft = await ctx.agent(`为「${feature}」生成文档。${tplHint}`, { label: "生成", phase: "Main" })
  const out = ctx.config?.outputPathByPhase?.["Main"] ?? null
  if (out) await ctx.agent(`请用 write 工具把产物写入：${out}\n\n${draft}`, { label: "落盘", phase: "Main" })
  return { feature, draft }
}
```

### 8. 运行产物目录

所有产物写入 `storageRoot`（默认 `.opencode/workflows/`）：

```
.opencode/workflows/
├── runs/wf_<runId>.json        # Run 快照（状态/进度/token/logs）
├── journal/<runId>/journal.jsonl  # agent() 事件（resume + 审计）
└── scripts/<name>-wf_<runId>.mjs  # 脚本归档副本
```

> 区分：`storageRoot`（运行产物）与全局目录 `~/.opencode-workflows/`（自定义脚本 + 全局配置）是两回事。

### 9. 常见问题

| 现象 | 处理 |
|------|------|
| 补全列表没有 `/workflows-*` | 检查 `opencode.json` 两个入口都已加 |
| Sidebar 无 Workflow 区域 | 两入口都要加，且 `storageRoot` 一致 |
| 自定义 workflow 不被发现 | 放到全局目录 `~/.opencode-workflows/` |
| `ctx.config` 全为空 | 配置块 key 必须等于 workflow 全名 |
| 子 Agent 都超时/失败 | OpenCode 未配置可用模型，配置 provider/model 后重试 |
| run 永远卡在 running | 持有进程崩溃；重启 OpenCode 自动回收为 failed，或 `/workflows-cancel` |

---

## English

### 1. The 7 commands

The production entry is the TUI's **7 `/workflows-*` slash commands** (each with a `wf-*` alias).
Trigger them by **selecting from autocomplete** — do not type "command + space + args", since
trailing text after a space closes autocomplete and the whole line goes to the model as chat.
Runtime parameters (e.g. the requirement text) are collected by a **dialog** after selecting.

| # | Command | Alias | Purpose |
|---|---------|-------|---------|
| 1 | `/workflows-run` | `/wf-run` | pick workflow → enter requirement → start background run |
| 2 | `/workflows-studio` | `/wf-studio` | manage workflow **types**: browse / create / edit config / edit structure / delete |
| 3 | `/workflows-resume` | `/wf-resume` | pick a non-running run → resume |
| 4 | `/workflows-cancel` | `/wf-cancel` | pick a running/paused run → confirm → cancel |
| 5 | `/workflows-pause` | `/wf-pause` | pick a running run → pause |
| 6 | `/workflows-delete` | `/wf-delete` | pick a non-running run → confirm → delete artifacts |
| 7 | `/workflows-monitor` | `/wf-monitor` | open the 3-level drill-down full-screen monitor |

### 2. Run a workflow

Select `/workflows-run`, then: (1) pick a workflow from the list (4 built-in `default-*` + global
custom ones marked `(user)`); (2) enter the requirement, injected as `args.feature` and read via
`ctx.args.feature`. It returns a runId immediately (background, non-blocking) and jumps to the
full-screen monitor.

Built-in workflows: `default-prd`, `default-design`, `default-code`, `default-test`.

### 3. Manage types: `/workflows-studio`

A full-screen wizard with an intent menu: browse/details, create (guided 7-step), edit config
(incl. built-ins), edit structure (custom with draft only), delete (custom only; built-ins can't be
deleted). New workflows are saved to the global directory `~/.opencode-workflows/` for reuse.

### 4. Resume & lifecycle control

- **resume**: re-run a non-running run, reusing cached agent results with matching prompts, re-running from the first mismatch.
- **pause**: pause a running run (resumable).
- **cancel**: cancel a running/paused run (with confirmation); sub-agents are terminated.
- **delete**: delete all artifacts of a non-running run (with confirmation).

### 5. Full-screen monitor

Three levels: L1 run list → L2 phases + agent list → L3 single-agent detail (full prompt /
outcome / metadata). Reads from disk directly, so runIds are exact.

### 6. Enterprise config

Feed external templates / rules / review checklists and output paths per workflow via
`workflows.config.json`, merged across three layers (project ← global ← script default). Uses the
**per-phase format** (keyed by workflow name, then nested by phase). `output_path` supports
`<RUNID>` / `<FEATURE>` placeholders replaced at runtime. Missing/invalid files degrade silently to
script defaults. The legacy flat format is no longer accepted.

### 7. Scripting API

A workflow script default-exports `execute(ctx)`. Key members: `ctx.agent(prompt, opts)`,
`ctx.parallel(thunks)`, `ctx.pipeline(items, ...stages)`, `ctx.phase(title)`,
`ctx.workflow(name, args)`, `ctx.args`, `ctx.config`, `ctx.runId`. The `opts` of `ctx.agent`
accepts `label`, `phase`, `schema`, `model`, `timeout`, `agentType`.

### 8. Run artifacts

Everything is written under `storageRoot` (default `.opencode/workflows/`): `runs/` snapshots,
`journal/` events (resume + audit), `scripts/` archived script copies. Note this is distinct from
the global directory `~/.opencode-workflows/` (custom scripts + global config).

### 9. Troubleshooting

| Symptom | Fix |
|---------|-----|
| No `/workflows-*` in autocomplete | ensure the package name is in `opencode.json` (single entry; do NOT append `/tui`) |
| No sidebar Workflow section | ensure the plugin loaded; check `opencode.json` and `storageRoot` |
| Custom workflow not found | put it in `~/.opencode-workflows/` |
| `ctx.config` empty | config block key must equal the full workflow name |
| Sub-agents time out / fail | configure a working provider/model in OpenCode |
| Run stuck at running | restart OpenCode to auto-recover as failed, or `/workflows-cancel` |
