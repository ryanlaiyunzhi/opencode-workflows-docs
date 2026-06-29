# 安装部署指导 / Installation Guide
> 适用版本：**v0.1.5** · [简体中文](#简体中文) · [English](#english)
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
> 运行时依赖（`@opentui/core`、`@opentui/solid`、`solid-js`、`typescript`）随包安装，无需手动处理。
### 2. 安装
```bash
npm install -g @ryanlaiyunzhi/opencode-workflows
```
### 3. 配置插件
本包含**两个插件入口**（Server + TUI，同一个 npm 包），分别由 OpenCode 的两套加载上下文读取：
**Server 入口读 `opencode.json`，TUI 入口读 `tui.json`**。因此需在**两个文件**的 `plugin`
数组里**各写一次包名**：
```jsonc
// opencode.json（或 .opencode/opencode.jsonc）—— 加载 Server 入口（引擎装配 + Hook）
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"]
}
```
```jsonc
// tui.json —— 加载 TUI 入口（/workflows-* 斜杠命令 / Sidebar / 全屏监控）
{
  "$schema": "https://opencode.ai/tui.json",
  "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"]
}
```
> ⚠️ **两个文件都要写**。只写 `opencode.json` → Server 引擎加载但 TUI 的 `/workflows-*` 命令
> 不出现；只写 `tui.json` → 命令在但后台引擎未装配。两者缺一不可。
> ⚠️ **不要**写成 `"@ryanlaiyunzhi/opencode-workflows/tui"`。带 `/tui` 子路径的 spec 会被
> npm 当成本地路径解析而安装失败（`ENOENT`），导致插件加载不出来、命令不出现。子路径写法只适用于
> 「本地文件路径」开发场景（如 `./opencode-workflows/tui.tsx`），**不适用于已发布的 npm 包**。
#### 带运行时参数（可选）
用 `[spec, options]` 元组传参（在 `opencode.json` 里，作用于 Server 引擎）：
```jsonc
{
  "plugin": [
    ["@ryanlaiyunzhi/opencode-workflows@0.1.5", {
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

**推荐方式：锁定版本号，升级时改数字**

把两个文件里的版本号改为新版本（如 `@0.1.5`），重启 OpenCode 即自动安装并加载新版：

```jsonc
// opencode.json
{ "$schema": "https://opencode.ai/config.json", "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"] }
// tui.json
{ "$schema": "https://opencode.ai/tui.json", "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"] }
```

> ⚠️ **不要用 bare 包名（不带版本号）**。OpenCode 会把无版本 spec 缓存为 `@latest`，首次安装后**永久使用缓存**，即使 npm 上已发布新版，重启后仍加载旧版。
>
> ⚠️ **`npm update -g` 对走 plugin 数组的用户不生效**。插件由 OpenCode 自己的缓存管理，与全局 npm 包无关。

#### v0.1.5 升级注意

v0.1.5 包含**破坏性配置变更**：现有 `~/.opencode-workflows/<name>.config.json` 若为旧扁平格式，将被拒绝解析并以空配置降级运行。升级后请用 `/workflows-studio` 的「改配置」入口重新保存。

升级后建议重新执行第 4 步验证命令仍可见。
### 6. 卸载
```bash
npm uninstall -g @ryanlaiyunzhi/opencode-workflows
```
并从 `opencode.json` 与 `tui.json` 的 `plugin` 数组各移除该声明。可选清理：
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
The package ships **two plugin entries** (Server + TUI) read by OpenCode's two loading contexts:
**the Server entry reads `opencode.json`, the TUI entry reads `tui.json`**. So you must write the
**package name once in each of the two files**:
```jsonc
// opencode.json (or .opencode/opencode.jsonc) — loads the Server entry
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"]
}
```
```jsonc
// tui.json — loads the TUI entry (/workflows-* slash commands / sidebar / monitor)
{
  "$schema": "https://opencode.ai/tui.json",
  "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"]
}
```
> ⚠️ **Both files are required.** Only `opencode.json` → the Server engine loads but the TUI
> `/workflows-*` commands won't appear; only `tui.json` → commands appear but no engine. Neither alone suffices.
> ⚠️ Do **not** write `"@ryanlaiyunzhi/opencode-workflows/tui"`. A spec with a `/tui` subpath is
> resolved by npm as a local path and fails to install (`ENOENT`). The subpath form only applies to
> **local file paths** during development, not to published npm packages.
Pass runtime options with the `[spec, options]` tuple form (in `opencode.json`, for the Server engine):
```jsonc
{
  "plugin": [
    ["@ryanlaiyunzhi/opencode-workflows@0.1.5", { "maxConcurrency": 4, "storageRoot": ".opencode/workflows" }]
  ]
}
```
### 4. Verify
Start OpenCode, type `/` in the TUI input — `workflows-run`, `workflows-studio` etc. should appear
in autocomplete. Opening `/workflows-studio` confirms both entries loaded; a Workflow section in
the right sidebar confirms the progress panel is ready.
### 5. Upgrade

**Recommended: pin an explicit version and bump the number to upgrade**

Change the version number (e.g. `@0.1.5`) in both files and restart OpenCode — it automatically installs and loads the new version:

```jsonc
// opencode.json
{ "$schema": "https://opencode.ai/config.json", "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"] }
// tui.json
{ "$schema": "https://opencode.ai/tui.json", "plugin": ["@ryanlaiyunzhi/opencode-workflows@0.1.5"] }
```

> ⚠️ **Do not use a bare package name (no version)**. OpenCode permanently caches the `@latest` resolution — even after a new npm release, restarts will still load the old cached version.
>
> ⚠️ **`npm update -g` has no effect** for plugins loaded via the plugin array. OpenCode manages its own package cache independently of the global npm registry.

#### Upgrading to v0.1.5

v0.1.5 includes a **breaking config change**: existing `~/.opencode-workflows/<name>.config.json` files in the old flat format will be rejected and fall back to empty config. After upgrading, re-save your configuration via `/workflows-studio` → "Edit Config".
### 6. Uninstall
```bash
npm uninstall -g @ryanlaiyunzhi/opencode-workflows
```
Then remove the entry from the `plugin` array in **both** `opencode.json` and `tui.json`. Optionally
clean up run artifacts (`.opencode/workflows/`) and custom scripts (`~/.opencode-workflows/`).
