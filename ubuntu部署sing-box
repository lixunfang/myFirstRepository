### ubuntu 22.04 LTS安装步骤
### 注意：基于sing-box 1.14版本
```bash
# 1. 添加官方的 GPG 密钥和 APT 仓库
sudo mkdir -p /etc/apt/keyrings && \
sudo curl -fsSL https://sing-box.app/gpg.key -o /etc/apt/keyrings/sagernet.asc && \
sudo chmod a+r /etc/apt/keyrings/sagernet.asc && \
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/sagernet.asc] https://deb.sagernet.org/ * *" | \
sudo tee /etc/apt/sources.list.d/sagernet.list > /dev/null

# 2. 更新软件包列表并安装 sing-box
sudo apt-get update
sudo apt-get install sing-box

# 3. 启用 BBR（重启后生效）
echo "net.core.default_qdisc=fq" | sudo tee -a /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# 重载 systemd 守护进程（确保读取到最新的服务配置）
sudo systemctl daemon-reload

# 启动 sing-box 服务
sudo systemctl start sing-box

# 设置开机自启
sudo systemctl enable sing-box

# 查看服务运行状态（如果显示绿色的 active (running) 即代表成功）
sudo systemctl status sing-box


sudo sing-box check -c /etc/sing-box/config.json
sudo systemctl restart sing-box


sing-box generate reality-keypair
PrivateKey: ```
PublicKey: ```
```

# 配置/etc/sing-box/config.json
### config.json
```json
{
  "log": {
    "level": "info",
    "timestamp": true
  },
  "dns": {
    "servers": [
      {
        "type": "tls",
        "tag": "google",
        "server": "8.8.8.8"
      }
    ]
  },
  "inbounds": [
    {
      "type": "vless",
      "tag": "vless-in",
      "listen": "::",
      "listen_port": 443,
      "users": [
        {
          "uuid": "```",
          "flow": "xtls-rprx-vision"
        }
      ],
      "tls": {
        "enabled": true,
        "server_name": "www.apple.com", 
        "reality": {
          "enabled": true,
          "handshake": {
            "server": "www.apple.com",
            "server_port": 443
          },
          "private_key": "```",
          "short_id": [""]
        }
      }
    }
  ],
  "outbounds": [
    {
      "type": "direct",
      "tag": "direct"
    }
  ]
}

```


### clash-verge的配置文件
```yml
mixed-port: 7890
allow-lan: true
mode: rule
log-level: info

proxies:
    # 节点名称，可自定义
  - name: "Ubuntu-Reality"
    type: vless
    # 替换为你的 Ubuntu 服务器公网 IP
    server: ```
    # 对应 sing-box 配置中的 listen_port
    port: 443
    # 替换为你在 sing-box 中生成的 UUID
    uuid: ```
    network: tcp
    tls: true
    udp: true
    # 必须与服务端 config.json 中的 flow 保持一致
    flow: xtls-rprx-vision
    # 必须与 sing-box 配置中 tls.server_name 完全一致
    servername: www.apple.com
    # 模拟浏览器指纹，防止被探测
    client-fingerprint: chrome
    reality-opts:
      # 替换为 sing-box generate reality-keypair 输出的 public_key
      public-key: ```
      # 如果 sing-box 中 short_id 为空，这里也留空；否则填入对应的短ID
      short-id: ""

proxy-groups:
  - name: PROXY
    type: select
    proxies:
      - Ubuntu-Reality
      - DIRECT

rules:
  - MATCH,PROXY
```


```yaml
port: 7890
socks-port: 7891
allow-lan: true
mode: rule
log-level: info

proxies:
  - name: hy2-h2.example.com
    type: hysteria2
    server: h2.example.com
    port: 443
    password: ```
    sni: h2.example.com
    alpn: [h3]
    # up: "50 Mbps"     # 可选速率上限
    # down: "200 Mbps"
    skip-cert-verify: false
    # fast-open: true   # 可按需开启

proxy-groups:
  - name: 🚀 Proxy
    type: select
    proxies:
      - hy2-h2.example.com
      - DIRECT

rules:
  - GEOIP,CN,DIRECT
  - MATCH,🚀 Proxy
```

