# tencent-novnc-chromium-cdp

一键部署 **noVNC + Chromium + CDP 远程可视可控浏览器**，让 AI Agent 与用户共享同一个云端浏览器。

适用于登录、滑块、验证码、扫码、风控验证等需要人工协作的网页自动化场景。

## 最简单用法：让 AI 帮你安装

把下面这句话发给你的 AI Agent：

```text
使用 tencent-novnc-chromium-cdp 技能，帮我部署 noVNC + Chromium 然后用CDP的方式远程控制Chromium浏览器。
```

如果你的 AI Agent 不能直接安装 SkillHub 技能，也可以把这篇腾讯云文章发给它：

https://cloud.tencent.com/developer/article/2670539

AI Agent 可以根据文章步骤部署 noVNC + Chromium + CDP 远程可视可控浏览器。

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
- 可选端口：`5900/tcp`、`9222/tcp`

## 部署特点

- noVNC 网页可视化浏览器
- Chromium 浏览器远程运行
- AI Agent 通过 CDP 控制 Chromium
- 用户可在 noVNC 页面手动处理登录、验证码、滑块
- 部署完成后返回 noVNC 地址和浏览器截图
- 支持 QQbot 发图适配

## 版本

当前版本：`1.0.15`

## 来源

腾讯云开发者社区文章：

《给 OpenClaw 龙虾看的：noVNC + Chromium 远程浏览器搭建与 CDP 控制完全指南》

https://cloud.tencent.com/developer/article/2670539

## License

MIT
