---
name: tencent-novnc-chromium-cdp
description: "一键部署 noVNC + Chromium + CDP 远程可视可控浏览器，让 AI Agent 与用户共享同一个云端浏览器，适用于登录、滑块、验证码等需要人工协作的网页自动化场景。"
version: 1.0.15
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [novnc, chromium, cdp, xvfb, x11vnc, remote-browser]
---

# noVNC + Chromium + CDP 远程可视可控浏览器

来源文章：腾讯云开发者社区《给 OpenClaw 龙虾看的：noVNC + Chromium 远程浏览器搭建与 CDP 控制完全指南》
https://cloud.tencent.com/developer/article/2670539

如果暂时无法安装本技能，也可以直接把上面这篇文章链接发给你的 AI Agent。AI Agent 可以读取文章内容，并根据文章步骤为你部署 noVNC + Chromium + CDP 远程可视可控浏览器。

## 技能简介

本技能用于在 Linux 云服务器上部署一套 **远程可视化 Chromium 浏览器环境**：用户通过 noVNC 在网页里实时查看和手动操作浏览器，AI Agent 通过 Chrome DevTools Protocol（CDP）控制同一个 Chromium 实例。

它解决的是传统 Headless 浏览器自动化最常见的痛点：遇到登录、扫码、滑块、验证码、风控验证时，AI 可以暂停并让用户在 noVNC 页面中手动完成验证，验证通过后再继续由 AI 接管操作。

适合用于抖音创作者中心、小红书、微博、番茄小说、各类后台管理系统等需要“AI 自动化 + 人工验证协作”的网页场景。

## 核心能力

- **可视化浏览器**：Chromium 运行在 Xvfb 虚拟屏幕上，用户可通过 noVNC 网页实时查看画面。
- **AI 可控**：Agent 通过 CDP 连接 `127.0.0.1:9222`，可执行打开网页、点击、输入、读取页面、截图等操作。
- **人机协作**：遇到登录、二维码、滑块、验证码时，用户可在 noVNC 中手动处理，处理完成后 AI 继续执行。
- **权限预检**：安装前先确认 root/sudo 权限，权限不足时主动提醒用户授权方式，避免半安装状态。
- **安全边界**：默认只建议开放 noVNC 的 `6080/tcp`，CDP 调试端口 `9222/tcp` 不建议暴露到公网。
- **完成反馈**：部署完成后，Agent 必须主动发送 noVNC 访问地址、当前浏览器截图和端口开放提醒。

## 推荐使用提示词

用户可以直接把下面任意一句发给 AI Agent 使用本技能：

```text
使用 tencent-novnc-chromium-cdp 技能，帮我部署 noVNC + Chromium 然后用CDP的方式远程控制Chromium浏览器。
```

如果用户希望部署完成后立刻拿到访问地址，可以使用：

```text
技能 tencent-novnc-chromium-cdp 执行好了以后，请把 Chromium 的 noVNC 网站访问地址发给我，并附上当前浏览器截图。
```

Agent 收到以上提示后，应完成以下动作：

1. 加载并执行 `tencent-novnc-chromium-cdp` 技能；
2. 开始部署前，先向用户发送配置、端口和预计耗时提醒；
3. 检查服务器配置、系统环境和 root/sudo 权限；
4. 安装过程中每 30 秒检查一次部署进度和关键进程状态，如发现依赖安装失败、进程未启动、端口未监听等问题，应先尝试自动修复，避免安装中断；
5. 部署期间每分钟向用户发送一次简短进度消息，说明当前执行到哪一步，直到部署成功或遇到必须人工处理的问题；
6. 安装并启动 Xvfb、Chromium、x11vnc、noVNC/websockify；
7. 首次启动后自动打开 `https://www.baidu.com/`，用于确认浏览器和网络访问正常；
8. 检测服务器公网 IP，生成 noVNC 访问地址；
9. 截取当前 Chromium 浏览器画面，截图中应能看到百度页面；
10. 主动回复用户 noVNC 地址、百度页面截图和端口开放提醒。

## 服务器配置要求与端口提醒

### 开始部署前必须先提醒用户

