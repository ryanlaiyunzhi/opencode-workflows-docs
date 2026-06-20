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

[简体中文](#简体中文) · [English](#english)

---

## 简体中文

### 这是什么

OpenCode Workflows 是一个基于 OpenCode Plugin SDK 的**通用多 Agent 编排框架**（OpenCode 版
Claude Code Workflows）。你用 ESM 脚本（`.mjs`）描述「多个子 Agent 如何并行 / 流水线 / 门控协作」，
框架负责后台执行、并发控制、状态持久化、断点续跑和 TUI 实时进度展示。

### 核心能力

- **多 Agent 编排**：并行扇出（`parallel`）、流水线（`pipeline`）、审查门控 Ralph Loop（`gate`）、
  子 Workflow 内联（`subflow`）。
- **双插件入口**：Server（引擎装配 + Hook）与 TUI（7 条 `/workflows-*` 命令 + Sidebar 面板 +
  全屏监控）。
- **四个内置 Workflow**：PRD / Design / Code / Test，开箱即用。
- **企业配置桥接**：用 `workflows.config.json` 喂入模板 / 规约 / 审查清单并指定产出路径，配置驱动而无需改脚本。
- **引导式创建**：全屏向导生成自定义 Workflow，落盘全局目录跨项目复用。

### 与 Claude Code Workflows 的对应

| 能力 | Claude Code | OpenCode Workflows |
|------|-------------|--------------------|
| 多 Agent 并行编排 | `/workflows` | `/workflows-run` + `.mjs` 脚本 |
| 确定性脚本化编排 | Workflow 定义 | ESM `execute(ctx)` + 编排 API |
| 进度可视化 | 内置 | Sidebar 面板 + `/workflows-monitor` 全屏 |
| 自定义编排 | Workflow 工具 | `/workflows-studio` 引导式创建 |

### 快速开始

1. **安装**：`npm install -g @ryanlaiyunzhi/opencode-workflows`
2. **配置**：在 `opencode.json` 的 `plugin` 数组加两条（见 [安装部署指导](./docs/installation.md)）。
3. **使用**：启动 OpenCode，敲 `/workflows-run` 选 Workflow 并输入需求（见 [使用指导](./docs/usage.md)）。

### 文档

- 📥 [安装部署指导](./docs/installation.md) —— 环境要求、npm 安装、配置、升级、卸载
- 📖 [使用指导书](./docs/usage.md) —— 7 条命令详解、创建 / 运行 / 监控、企业配置、脚本编排 API

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
- **Two plugin entries**: Server (engine assembly + hooks) and TUI (7 `/workflows-*` commands +
  sidebar panel + full-screen monitor).
- **Four built-in workflows**: PRD / Design / Code / Test, ready out of the box.
- **Enterprise config bridge**: feed templates / rules / review checklists and output paths via
  `workflows.config.json` — config-driven, no script edits needed.
- **Guided creation**: a full-screen wizard generates custom workflows saved to a global directory
  for cross-project reuse.

### Getting started

1. **Install**: `npm install -g @ryanlaiyunzhi/opencode-workflows`
2. **Configure**: add two entries to the `plugin` array in `opencode.json` (see
   [Installation Guide](./docs/installation.md)).
3. **Use**: start OpenCode, run `/workflows-run`, pick a workflow and enter your requirement (see
   [Usage Guide](./docs/usage.md)).

### Docs

- 📥 [Installation Guide](./docs/installation.md) — requirements, npm install, config, upgrade, uninstall
- 📖 [Usage Guide](./docs/usage.md) — the 7 commands, create / run / monitor, enterprise config, scripting API

---

## License

[MIT](https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows) © ryanlaiyunzhi
