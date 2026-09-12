# 第三方与后端改动任务清单

计划日期：2026-09-13  
工作分支：`codex/security-foundation`

这份清单只收录需要 Firebase、Google Cloud、Gemini API 或托管平台配置配合的事项。纯前端改动不放在这里。

## P0：Firebase 数据权限与防作弊

- [ ] 导出并审查当前 Firebase Realtime Database Rules。
- [ ] 默认拒绝未经验证的读写；确认匿名用户和 Google 登录用户的产品需求。
- [ ] 玩家只能写入 `rooms/{roomId}/players/{auth.uid}` 下允许的字段。
- [ ] 只有房主 UID 可以修改房间设置、开始比赛和发起重赛。
- [ ] 使用 Rules 校验 `hp`、`score`、`progress`、`bars`、`ready` 和状态转换范围。
- [ ] 禁止普通客户端删除其他玩家或清理整个房间。
- [ ] 使用 Firebase Emulator Suite 编写通过与拒绝测试。
- [ ] 部署规则前在测试项目验证，不直接在生产项目试错。

验收标准：修改浏览器请求或控制台变量不能替自己加分、恢复血量、冒充房主或修改对手状态。

## P0：权威比赛服务

- [ ] 建立 Cloud Functions 或 Cloud Run 服务处理房间加入、开始和结束。
- [ ] 使用事务保证房间最多两位玩家，解决并发加入竞争。
- [ ] 使用服务器时间戳，避免依赖玩家设备时间。
- [ ] 服务端裁决胜负并保证赢家只能写入一次。
- [ ] 服务端验证攻击、进度和分数变化是否合法。
- [ ] 将过期房间清理由客户端迁移到定时后端任务。
- [ ] 加入断线、重连、重复提交和重赛的幂等处理。

验收标准：两个客户端同时加入不会超员；双方冲线时只产生一个确定结果；刷新或断线后状态可以恢复。

## P0：私人房间凭据

- [ ] 停止把私人房间明文密码写入 Realtime Database。
- [ ] 由 callable function 或受保护的 HTTPS endpoint 验证密码。
- [ ] 使用带盐密码哈希，或改用高熵邀请令牌。
- [ ] 限制失败次数并记录不含密码的安全事件。
- [ ] 数据迁移或删除现存房间中的旧明文密码。

验收标准：读取房间数据、浏览网络请求或打开客户端源码都无法取得房间密码。

## P0：Gemini 识谱代理

- [ ] 建立后端图片识谱端点，浏览器不再直接调用 Gemini。
- [ ] 将 Gemini Key 放入 Google Secret Manager 或等效机密存储。
- [ ] 对端点做 Firebase Auth 身份验证与 App Check。
- [ ] 限制 MIME 类型、文件大小、图片像素、请求时间和每用户频率。
- [ ] 服务端清理 Gemini 输出，并只返回定义好的 JSON schema。
- [ ] 配置预算、配额告警和滥用防护。
- [ ] 确定图片保留策略，默认处理后立即删除。

验收标准：浏览器代码、localStorage、sessionStorage、URL 和日志中都不出现服务端 Gemini Key。

## P1：Firebase App Check 与环境隔离

- [ ] 为 Web 应用启用 Firebase App Check。
- [ ] 建立 development、staging、production 三套环境配置。
- [ ] 为每套环境使用独立数据库、OAuth 设置和服务凭据。
- [ ] 限制 Firebase API Key 可使用的 API 与允许来源。
- [ ] 检查 Google 登录授权域名和 OAuth consent screen。

## P1：托管安全配置

- [ ] 在 hosting/CDN 配置 Content-Security-Policy。
- [ ] 配置 `X-Content-Type-Options: nosniff`、`Referrer-Policy` 和 `Permissions-Policy`。
- [ ] CSP 稳定后移除 `unsafe-inline`，改用外部脚本或 nonce/hash。
- [ ] 限制 camera、microphone、MIDI 等权限到实际需要的页面来源。
- [ ] 检查 HTTPS、缓存策略与 service worker 更新策略。

## P1：监控与发布

- [ ] 接入不包含密码、Token、API Key 或图片内容的错误监控。
- [ ] 对房间创建失败、连接失败、函数错误率和 Gemini 额度建立告警。
- [ ] GitHub Actions 中加入 Firebase Rules 测试、前端测试和 staging 部署。
- [ ] 所有第三方改动先部署 staging，完成双人实机测试后再发布 production。

## 明日执行顺序

1. 备份当前 Firebase Rules 和生产配置。
2. 建立或确认 staging Firebase 项目。
3. 编写并测试 Realtime Database Rules。
4. 实现安全加入房间与密码验证函数。
5. 实现服务端胜负裁决的最小版本。
6. 搭建 Gemini 代理和 Secret Manager。
7. 配置 App Check、托管安全响应头与监控。
8. 在 staging 做双设备、断线和作弊测试。