本技能需要安装系统依赖、下载 noVNC、启动 Chromium 图形浏览器和 VNC 转发服务，步骤较多。Agent 在正式执行安装前，应先发一条简短提醒，让用户知道部署需要等待一段时间。

推荐提醒话术：

```text
开始部署 noVNC + Chromium 远程可视浏览器。

重要提示：服务器建议至少 2H4G。
端口提醒：必须在云服务器安全组放行 6080/tcp；5900/tcp 和 9222/tcp 为高级用途，可选开放，开放时建议限制来源 IP。
预计耗时：通常约 5-15 分钟；如果服务器配置较低、网络较慢或需要安装大量依赖，可能需要 20-30 分钟。部署过程中请耐心等待，不要中断。
Token 提醒：顺利部署预计消耗约 2万-5万 tokens；如果网络较慢、依赖安装失败、需要自动修复或多次重试，可能消耗 5万-10万+ tokens，实际以执行过程为准。开始前请确认当前会话/套餐剩余 Token 必须大于预计消耗，建议至少预留 10万 tokens 以上，避免部署过程中因 Token 不足中断。
进度提醒：安装过程中我会每 30 秒检查一次进度和服务状态，如有常见问题会先自动修复；并且每分钟向你汇报一次部署进度，直到安装成功。
```

如果检测到服务器配置低于 2H4G，应额外提醒用户可能出现 Chromium 卡顿、白屏或被系统杀掉的问题。

### 安装过程进度监控与自动修复

部署本技能时，Agent 不应长时间静默等待。正式开始安装后，应执行以下进度监控规则：

1. **每 30 秒检查一次进度**：检查当前安装命令是否仍在运行，查看最近日志，确认是否卡在依赖安装、软件下载、服务启动或端口监听阶段。
2. **每 30 秒检查关键状态**：重点检查 `Xvfb`、`chromium`、`x11vnc`、`websockify/noVNC` 是否启动，`6080/tcp` 和 `9222/tcp` 是否正常监听。
3. **常见问题优先自动修复**：如依赖缺失、包管理锁、残留旧进程、端口占用、Chromium 启动失败、noVNC 未下载完整等，应先尝试清理旧进程、重新安装依赖、重新启动服务或重新下载组件。
4. **每分钟汇报一次进度**：向用户发送简短消息，说明当前阶段，例如“正在安装系统依赖”“正在启动 Chromium”“正在检测 6080 端口”“正在截图确认百度页面”。
5. **不要刷屏**：进度消息保持简短，每分钟一次即可；只有遇到需要用户处理的问题时才立即额外提醒。
6. **直到成功或需要人工介入**：持续监控并汇报，直到 noVNC 地址可访问、Chromium 已打开百度页面、截图已生成；如果自动修复失败，应明确告诉用户失败步骤、原因和下一步需要用户做什么。

推荐进度消息示例：

```text
部署进度：正在安装系统依赖，已开始自动检查安装状态，请耐心等待。
部署进度：依赖安装完成，正在启动 Xvfb 和 Chromium。
部署进度：Chromium 已启动，正在启动 noVNC 并检测 6080 端口。
部署进度：noVNC 已可访问，正在打开百度页面并截图确认。
```

### 最低服务器配置

本技能会启动 Chromium 图形浏览器、虚拟显示器和 VNC 转发服务，资源占用高于普通命令行脚本。建议服务器配置不低于：

```text
最低推荐：2 核 CPU / 4GB 内存（2H4G）
系统推荐：Debian 10+ / Ubuntu 20.04+
磁盘建议：至少预留 5GB 可用空间
```

低于 2H4G 的服务器也可能启动成功，但容易出现 Chromium 卡顿、页面白屏、验证码页面 CPU 飙高、浏览器被系统杀掉等问题。

### 端口开放说明

必须开放：

- `6080/tcp`：noVNC 网页访问端口。用户通过 `http://<服务器公网IP>:6080/vnc.html` 打开远程 Chromium 浏览器。

可选开放：

- `5900/tcp`：VNC 原生端口。默认仅供本机 websockify 转发使用，一般不需要公网开放；只有需要用 VNC 客户端直连时才考虑开放，并建议限制来源 IP。
- `9222/tcp`：Chromium CDP 调试端口。默认仅供 Agent 在服务器本机控制浏览器使用；只有明确需要远程调试时才考虑开放，并必须限制来源 IP，避免浏览器被他人接管。

