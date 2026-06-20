# 安装部署指导 / Installation Guide

> [简体中文](#简体中文) · [English](#english)
>
> 📦 npm: https://www.npmjs.com/package/@ryanlaiyunzhi/opencode-workflows

---

## 简体中文

### 1. 环境要求

| 依赖 | 要求 | 校验 |
|------|------|------|
| OpenCode | `>=1.15.13`（已在 1.17.8 实测兼容） | `opencode --version` |
| Node.js | `>=18`（用于全局安装 npm 包） | `node --version` |
| 模型 | OpenCode 已配置可用的 provider/model（子 Agent 会真实调用 LLM） | — |

> 提示：`@ryanlaiyunzhi/opencode-workflows` 只声明 `@opencode-ai/plugin` 为 peerDependency，
> 运行时依赖（`@opentui/core`、`@opentui/solid`、`solid-js`）随包安装，无需手动处理。

### 2. 安装

```bash
npm install -g @ryanlaiyunzhi/opencode-workflows
```

### 3. 配置插件

本包含**两个插件入口**（同一个 npm 包），必须在 OpenCode 配置文件 `opencode.json`
（或 `.opencode/opencode.jsonc`）的 `plugin` 数组里**同时声明两条**：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    // Server 入口：引擎装配 + before-tool 兜底 Hook
    "@ryanlaiyunzhi/opencode-workflows",
    // TUI 入口：7 条 /workflows-* 命令 + Sidebar 面板 + /workflows-monitor 全屏界面
    "@ryanlaiyunzhi/opencode-workflows/tui"
  ]
}
```

> 为什么要写两条：OpenCode 规定单个插件入口只能导出 `server()` 或 `tui()` 之一。本包通过两个
> export 子路径（`.` 和 `./tui`）分别提供，**两条都声明**才能拿到完整能力。

#### 带运行时参数（可选）

用 `[spec, options]` 元组传参。**两入口的 `storageRoot` 必须一致**，否则 TUI 面板读不到
Server 写的运行快照：

```jsonc
{
  "plugin": [
    ["@ryanlaiyunzhi/opencode-workflows", {
      "maxConcurrency": 4,
      "maxAgents": 1000,
      "agentTimeout": 300000,
      "workflowTimeout": 604800000,
      "schemaRetries": 2,
      "storageRoot": ".opencode/workflows"
    }],
    ["@ryanlaiyunzhi/opencode-workflows/tui", { "storageRoot": ".opencode/workflows" }]
  ]
}
```

| 参数 | 默认值 | 含义 |
|------|--------|------|
| `maxConcurrency` | `min(16, CPU核数-2)`，最小 1 | 最大并发子 Agent 数 |
| `maxAgents` | `1000` | 单 Workflow 内 `agent()` 调用总数上限（安全阀） |
| `agentTimeout` | `300000`（300s） | 单个子 Agent 超时；`null`/`<=0` 关闭 |
| `workflowTimeout` | `604800000`（7 天） | 整个 Workflow 超时；`null`/`<=0` 关闭 |
| `schemaRetries` | `2` | 结构化输出 Schema 校验失败重试次数 |
| `storageRoot` | `.opencode/workflows` | 运行产物根目录（两入口须一致） |

### 4. 验证安装

启动 OpenCode 后，在 TUI 输入框敲 `/`，补全列表应能看到 `workflows-run`、`workflows-studio`
等命令；选中 `/workflows-studio` 能进入全屏 Studio，说明两入口都已生效；右侧 Sidebar 出现
Workflow 区域说明进度面板就绪。

### 5. 升级

```bash
# 手动升级到最新版
npm update -g @ryanlaiyunzhi/opencode-workflows

# 或安装指定版本
npm install -g @ryanlaiyunzhi/opencode-workflows@0.1.1
```

**两种升级路径**：

1. **手动升级（推荐，确定性）**：执行上面的 `npm update -g`，把全局包更新到最新版后重启 OpenCode。
2. **自动跟随最新版**：`opencode.json` 的 plugin spec **不带版本号**时（如本文档的配置示例），
   OpenCode 的插件解析对无版本 spec 默认按 `@latest` 处理，重启即拉取已安装的最新全局版本。
   若想锁定版本，在 spec 后显式写版本范围。

升级后建议重新执行第 4 步验证命令仍可见。

### 6. 卸载

```bash
npm uninstall -g @ryanlaiyunzhi/opencode-workflows
```

并从 `opencode.json` 的 `plugin` 数组移除两条声明。可选清理：
- 运行产物：项目下的 `.opencode/workflows/`
- 自定义脚本与全局配置：`~/.opencode-workflows/`

---

## English

### 1. Requirements

| Dependency | Requirement | Check |
|------------|-------------|-------|
| OpenCode | `>=1.15.13` (verified on 1.17.8) | `opencode --version` |
| Node.js | `>=18` (for global npm install) | `node --version` |
| Model | A working provider/model configured in OpenCode | — |

### 2. Install

```bash
npm install -g @ryanlaiyunzhi/opencode-workflows
```

### 3. Configure

The package ships **two plugin entries**. You must declare **both** in the `plugin` array of your
`opencode.json` (or `.opencode/opencode.jsonc`):

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "@ryanlaiyunzhi/opencode-workflows",
    "@ryanlaiyunzhi/opencode-workflows/tui"
  ]
}
```

> Why two: an OpenCode plugin entry can export only one of `server()` / `tui()`. This package
> exposes them via two subpath exports (`.` and `./tui`); declare both for full capability.

Pass runtime options with the `[spec, options]` tuple form. The `storageRoot` of both entries
**must match**:

```jsonc
{
  "plugin": [
    ["@ryanlaiyunzhi/opencode-workflows", { "maxConcurrency": 4, "storageRoot": ".opencode/workflows" }],
    ["@ryanlaiyunzhi/opencode-workflows/tui", { "storageRoot": ".opencode/workflows" }]
  ]
}
```

### 4. Verify

Start OpenCode, type `/` in the TUI input — `workflows-run`, `workflows-studio` etc. should appear
in autocomplete. Opening `/workflows-studio` confirms both entries loaded; a Workflow section in
the right sidebar confirms the progress panel is ready.

### 5. Upgrade

```bash
# Manual upgrade to latest
npm update -g @ryanlaiyunzhi/opencode-workflows
# Or install a specific version
npm install -g @ryanlaiyunzhi/opencode-workflows@0.1.1
```

Two paths: (1) **manual** — run `npm update -g` and restart OpenCode (recommended, deterministic);
(2) **follow latest** — when the plugin spec has no version, OpenCode resolves it as `@latest`, so a
restart picks up the latest installed global version. Pin a version range in the spec to lock it.

### 6. Uninstall

```bash
npm uninstall -g @ryanlaiyunzhi/opencode-workflows
```

Then remove both entries from the `plugin` array. Optionally clean up run artifacts
(`.opencode/workflows/`) and custom scripts (`~/.opencode-workflows/`).
