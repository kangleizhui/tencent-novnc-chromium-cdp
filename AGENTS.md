# AGENTS.md — tencent-novnc-chromium-cdp 部署指南

你正在帮助用户部署 `tencent-novnc-chromium-cdp`：一套 noVNC + Chromium + CDP 远程可视可控浏览器环境。

核心目标：让用户通过 noVNC 网页看到并手动操作浏览器，让 AI Agent 通过 CDP 控制同一个 Chromium 浏览器。

## 使用方式

优先读取并执行仓库根目录的：

```text
SKILL.md
```

推荐用户提示词：

```text
使用 tencent-novnc-chromium-cdp 技能
```

## 关键要求

- 需要 root 或免密 sudo 权限。权限不足时首选引导用户配置免密 sudo，备选请求 SSH 凭证。
- `6080/tcp` 必须在云服务器安全组放行。
- `9223/tcp`（CDP 调试端口）仅供 Agent 本机使用，不建议对公网开放。
- Chromium/Chrome 安装策略：deb 版优先；deb 安装失败后，自动切换至系统可用的其他版本。
- **严禁给 Chromium 添加 `--headless` 或 `--headless=new` 启动参数**，否则 noVNC 画面无法显示。
- 启动后先打开 `https://www.baidu.com/` 确认浏览器正常。
- 部署完成后必须按顺序执行三步收尾：打开百度 → 截图 → 发送回复。
- noVNC 访问密码随机生成（8 位字母+数字混合），必须发给用户。

## 验证命令

部署完成后至少验证：

```bash
ss -tlnp | grep -E '6080|5900|9223'
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:6080/vnc.html
curl -s http://127.0.0.1:9223/json/version
```

预期：

- `6080` 正在监听
- noVNC 页面 HTTP 状态码是 `200`
- CDP `/json/version` 有返回

## 完成回复

部署完成后按 SKILL.md 第八节模板回复用户，必须包含：noVNC 地址、访问密码、百度截图、开机自启状态。

QQbot/QQ 通道截图发送：

```bash
mkdir -p ~/.openclaw/media/qqbot
cp /tmp/remote-browser.png ~/.openclaw/media/qqbot/remote-browser.png
```

```text
<qqmedia>/root/.openclaw/media/qqbot/remote-browser.png</qqmedia>
```

其他通道：

```text
MEDIA:/tmp/remote-browser.png
```