给用户的简短提醒可以写成：

```text
重要提示：服务器建议至少 2H4G。
端口提醒：必须在云服务器安全组放行 6080/tcp；5900/tcp 和 9222/tcp 为高级用途，可选开放，开放时建议限制来源 IP。
预计耗时：通常约 5-15 分钟；网络较慢或首次安装依赖时可能需要 20-30 分钟，请耐心等待。
Token 提醒：顺利部署预计约 2万-5万 tokens；如遇报错、自动修复或多次重试，可能达到 5万-10万+ tokens。开始前请确认剩余 Token 必须大于预计消耗，建议至少预留 10万 tokens 以上，以免部署中断。
进度提醒：安装过程中 AI 会每 30 秒检查一次进度和服务状态，遇到常见问题会先自动修复，并且每分钟向你汇报一次部署进度，直到安装成功。
```

## 适用场景

当用户需要在云服务器上启动一个**可视化浏览器**，让用户通过网页实时看到浏览器画面，同时让 AI Agent 可以通过 Chrome DevTools Protocol（CDP）控制浏览器时，使用本技能。

典型场景：

- 登录类后台：抖音创作者中心、小红书创作者平台、微博、公众号后台等。
- 风控验证：滑块验证、扫码登录、短信验证、图形验证码、设备验证等。
- 内容发布：自动打开发布页、填写标题/正文/标签/封面，并让用户处理最后的人工验证。
- 网页调试：需要用户和 AI 同时看到同一个浏览器画面，方便排查页面状态。

不适合场景：

- 纯 API 调用任务，不需要浏览器画面。
- 对安全要求极高、不能暴露任何远程桌面入口的生产环境。
- 无法开放 `6080/tcp` 或无法提供 root/sudo 权限的服务器。

最终效果：

- 用户访问：`http://<服务器IP>:6080/vnc.html`
- Agent 控制：`http://127.0.0.1:9222/json` / CDP WebSocket
- 浏览器运行在虚拟显示器 `:99`

数据流：

```text
Chromium → Xvfb 虚拟屏幕 → x11vnc → noVNC/websockify → 用户浏览器
```

## 使用示例：打开抖音创作者平台

部署完成后，Agent 可以通过 CDP 打开目标网站，用户通过 noVNC 观看和接管：

```bash
python3 - <<'PY'
import json, time, urllib.request
import websocket

target_url = 'https://creator.douyin.com/'
tabs = json.load(urllib.request.urlopen('http://127.0.0.1:9222/json'))
page = next(t for t in tabs if t.get('type') == 'page')
ws = websocket.create_connection(page['webSocketDebuggerUrl'], header=['Origin: http://127.0.0.1:9222'], timeout=20)

msg_id = 0
def call(method, params=None):
    global msg_id
    msg_id += 1
    ws.send(json.dumps({'id': msg_id, 'method': method, 'params': params or {}}))
    while True:
        msg = json.loads(ws.recv())
        if msg.get('id') == msg_id:
            return msg

call('Page.enable')
call('Runtime.enable')
call('Page.navigate', {'url': target_url})
time.sleep(5)
res = call('Runtime.evaluate', {
    'expression': '({title: document.title, url: location.href})',
    'returnByValue': True,
})
print(json.dumps(res['result']['result']['value'], ensure_ascii=False, indent=2))
ws.close()
PY
```

预期效果：

- noVNC 页面中能看到 Chromium 打开的抖音创作者平台；
- 如果页面要求扫码/验证码/滑块，用户可在 noVNC 中手动完成；
- 验证完成后，Agent 继续通过 CDP 自动操作当前浏览器。

## 组件说明

| 组件 | 作用 |
|---|---|
| Xvfb | 提供虚拟显示器，例如 `:99` |
| Chromium | 真正的浏览器，开启 CDP 端口 `9222` |
| x11vnc | 把 Xvfb 画面转换成 VNC 协议 |
| noVNC + websockify | 把 VNC 转成网页可访问的 WebSocket 页面 |

## 一、检查系统与服务器配置

```bash
cat /etc/os-release | head -3
uname -m
nproc
free -h
df -h /
```

