# Piano Clash 修改前版本存档

- 存档日期：2026-09-12
- 原始分支：`main`
- 原始提交：`e7b1739302c070d7d6f5998a1388d1de0c1d8ea2`
- 存档分支：`archive/pre-security-improvements-2026-09-12`
- 改进分支：`codex/security-foundation`

## 恢复方法

需要恢复修改前版本时，可从存档分支检出或创建新分支：

```bash
git switch archive/pre-security-improvements-2026-09-12
```

存档分支保留修改前的完整应用，包括当时的 `index.html`、资源文件与项目文档。

## 第一阶段修改范围

1. 阻止多人房间、玩家昵称、歌曲元数据和错误信息中的 HTML 注入。
2. 将用户自行提供的 Gemini API Key 从长期存储改为当前会话存储。
3. 为后续 Firebase 权限、房间密码和服务端裁决改造建立独立开发分支。
