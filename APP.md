# Piano Clash — APP 版本指南

Piano Clash 已经是 **PWA（渐进式网页应用）**，这意味着它现在就能像 App 一样安装使用，
同时也是打包上架应用商店的基础。

## 方案一：直接安装（现在就能用，免费）

### Android（Chrome / Edge）
1. 用 Chrome 打开游戏网址
2. 主菜单会出现「📲 安装 App 到主屏幕」链接，点它
   （或浏览器右上角菜单 → 「安装应用」）
3. 桌面出现 Piano Clash 图标，全屏启动、离线可玩单人模式

### iPhone / iPad（Safari）
1. 用 Safari 打开游戏网址
2. 点底部**分享按钮** → 「**添加到主屏幕**」
3. 桌面出现图标，全屏启动

> 这已经覆盖了 App 的核心体验：桌面图标、全屏、离线。
> 建议先推广这个方式，验证需求后再考虑上架商店。

## 方案二：上架 Google Play（成本低，推荐先做）

使用 **PWABuilder**（微软开源，免费）把 PWA 打包成 Android App（TWA 技术，
Google 官方支持，本质是全屏加载你的网站，自动同步网站更新——**上架后改网页就是改 App**）。

1. 访问 https://www.pwabuilder.com
2. 输入游戏网址 → 它会检测 manifest.json 和 sw.js（本仓库已配好）
3. 点 **Package for Stores → Android** → 下载生成的 `.aab` 包
4. 注册 Google Play 开发者账号（一次性 $25）：https://play.google.com/console
5. 创建应用 → 上传 `.aab` → 填商店信息（截图、描述）→ 提交审核
6. 额外一步：把 PWABuilder 给的 `assetlinks.json` 放到网站的
   `/.well-known/assetlinks.json`（去掉浏览器地址栏必需）

审核通过后（通常 1–3 天），用户就能在 Play 商店搜到 Piano Clash。

## 方案三：上架 Apple App Store（成本高，验证需求后再做）

- 需要 **Apple Developer 账号（$99/年）** + 一台 **Mac（Xcode）**
- 路径 A：PWABuilder 也能生成 iOS 包（基于 WKWebView），用 Xcode 打开、签名、上传
- 路径 B：用 Capacitor 包装（更可控，可以加原生功能如推送通知）
- 注意：Apple 审核对"纯网页包装"的 App 较严格，最好加 1–2 个原生能力
  （如推送通知提醒每日挑战）提高过审率

## 当前 PWA 配置清单（已完成）

| 项目 | 状态 |
|---|---|
| manifest.json（名称/图标/独立窗口） | ✅ |
| 应用图标 192/512px | ✅ `icon-192.png` / `icon-512.png` |
| Service Worker 离线缓存 | ✅ `sw.js`（网络优先，离线回退） |
| iOS 添加到主屏幕 meta | ✅ |
| 应用内安装提示 | ✅ 主菜单「📲 安装 App」 |
| HTTPS 托管 | ✅ Cloudflare Pages |

## 建议节奏

1. **现在**：推广「添加到主屏幕」，零成本
2. **有稳定玩家后**：花 $25 上 Google Play（PWABuilder 半天搞定）
3. **确认付费意愿后**：再投入 App Store（$99/年 + Mac + 审核周期）