推荐环境：Debian 10+ / Ubuntu 20.04+ / 较新的 Linux 发行版。

配置要求：建议至少 **2 核 CPU / 4GB 内存（2H4G）**。如果内存低于 4GB，Agent 应先提醒用户可能出现 Chromium 卡顿、白屏或被系统杀掉的问题，再继续安装。

## 二、权限预检（必须）

部署本技能需要安装系统包、写入 `/opt` 和 `/root/start-remote-browser.sh`，通常需要 `root` 或可用的 `sudo` 权限。Agent 在执行安装前必须先确认权限：

```bash
if [ "$(id -u)" = "0" ]; then
  echo "PERMISSION_OK: 当前是 root 用户"
elif command -v sudo >/dev/null 2>&1 && sudo -n true 2>/dev/null; then
  echo "PERMISSION_OK: 当前用户可免密 sudo"
else
  echo "PERMISSION_DENIED: 当前用户不是 root，且无法免密 sudo"
fi
```

如果输出 `PERMISSION_DENIED`，不要继续强行安装；必须先提醒用户需要授权。**不要直接要求用户在聊天里发送 root 密码**，因为聊天记录可能被保存或转发。优先建议用户选择以下安全方式之一：

1. 用户自己切换到 root 后重新运行 Agent；
2. 用户给当前账号配置临时 sudo 权限；
3. 用户提供临时服务器/临时 root 凭据，并在部署完成后立即修改密码或删除临时账号；
4. 如果平台支持安全密钥/密文变量/Secret 输入，优先通过该方式提供凭据。

可发送给用户的提醒话术：

```text
当前账号没有足够权限部署 noVNC + Chromium。
这个技能需要安装系统依赖、写入 /opt 和 /root/start-remote-browser.sh，因此需要 root 或 sudo 权限。

请你任选一种方式处理：
1. 切换到 root 账号后重新让我执行；
2. 给当前账号配置 sudo 权限；
3. 提供临时 root/有 sudo 权限的账号，部署完成后请立刻修改密码或删除临时账号；
4. 如果你的平台支持 Secret/密文输入，请优先用 Secret，不建议直接在聊天里发送长期 root 密码。
```

后续命令可使用下面的 `SUDO` 变量兼容 root 和 sudo 用户：

```bash
if [ "$(id -u)" = "0" ]; then SUDO=""; else SUDO="sudo"; fi
```

## 三、安装依赖

Debian / Ubuntu：

```bash
apt update && apt upgrade -y
apt install -y xvfb x11vnc git python3-pip curl wget ca-certificates
```

### 优先安装 deb 版 Chromium/Chrome

本技能需要通过 CDP 控制浏览器，安装浏览器时推荐**优先安装 deb 包形式的 Chromium/Chrome**。如果 deb 版安装失败，再自动切换至系统可用的其他 Chromium/Chrome 版本，保证部署流程尽量不中断。

安装时应先检测系统类型，并按“deb 版优先、失败后自动切换至其他版本”的策略执行：

```bash
. /etc/os-release

if [ "$ID" = "debian" ]; then
  apt install -y chromium || apt install -y chromium-browser || true
elif [ "$ID" = "ubuntu" ]; then
  echo "Ubuntu 环境优先安装 deb 版 Chrome 作为 Chromium/CDP 兼容浏览器。"
  if wget -O /tmp/google-chrome-stable_current_amd64.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb; then
    apt install -y /tmp/google-chrome-stable_current_amd64.deb || true
  fi
  if command -v google-chrome-stable >/dev/null 2>&1; then
    ln -sf /usr/bin/google-chrome-stable /usr/local/bin/chromium
  else
    echo "deb 版 Chrome 安装失败，尝试安装系统源 Chromium/Chromium Browser。"
    apt install -y chromium || apt install -y chromium-browser || true
  fi
else
  apt install -y chromium || apt install -y chromium-browser || true
fi

CHROME_BIN=$(command -v chromium || command -v chromium-browser || command -v google-chrome-stable || true)
if [ -z "$CHROME_BIN" ]; then
  echo "ERROR: 未找到 Chromium/Chrome 可执行文件"
  exit 1
fi

echo "浏览器路径：$CHROME_BIN"
```

安装 noVNC 和 websockify：

