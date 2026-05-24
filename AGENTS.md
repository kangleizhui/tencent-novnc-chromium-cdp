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
使用 tencent-novnc-chromium-cdp 技能，帮我部署 noVNC + Chromium 然后用CDP的方式远程控制Chromium浏览器。
```

## 部署前必须提醒用户

开始前先告诉用户：

```text
开始部署 noVNC + Chromium + CDP 远程可视可控浏览器。

服务器建议至少 2H4G。
必须开放 6080/tcp。
预计耗时通常 5-15 分钟，网络慢或首次安装依赖可能 20-30 分钟。
顺利部署预计约 2万-5万 tokens；如遇报错、自动修复或多次重试，可能达到 5万-10万+ tokens。请确认剩余 Token 大于预计消耗，建议预留 10万 tokens 以上。
部署过程中我会每 30 秒检查一次进度，每分钟汇报一次进度。
```

## 关键要求

- 需要 root 或免密 sudo 权限。
- `6080/tcp` 必须在云服务器安全组放行。
- Chromium/Chrome 安装策略：deb 版优先；deb 安装失败后，自动切换至系统可用的其他 Chromium/Chrome 版本。
- 启动后先打开 `https://www.baidu.com/`。
- 完成后必须发送 noVNC 地址和浏览器截图。
- 如果是 QQbot/QQ 通道，截图文件放到 `~/.openclaw/media/qqbot/`，并用 `<qqmedia>` 标签发送。

## 验证命令

部署完成后至少验证：

```bash
ss -tlnp | grep -E '6080|5900|9222'
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:6080/vnc.html
curl -s http://127.0.0.1:9222/json/version
```

预期：

- `6080` 正在监听
- noVNC 页面 HTTP 状态码是 `200`
- CDP `/json/version` 有返回

## 完成回复

完成后回复用户：

```text
远程 Chromium 浏览器已启动：

- noVNC 地址：http://<服务器IP>:6080/vnc.html
- 浏览器状态：已自动打开百度页面，并已附上截图用于确认浏览器正常运行
- 端口提醒：请确认云服务器安全组已放行 6080/tcp
- 如果遇到登录、验证码、滑块，请在 noVNC 页面手动完成，完成后告诉我继续。
```

普通通道发送：

```text
MEDIA:/tmp/remote-browser.png
```

QQbot/QQ 通道发送：

```text
<qqmedia>/root/.openclaw/media/qqbot/remote-browser.png</qqmedia>
```
