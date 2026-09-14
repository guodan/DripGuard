# 滴速守卫 · 输液助手 (Drip Guard)

一个**零服务器成本**、可离线使用的渐进式 Web 应用（PWA），用于医院陪护场景。家属通过「换药打卡」与「Tap 节拍敲击」，快速估算输液剩余时间，并在滴完前提早收到响铃 / 震动提醒。

> 本工具仅作陪护参考，实际滴速与用药安全请以医护人员判断为准。

---

## 功能特性

- **换药打卡**：点一下记录本次换药的起始时间 `T₀`。
- **容量与滴系数选择**：液量 `100 / 250（默认）/ 500 / 自定义` ml；滴系数默认 `15 滴/ml`，可切换 `20` 或自定义。
- **Tap 节拍采样**：顺着药水滴落节奏轻敲屏幕 5 下，每下带涟漪动画与震动（`navigator.vibrate`）；自动取间隔均值推算滴速 `R = 60000 / 平均间隔(ms)`（滴/分钟）。
- **时间推算**：`总滴数 = 容量 × 滴系数`，`总时长 = 总滴数 / R`，`预计滴完时间 = T₀ + 总时长`。
- **倒置输液瓶倒计时**：瓶内液面随倒计时**自上而下降**，剩余不足 15% 转橙色警示、滴完转绿色。
- **提前响铃提醒**：默认提前 5 分钟用 Web Audio 蜂鸣 + 震动提醒（提前量可调）。
- **后台保活**：开启后循环播放一段静音 AudioContext 音频，防止移动端浏览器切后台或锁屏时计时器被系统挂起。
- **状态持久化**：计时进度写入 `localStorage`，刷新或重开可续算。
- **PWA 安装**：支持「添加到主屏幕」（Android / 桌面 Chrome·Edge 以及 iOS Safari 均可），离线秒开。

---

## 技术栈

- 纯原生 **HTML5 + CSS + JavaScript**，单文件 `index.html`，**无任何外部依赖 / 无构建步骤**。
- **Service Worker**（`sw.js`）：网络优先 + 自动更新策略，保证改版后用户能看到最新内容；缓存应用外壳实现离线。
- **Web Manifest**（`manifest.json`）+ PNG 图标（含 `maskable`），满足各平台「添加到主屏幕」要求。
- 部署平台：GitHub Pages 或 Vercel，**完全零成本**。

---

## 本地使用

直接用浏览器打开 `index.html` 即可体验完整流程（测速、倒计时、瓶子动画、响铃）。

若想体验完整的「添加到主屏幕 / 离线」能力，需通过本地静态服务器（SW 要求 `https` 或 `localhost`）访问，例如：

```bash
# 任意静态服务器均可，下面用 Python 内置服务器举例
python -m http.server 8123
# 然后浏览器打开 http://127.0.0.1:8123/
```

> 注意：`localhost` 下 Service Worker 可正常注册；`file://` 协议下 PWA 能力不可用。

---

## 部署

### GitHub Pages

1. 将本仓库文件推送到 GitHub（已在 `guodan/DripGuard`）。
2. 仓库 **Settings → Pages → Branch** 选择 `master`，目录选择 `/(root)`，保存。
3. 约 1 分钟后访问 `https://guodan.github.io/DripGuard/`。

> 仓库根目录已含 `.nojekyll`，避免 GitHub Pages 用 Jekyll 处理静态资源。

### Vercel

将本目录拖拽到 [vercel.com/new](https://vercel.com/new)，自动识别为静态站点，无需额外配置。

---

## 浏览器兼容性

| 平台 | 安装到主屏幕 | 离线 | 备注 |
|---|---|---|---|
| Android · Chrome / Edge | 支持 | 支持 | 完整体验 |
| 桌面 · Chrome / Edge | 支持 | 支持 | 完整体验 |
| iOS · Safari | 支持 | 支持 | 已提供 PNG 图标（`apple-touch-icon`），主屏图标正常显示 |

---

## 开源协议

本项目以 **MIT 协议** 开源，详见 [LICENSE](./LICENSE)。

你可以自由地使用、修改、分发本项目的代码，包括用于商业用途，只需保留原始版权声明与许可声明。

### 贡献

欢迎通过 Issue 与 Pull Request 提出建议或改进。提交前请确保：

- 保持「零外部依赖、零构建步骤」的设计原则；
- 若改动 `index.html` / `sw.js`，请将 `sw.js` 中的缓存版本号 `CACHE` 递增，以便用户及时更新。

---

## 目录结构

```
DripGuard/
├── index.html            # 单页应用主程序（含全部业务逻辑与 UI）
├── manifest.json         # PWA 安装元数据
├── sw.js                 # Service Worker（网络优先 + 自动更新）
├── icon.svg              # 普通 SVG 图标
├── icon-maskable.svg     # maskable SVG 图标
├── icon-192.png          # 192×192 PNG（含 iOS apple-touch-icon）
├── icon-512.png          # 512×512 PNG
├── icon-maskable-512.png # 512×512 maskable PNG
├── .nojekyll             # 禁用 GitHub Pages 的 Jekyll 处理
├── .gitignore
├── LICENSE               # MIT 协议
└── README.md
```