```bash
cd /opt
if [ ! -d /opt/noVNC ]; then
  git clone https://github.com/novnc/noVNC.git
fi
pip3 install websockify --break-system-packages
```

如果 noVNC master 版本在 Chrome 中出现 ES module 报错，可固定到稳定版：

```bash
cd /opt/noVNC
git fetch --tags
git checkout v1.6.0
```

## 四、开放端口

服务器本机防火墙：

```bash
ufw allow 6080/tcp || true
ufw allow 9222/tcp || true
ufw reload || true
```

云厂商安全组也要放行：

- `6080/tcp`：noVNC 网页访问端口
- `9222/tcp`：CDP 调试端口（如不需要外网访问 CDP，可只允许本机）

## 五、创建一键启动脚本

写入 `/root/start-remote-browser.sh`：

```bash
cat > /root/start-remote-browser.sh <<'SCRIPT_END'
#!/bin/bash
set -e

pkill -9 -f x11vnc 2>/dev/null || true
pkill -9 -f Xvfb 2>/dev/null || true
pkill -9 -f websockify 2>/dev/null || true
pkill -9 -f 'chromium.*remote-debugging-port=9222' 2>/dev/null || true
sleep 2

echo "[1/4] 启动虚拟显示器..."
Xvfb :99 -screen 0 1920x1080x24 &
sleep 1
export DISPLAY=:99

echo "[2/4] 启动 Chromium (CDP:9222)..."
CHROME_BIN=$(command -v chromium || command -v chromium-browser || command -v google-chrome-stable || true)
if [ -z "$CHROME_BIN" ]; then
  echo "ERROR: 未找到 Chromium/Chrome 可执行文件"
  exit 1
fi
"$CHROME_BIN" \
  --no-sandbox \
  --disable-gpu \
  --disable-dev-shm-usage \
  --remote-debugging-port=9222 \
  --remote-debugging-address=0.0.0.0 \
  --remote-allow-origins='*' \
  --window-size=1920,1080 \
  --start-maximized \
  --no-first-run \
  --no-default-browser-check \
  --user-data-dir=/root/.chromium-remote \
  https://www.baidu.com/ &
sleep 3

echo "[3/4] 启动 x11vnc..."
x11vnc -display :99 -forever -nopw -quiet -listen 127.0.0.1 &
sleep 1

echo "[4/4] 启动 noVNC (端口:6080)..."
websockify --web /opt/noVNC 6080 127.0.0.1:5900 &
sleep 1

IP=$(hostname -I | awk '{print $1}')
echo ""
echo "========================================"
echo "  ✅ 远程浏览器已就绪"
echo "  用户访问: http://${IP}:6080/vnc.html"
echo "  CDP: http://${IP}:9222/json"
echo "========================================"
SCRIPT_END

chmod +x /root/start-remote-browser.sh
```

## 六、启动远程浏览器

```bash
bash /root/start-remote-browser.sh
```

启动完成后，用户访问：

```text
http://<服务器IP>:6080/vnc.html
```

CDP 检查地址：

```bash
curl -s http://127.0.0.1:9222/json/version
curl -s http://127.0.0.1:9222/json
```

## 七、通过 CDP 打开网页

使用 Python 控制已启动的 visible Chromium：

```bash
python3 - <<'PY'
import json, time, urllib.request
import websocket

url = 'https://example.com'
tabs = json.load(urllib.request.urlopen('http://127.0.0.1:9222/json'))
page = next(t for t in tabs if t.get('type') == 'page')
ws = websocket.create_connection(
    page['webSocketDebuggerUrl'],
    header=['Origin: http://127.0.0.1:9222'],
    timeout=20,
)

msg_id = 0
def call(method, params=None):
    global msg_id
    msg_id += 1
    ws.send(json.dumps({'id': msg_id, 'method': method, 'params': params or {}}))
    while True:
        msg = json.loads(ws.recv())
        if msg.get('id') == msg_id:
            return msg

call('Page.enable')
call('Runtime.enable')
call('Page.navigate', {'url': url})
time.sleep(3)
res = call('Runtime.evaluate', {
    'expression': '({title: document.title, url: location.href})',
    'returnByValue': True,
})
print(json.dumps(res['result']['result']['value'], ensure_ascii=False, indent=2))
ws.close()
PY
```

