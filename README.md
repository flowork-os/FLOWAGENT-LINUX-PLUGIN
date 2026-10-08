<div align="center">

# 🐧 Flowork OS Sovereign Plugin Registry (Linux)

**High-Performance Modular GUI & WASM Extensions for Sovereign AI Agents on Linux**

[![Linux](https://img.shields.io/badge/Platform-Linux%20x86__64%20%7C%20AArch64-FCC624?style=for-the-badge&logo=linux&logoColor=black)](https://github.com/flowork-os/FLOWAGENT-LINUX-PLUGIN)
[![Runtime](https://img.shields.io/badge/Runtime-Node.js%20%7C%20WASM%20%7C%20Canvas%20GUI-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)](https://floworkos.com)
[![Distribution](https://img.shields.io/badge/Distribution-Zero--API%20CDN-00D26A?style=for-the-badge)](https://plugins.floworkos.com)
[![Architecture](https://img.shields.io/badge/Architecture-Nano--Modular%20Sharded-00F5FF?style=for-the-badge)](https://floworkos.com)
[![License](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=for-the-badge)](https://github.com/flowork-os/FLOWAGENT-LINUX-PLUGIN/pulls)

<p align="center">
  <a href="#-overview">Overview</a> •
  <a href="#-verified-sovereign-plugins">Plugins Catalog</a> •
  <a href="#-architecture--specs">Architecture</a> •
  <a href="#-zero-api-cdn-installation">Installation</a> •
  <a href="#-plugin-manifest-specification">Plugin Manifest</a> •
  <a href="#-publishing-guidelines">Publishing</a>
</p>

---

</div>

## 🌟 Overview

The **Flowork OS Linux Plugin Registry** is the curated open-source repository of verified, sovereign extensions and desktop applications engineered specifically for **Flowork OS** and autonomous AI agents on Linux environments.

Unlike traditional monolithic agent plugins that exhaust LLM context windows, Flowork plugins utilize the **Nano-Plug Architecture**:
- ⚡ **Zero Prompt Bloat**: Plugins remain off-context until explicitly summoned or launched by the user or agent.
- 🎨 **Visual Canvas UI + Daemon Engine**: Full dual-process architecture combining reactive HTML5/Canvas interfaces with high-throughput native Node.js/WASM backends.
- 🔒 **Zero-Zombie Sandbox**: Managed IPC lifecycle with automatic port resolution (`$FLOWORK_APP_PORT`) and instant signal termination.
- 🌐 **Zero GitHub API Quota**: Sharded CDN packaging via Cloudflare Edge Gateways and raw tarball streaming.

---

## 📦 Verified Sovereign Plugins

| Icon | Plugin Name | ID | Version | Category | Description | Source & Shard |
| :---: | :--- | :--- | :---: | :--- | :--- | :--- |
| ♟️ | **Sovereign Chess Arena** | `chess` | `1.0.0` | Games & Strategy | Dual-Actor Chess Arena: Human vs Agent AI. Play solo or duel with open Agent chat in real-time. | [`plugins/ch/chess`](plugins/ch/chess) • [`shard`](index/ch/es/chess.json) |
| 📹 | **YouTube Downloader & Suno Studio** | `yt-downloader` | `1.2.0` | Media & Network | Sovereign YouTube Video/Audio Extractor with 59s Anti-Copyright Speed Ramp for Suno AI, Custom Folders, and Multi-Format DSP. | [`plugins/yt/yt-downloader`](plugins/yt/yt-downloader) • [`shard`](index/yt/do/yt_downloader.json) |

*Want to add your plugin to the official Linux store? See the [Publishing Guidelines](#-publishing-guidelines).*

---

## 🏛️ Architecture & Specs

```
FLOWAGENT-LINUX-PLUGIN/
├── index/                        # O(1) Crates.io-style sharded lookup metadata
│   ├── ch/es/chess.json
│   └── yt/do/yt_downloader.json
├── plugins/                      # Sovereign plugin source roots
│   ├── ch/chess/
│   │   ├── plugin.manifest.json  # Plug & Play agnostic manifest
│   │   ├── SKILL.md              # 20-keyword Agent runbook & SOP
│   │   ├── gui/                  # HTML5 / Canvas frontend
│   │   └── engine/               # Node.js / WASM backend
│   └── yt/yt-downloader/
│       ├── plugin.manifest.json
│       ├── SKILL.md
│       ├── gui/
│       └── engine/
├── plugins.json                  # Root registry index
└── README.md
```

### 1. Two-Tier Directory Sharding
To maintain sub-millisecond Git index performance and avoid directory inode saturation as the registry scales to thousands of plugins, all plugins are partitioned:
$$\text{Path} = \text{plugins}/\{id[0..2]\}/\{id\}$$
$$\text{Metadata Shard} = \text{index}/\{id[0..2]\}/\{id[2..4]\}/\{id\}.\text{json}$$

### 2. Dual-Actor IPC Lifecycle
1. **Host Boot**: Flowork OS allocates an available ephemeral TCP port (or honors configured defaults) and sets `FLOWORK_APP_PORT=<PORT>`.
2. **Process Spawn**: The engine background process spawns via `node engine/server.mjs` or native binary.
3. **Canvas Docking**: The graphical interface (`gui/index.html`) is rendered in the Flowork Canvas webview communicating over the allocated port.
4. **Clean Teardown**: Upon tab close or agent unmount, `SIGTERM` kills the background process cleanly without orphan zombies.

---

## 🚀 Zero-API CDN Installation

Flowork agents and users can install verified plugins directly with zero GitHub API consumption using direct raw tarball downloads:

```bash
# Direct CDN Tarball Stream
curl -sL "https://codeload.github.com/flowork-os/FLOWAGENT-LINUX-PLUGIN/tar.gz/main" | \
  tar -xz --strip-components=3 -C ./plugins/ "FLOWAGENT-LINUX-PLUGIN-main/plugins/ch/chess"
```

Or via Flowork Agent CLI:
```bash
flowork plugin install chess
```

---

## 📋 Plugin Manifest Specification

Every plugin includes a mandatory `plugin.manifest.json`:

```json
{
  "id": "chess",
  "name": "Sovereign Chess Arena",
  "version": "1.0.0",
  "author": "Flowork OS & Community",
  "category": "Games & Strategy",
  "icon": "♟️",
  "description": "Dual-Actor Chess Arena: Human vs Agent AI with real-time IPC.",
  "entry": {
    "gui": "gui/index.html",
    "backend": "engine/server.mjs"
  },
  "ipc": {
    "port_env": "FLOWORK_APP_PORT",
    "default_port": 17820
  },
  "dependencies": {
    "system": ["node", "bash"],
    "npm": []
  }
}
```

---

## 🛠️ Publishing Guidelines

1. **Strict 1-Folder / 1-Plugin Isolation**: No dependencies outside the plugin directory.
2. **Mandatory `SKILL.md`**: Must provide agent instructions with exactly 20 English keywords in YAML frontmatter.
3. **Agnostic Port Binding**: Must bind dynamically to `process.env.FLOWORK_APP_PORT || default_port`.
4. **No External CDN Leaks**: All CSS, JavaScript libraries, fonts, and assets must be self-contained locally.
5. **Multi-Architecture Linux Ready**: Ensure compatibility with Ubuntu, Debian, Arch Linux, Fedora, and Alpine.

### Submit via Pull Request
```bash
git checkout -b feature/my-plugin
# Place plugin in plugins/{prefix}/{plugin_id}
git add plugins/ index/ plugins.json
git commit -m "feat(plugin): add my-plugin to Linux registry"
git push origin feature/my-plugin
```

---

## 📄 License & Sovereignty

Licensed under the **MIT License**. Built with sovereign pride by the Flowork OS Community.

Co-authored-by: Flowork OS <agent@floworkos.com>
