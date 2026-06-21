# 安装部署指导 / Installation Guide

> 适用版本：**v0.1.3** · [简体中文](#简体中文) · [English](#english)
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

本包含**两个插件入口**（Server + TUI，同一个 npm 包）。以 **npm 包**形式安装时，只需在
OpenCode 配置文件 `opencode.json`（或 `.opencode/opencode.jsonc`）的 `plugin` 数组里
**写一次包名**——OpenCode 会自动探测包的 `exports["./tui"]` 与 `main`，把 Server 与 TUI
两个入口都加载起来：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    // 写一次包名即可，Server + TUI 两个入口由 OpenCode 自动探测加载
    "@ryanlaiyunzhi/opencode-workflows"
  ]
}
```

> ⚠️ **不要**写成 `"@ryanlaiyunzhi/opencode-workflows/tui"`。带 `/tui` 子路径的 spec 会被
> npm 当成本地路径解析而安装失败（`ENOENT`），导致插件加载不出来、命令不出现。子路径 / 多条
> 写法只适用于「本地文件路径」开发场景（如 `./opencode-workflows/tui.tsx`），**不适用于已发布
> 的 npm 包**——npm 包由 OpenCode 自动探测 `exports` 的 `./server` / `./tui`，写一次包名即可。

#### 带运行时参数（可选）

用 `[spec, options]` 元组传参（写一次包名，参数同时作用于 Server 与 TUI 两个入口）：

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
    }]
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

The package ships **two plugin entries** (Server + TUI). When installed as an **npm package**, you
only need to declare the **package name once** — OpenCode auto-detects the package's
`exports["./tui"]` and `main` and loads both the Server and TUI entries:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [
    "@ryanlaiyunzhi/opencode-workflows"
  ]
}
```

> ⚠️ Do **not** write `"@ryanlaiyunzhi/opencode-workflows/tui"`. A spec with a `/tui` subpath is
> resolved by npm as a local path and fails to install (`ENOENT`), so the plugin never loads and no
> commands appear. The subpath / multi-entry form only applies to **local file paths** during
> development; published npm packages are auto-detected from their `exports` (`./server` / `./tui`),
> so a single package name is enough.

Pass runtime options with the `[spec, options]` tuple form (one entry; options apply to both the
Server and TUI sides):

```jsonc
{
  "plugin": [
    ["@ryanlaiyunzhi/opencode-workflows", { "maxConcurrency": 4, "storageRoot": ".opencode/workflows" }]
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