如果缺少 websocket 包：

```bash
pip3 install websocket-client --break-system-packages
```

## 八、完成后必须回复用户

启动成功后，Agent 必须把以下内容发送给用户，不要只在终端里打印链接：

1. noVNC 浏览器访问地址：
   `http://<服务器IP>:6080/vnc.html`

2. 首次打开百度页面并截图：
   - 安装/启动完成后，Agent 必须先让 Chromium 打开 `https://www.baidu.com/`
   - 然后截图发给用户，用于确认远程浏览器正常运行、noVNC 画面正常、服务器可以访问外网

3. 当前浏览器截图：
   使用命令：
   `DISPLAY=:99 import -window root /tmp/remote-browser.png`

   如果当前对话通道是 **QQbot/QQ 通道**，发图方式比较特殊：截图文件必须放到 `~/.openclaw/media/qqbot/` 目录下，并使用 `<qqmedia>` 标签发送，不能只使用普通 `MEDIA:` 路径。

   ```bash
   mkdir -p ~/.openclaw/media/qqbot
   cp /tmp/remote-browser.png ~/.openclaw/media/qqbot/remote-browser.png
   ```

   QQbot 回复中使用：

   ```text
   <qqmedia>/root/.openclaw/media/qqbot/remote-browser.png</qqmedia>
   ```

   非 QQbot 通道可继续使用普通媒体附件格式：

   ```text
   MEDIA:/tmp/remote-browser.png
   ```

4. 提醒用户开放端口：
   - 云服务器安全组需要放行 `6080/tcp`
   - 如果系统开启了防火墙，也需要放行 `6080/tcp`
   - `9222/tcp` 是 CDP 调试控制端口，默认只建议 Agent 本机使用，不建议对公网开放

### 1. 检测服务器公网 IP 并生成 noVNC 地址

优先检测服务器公网 IP；如果能检测到公网 IP，就发公网地址。检测失败时，再退回本机内网 IP。

```bash
PUBLIC_IP=$(curl -4 -s https://ifconfig.me 2>/dev/null || curl -4 -s https://api.ipify.org 2>/dev/null || true)
if [ -n "$PUBLIC_IP" ]; then
  NOVNC_URL="http://${PUBLIC_IP}:6080/vnc.html"
else
  LOCAL_IP=$(hostname -I | awk '{print $1}')
  NOVNC_URL="http://${LOCAL_IP}:6080/vnc.html"
fi
echo "$NOVNC_URL"
```

### 2. 首次打开百度并截取当前 Chromium 浏览器画面

启动后必须先打开百度页面，用百度页面作为“浏览器正常运行”的可视化确认页：

```bash
python3 - <<'PY'
import json, time, urllib.request
import websocket

tabs = json.load(urllib.request.urlopen('http://127.0.0.1:9222/json'))
page = next(t for t in tabs if t.get('type') == 'page')
ws = websocket.create_connection(page['webSocketDebuggerUrl'], header=['Origin: http://127.0.0.1:9222'], timeout=20)

msg_id = 0
def call(method, params=None):
    global msg_id
    msg_id += 1
    ws.send(json.dumps({'id': msg_id, 'method': method, 'params': params or {}}))
    while True:
        msg = json.loads(ws.recv())
        if msg.get('id') == msg_id:
            return msg

call('Page.enable')
call('Page.navigate', {'url': 'https://www.baidu.com/'})
time.sleep(3)
ws.close()
PY
```

如果服务器安装了 ImageMagick 的 `import` 命令：

```bash
DISPLAY=:99 import -window root /tmp/remote-browser.png
mkdir -p ~/.openclaw/media/qqbot
cp /tmp/remote-browser.png ~/.openclaw/media/qqbot/remote-browser.png
```

注意：只有检测到用户来自 QQbot/QQ 通道时，才必须复制到 `~/.openclaw/media/qqbot/` 并使用 `<qqmedia>` 标签；其他平台优先使用平台原生媒体发送方式。

### 3. 回复格式示例

Agent 完成安装/启动后，必须向用户发送类似下面的回复：

