# OpenCode Workflows

> OpenCode 版 Claude Code Workflows —— 基于 OpenCode Plugin SDK 的通用多 Agent 编排框架。
> OpenCode-native Claude Code Workflows — a general-purpose multi-agent orchestration framework built on the OpenCode Plugin SDK.

[![npm version](https://img.shields.io/npm/v/@ryanlaiyunzhi/opencode-workflows.svg)](https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows)
[![npm downloads](https://img.shields.io/npm/dm/@ryanlaiyunzhi/opencode-workflows.svg)](https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows)
[![license](https://img.shields.io/npm/l/@ryanlaiyunzhi/opencode-workflows.svg)](https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows)

```bash
npm install -g @ryanlaiyunzhi/opencode-workflows
```

📦 **npm**: https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows

> **当前版本：v0.1.5** · 升级方式：把配置里的版本号改为新版本（如 `@0.1.5`→`@0.1.6`）后重启 OpenCode；详见 [安装部署指导 · 升级章节](./docs/installation.md)。
> 变更记录见 npm 包内 `CHANGELOG.md`。

[简体中文](#简体中文) · [English](#english)

---

## 简体中文

### 这是什么

OpenCode Workflows 是一个基于 OpenCode Plugin SDK 的**通用多 Agent 编排框架**（OpenCode 版
Claude Code Workflows）。你用 ESM 脚本（`.mjs`）描述「多个子 Agent 如何并行 / 流水线 / 门控协作」，
框架负责后台执行、并发控制、状态持久化、断点续跑和 TUI 实时进度展示。

### 核心能力

- **多 Agent 编排**：并行扇出（`branch`）、流水线（`pipeline`）、审查门控 Ralph Loop（`gate`）、
  子 Workflow 内联（`subflow`）。
- **双插件入口**：Server（引擎装配 + Hook）与 TUI（8 条 `/workflows-*` 命令 + Sidebar 面板 +
  全屏监控）。
- **四个内置 Workflow**：PRD / Design / Code / Test，开箱即用（自带默认配置）。
- **企业配置桥接**：为每个 workflow 喂入模板 / 规约 / 审查清单并指定产出路径，配置驱动而无需改脚本。
- **⭐ 对话式创建（自主编排）**：敲 `/workflows-create`，用一句话描述你的流程，模型多轮对话引导，
  框架自动生成可运行的多 Agent 编排脚本并落盘全局目录、跨项目复用——**不写一行代码即可自定义 workflow**
  （也可用 `/workflows-studio` 的「新建」走表单式向导）。

### 与 Claude Code Workflows 的对应

| 能力 | Claude Code | OpenCode Workflows |
|------|-------------|--------------------|
| 多 Agent 并行编排 | `/workflows` | `/workflows-run` + `.mjs` 脚本 |
| 确定性脚本化编排 | Workflow 定义 | ESM `execute(ctx)` + 编排 API |
| 进度可视化 | 内置 | Sidebar 面板 + `/workflows-monitor` 全屏 |
| 自定义编排 | Workflow 工具 | `/workflows-create`（对话式）/ `/workflows-studio`（表单式） |

### 快速开始

1. **安装**：`npm install -g @ryanlaiyunzhi/opencode-workflows`
2. **配置**：在 `opencode.json` 与 `tui.json` 的 `plugin` 数组各写一次包名，tui.json需新建到同目录下
   （见 [安装部署指导](./docs/installation.md)）。
3. **运行内置**：启动 OpenCode，敲 `/workflows-run` 选 Workflow 并输入需求。
4. **⭐ 自建流程**：敲 `/workflows-create`，一句话描述你的流程，模型引导你对话式生成
   （见 [使用指导 · 创建你的 Workflow](./docs/usage.md)）。

### 文档

- 📥 [安装部署指导](./docs/installation.md) —— 环境要求、npm 安装、配置、升级、卸载
- 📖 [使用指导书](./docs/usage.md) —— 8 条命令详解、⭐ 创建自定义 workflow、运行 / 监控、企业配置、脚本编排 API

---

## English

### What is it

OpenCode Workflows is a general-purpose **multi-agent orchestration framework** built on the
OpenCode Plugin SDK (an OpenCode-native take on Claude Code Workflows). You describe how multiple
sub-agents collaborate (in parallel / pipeline / review-gated) with ESM scripts (`.mjs`); the
framework handles background execution, concurrency control, state persistence, resume, and live
TUI progress display.

### Key features

- **Multi-agent orchestration**: parallel fan-out (`parallel`), pipeline (`pipeline`),
  review-gated Ralph Loop (`gate`), inline sub-workflow (`subflow`).
- **Two plugin entries**: Server (engine assembly + hooks) and TUI (8 `/workflows-*` commands +
  sidebar panel + full-screen monitor).
- **Four built-in workflows**: PRD / Design / Code / Test, ready out of the box (with default config).
- **Enterprise config bridge**: feed templates / rules / review checklists and output paths per
  workflow — config-driven, no script edits needed.
- **⭐ Conversational creation (self-orchestration)**: run `/workflows-create`, describe your process
  in one line, and the model guides you through multi-turn dialog to generate a runnable multi-agent
  script saved to a global directory for cross-project reuse — **no code required** (or use
  `/workflows-studio` → "create" for a form-based wizard).

### Getting started

1. **Install**: `npm install -g @ryanlaiyunzhi/opencode-workflows`
2. **Configure**: add the package name to the `plugin` array in **both** `opencode.json` and
   `tui.json` (once each; see [Installation Guide](./docs/installation.md)).
3. **Run built-ins**: start OpenCode, run `/workflows-run`, pick a workflow and enter your requirement.
4. **⭐ Build your own**: run `/workflows-create`, describe your process in one line, and let the
   model guide you (see [Usage Guide · Create your workflow](./docs/usage.md)).

### Docs

- 📥 [Installation Guide](./docs/installation.md) — requirements, npm install, config, upgrade, uninstall
- 📖 [Usage Guide](./docs/usage.md) — the 8 commands, ⭐ create custom workflows, run / monitor, enterprise config, scripting API

---

## License

[MIT](https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows) © ryanlaiyunzhi
