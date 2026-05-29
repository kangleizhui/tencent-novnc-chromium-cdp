# tencent-novnc-chromium-cdp

一键部署 **noVNC + Chromium + CDP 远程可视可控浏览器**，让 AI Agent 与用户共享同一个云端浏览器。

适用于登录、滑块、验证码、扫码、风控验证等需要人工协作的网页自动化场景。

支持 **Linux（云服务器/NAS/本地）** 和 **Windows 本机** 双平台。

## 最简单用法：让 AI 帮你安装

把下面这句话发给你的 AI Agent：

```text
使用 tencent-novnc-chromium-cdp 技能
```

## SkillHub 技能文件

本仓库核心文件是：

```text
SKILL.md
```

你可以把 `SKILL.md` 复制到支持 Skill 的 AI Agent 中使用。

## 服务器要求

- 推荐配置：至少 2 核 CPU / 4GB 内存（2H4G）
- 系统推荐：Debian 10+ / Ubuntu 20.04+
- 必须开放：`6080/tcp`
- CDP 端口：`9223`（仅供本机 Agent 使用，不建议公网开放）
- VNC 端口：`5900/tcp`（可选）

## 部署特点

- noVNC 网页可视化浏览器（带随机密码保护）
- Chromium 浏览器远程运行
- AI Agent 通过 CDP 控制 Chromium
- 用户可在 noVNC 页面手动处理登录、验证码、滑块
- 部署完成后返回 noVNC 地址、访问密码和浏览器截图
- 支持 QQbot 发图适配
- 支持 systemd 开机自启

## 版本

当前版本：`1.0.35`

## License

MIT
