# 代理配置说明

本项目支持多种代理类型，包括 SOCKS5、SOCKS4、HTTP 和 MTProto 代理。

## 支持的代理类型

### 1. SOCKS5 代理
最常用的代理类型，支持用户名密码认证。

### 2. SOCKS4 代理
较老的代理协议，不支持认证。

### 3. HTTP 代理
基于 HTTP CONNECT 方法的代理。

### 4. MTProto 代理
Telegram 官方的代理协议，需要特殊的 secret。

## 配置方式

### 方式一：环境变量配置

在 `docker-compose.yml` 中设置环境变量：

```yaml
environment:
  # 基本配置
  - TELEGRAM_DAEMON_API_ID=your_api_id
  - TELEGRAM_DAEMON_API_HASH=your_api_hash
  - TELEGRAM_DAEMON_CHANNEL=your_channel_id
  
  # 代理配置
  - TELEGRAM_DAEMON_PROXY_TYPE=socks5
  - TELEGRAM_DAEMON_PROXY_ADDR=127.0.0.1
  - TELEGRAM_DAEMON_PROXY_PORT=1080
  - TELEGRAM_DAEMON_PROXY_USERNAME=username  # 可选
  - TELEGRAM_DAEMON_PROXY_PASSWORD=password  # 可选
```

### 方式二：命令行参数

```bash
python telegram-download-daemon.py \
  --api-id your_api_id \
  --api-hash your_api_hash \
  --channel your_channel_id \
  --proxy-type socks5 \
  --proxy-addr 127.0.0.1 \
  --proxy-port 1080 \
  --proxy-username username \
  --proxy-password password
```

## 具体配置示例

### SOCKS5 代理（带认证）

```bash
# 环境变量
TELEGRAM_DAEMON_PROXY_TYPE=socks5
TELEGRAM_DAEMON_PROXY_ADDR=127.0.0.1
TELEGRAM_DAEMON_PROXY_PORT=1080
TELEGRAM_DAEMON_PROXY_USERNAME=myuser
TELEGRAM_DAEMON_PROXY_PASSWORD=mypass

# 或命令行
--proxy-type socks5 --proxy-addr 127.0.0.1 --proxy-port 1080 --proxy-username myuser --proxy-password mypass
```

### SOCKS5 代理（无认证）

```bash
# 环境变量
TELEGRAM_DAEMON_PROXY_TYPE=socks5
TELEGRAM_DAEMON_PROXY_ADDR=127.0.0.1
TELEGRAM_DAEMON_PROXY_PORT=1080

# 或命令行
--proxy-type socks5 --proxy-addr 127.0.0.1 --proxy-port 1080
```

### HTTP 代理

```bash
# 环境变量
TELEGRAM_DAEMON_PROXY_TYPE=http
TELEGRAM_DAEMON_PROXY_ADDR=proxy.example.com
TELEGRAM_DAEMON_PROXY_PORT=8080

# 或命令行
--proxy-type http --proxy-addr proxy.example.com --proxy-port 8080
```

### MTProto 代理

```bash
# 环境变量
TELEGRAM_DAEMON_PROXY_TYPE=mtproto
TELEGRAM_DAEMON_PROXY_ADDR=mtproto.example.com
TELEGRAM_DAEMON_PROXY_PORT=443
TELEGRAM_DAEMON_PROXY_SECRET=your_secret_here

# 或命令行
--proxy-type mtproto --proxy-addr mtproto.example.com --proxy-port 443 --proxy-secret your_secret_here
```

## 注意事项

1. **MTProto 代理**：需要提供正确的 secret，如果不提供会使用默认值。
2. **认证信息**：用户名和密码仅在 SOCKS5 代理中有效。
3. **端口号**：必须是有效的数字。
4. **测试连接**：建议先用简单的工具测试代理是否可用。
5. **Docker 网络**：在 Docker 环境中，注意代理地址的网络可达性。

## 故障排除

### 常见错误

1. **连接超时**：检查代理地址和端口是否正确。
2. **认证失败**：检查用户名和密码是否正确。
3. **协议不支持**：确认代理服务器支持所选的协议类型。

### 调试方法

1. 查看程序启动时的代理配置输出。
2. 使用其他工具（如 curl）测试代理连接。
3. 检查防火墙和网络配置。

## Docker Compose 完整示例

```yaml
version: '3.8'
services:
  telegram-download-daemon:
    image: alfem/telegram-download-daemon:latest
    container_name: telegram-download-daemon
    restart: unless-stopped
    environment:
      TELEGRAM_DAEMON_API_ID: "your_api_id"
      TELEGRAM_DAEMON_API_HASH: "your_api_hash"
      TELEGRAM_DAEMON_CHANNEL: "your_channel_id"
      TELEGRAM_DAEMON_DEST: "/downloads"
      TELEGRAM_DAEMON_SESSION_PATH: "/session"
      # 代理配置
      TELEGRAM_DAEMON_PROXY_TYPE: "socks5"
      TELEGRAM_DAEMON_PROXY_ADDR: "127.0.0.1"
      TELEGRAM_DAEMON_PROXY_PORT: "1080"
      TELEGRAM_DAEMON_PROXY_USERNAME: "username"
      TELEGRAM_DAEMON_PROXY_PASSWORD: "password"
    volumes:
      - downloads:/downloads
      - session:/session

volumes:
  downloads:
  session:
```