```text
远程 Chromium 浏览器已启动：

- noVNC 地址：http://<服务器IP>:6080/vnc.html
- 浏览器状态：已自动打开百度页面，并已附上截图用于确认浏览器正常运行
- CDP 地址：仅供 Agent 本机控制使用，不建议公开
- 端口提醒：请确认云服务器安全组已放行 6080/tcp；如果系统开启防火墙，也要放行 6080/tcp
- 如果遇到登录、验证码、滑块，请在 noVNC 页面手动完成，完成后告诉我继续。

MEDIA:/tmp/remote-browser.png
```

如果用户来自 QQbot/QQ 通道，截图必须改用下面这种格式发送：

```text
远程 Chromium 浏览器已启动：

- noVNC 地址：http://<服务器IP>:6080/vnc.html
- 浏览器状态：已自动打开百度页面，并已附上截图用于确认浏览器正常运行
- CDP 地址：仅供 Agent 本机控制使用，不建议公开
- 端口提醒：请确认云服务器安全组已放行 6080/tcp；如果系统开启防火墙，也要放行 6080/tcp
- 如果遇到登录、验证码、滑块，请在 noVNC 页面手动完成，完成后告诉我继续。

<qqmedia>/root/.openclaw/media/qqbot/remote-browser.png</qqmedia>
```

也可以用 CDP 截图：

```bash
python3 - <<'PY'
import json, base64, urllib.request
import websocket

tabs = json.load(urllib.request.urlopen('http://127.0.0.1:9222/json'))
page = next(t for t in tabs if t.get('type') == 'page')
ws = websocket.create_connection(page['webSocketDebuggerUrl'], header=['Origin: http://127.0.0.1:9222'], timeout=30)
ws.send(json.dumps({'id': 1, 'method': 'Page.captureScreenshot', 'params': {'format': 'jpeg', 'quality': 70}}))
while True:
    msg = json.loads(ws.recv())
    if msg.get('id') == 1:
        data = base64.b64decode(msg['result']['data'])
        open('/tmp/remote-browser-cdp.jpg', 'wb').write(data)
        print('/tmp/remote-browser-cdp.jpg')
        break
ws.close()
PY
```

## 九、验证清单

```bash
ss -tlnp | grep -E '6080|5900|9222'
curl -s -o /dev/null -w '%{http_code}\n' http://127.0.0.1:6080/vnc.html
curl -s http://127.0.0.1:9222/json/version
```

应满足：

- `6080` 正在监听
- `5900` 正在监听本机 VNC
- `9222` 正在监听 CDP
- `vnc.html` 返回 `200`
- 用户能在浏览器看到远程 Chromium 画面

## 十、常见问题

### 1. noVNC 打开空白或连不上

检查进程：

```bash
ps -ef | grep -E 'Xvfb|chromium|x11vnc|websockify' | grep -v grep
ss -tlnp | grep -E '6080|5900|9222'
```

重新运行：

```bash
bash /root/start-remote-browser.sh
```

### 2. 外网打不开 6080

需要同时检查：

- 云服务器安全组是否放行 `6080/tcp`
- 系统防火墙是否放行 `6080/tcp`
- `websockify` 是否监听 `0.0.0.0:6080`

### 3. CDP WebSocket 403

Chromium 启动参数需要包含：

```bash
--remote-allow-origins='*'
```

Python websocket 连接时建议带 Origin：

```python
websocket.create_connection(ws_url, header=['Origin: http://127.0.0.1:9222'])
```

### 4. 浏览器画面卡住

可清理重启：

```bash
pkill -9 -f chromium || true
pkill -9 -f Xvfb || true
pkill -9 -f x11vnc || true
pkill -9 -f websockify || true
bash /root/start-remote-browser.sh
```

### 5. noVNC 报前端 JS 错误

如果使用 noVNC master 分支出现浏览器兼容问题，固定稳定版本：

```bash
cd /opt/noVNC
git fetch --tags
git checkout v1.6.0
bash /root/start-remote-browser.sh
```

## 十一、重要原则

- noVNC 里的 Chromium 是**用户可见浏览器**。
- Agent 内置的 headless browser 工具通常是另一个浏览器实例，不能代表 noVNC 画面。
- 用户需要看到操作时，应控制 `:99` 上的 Chromium，即通过 `127.0.0.1:9222` 的 CDP 控制。
