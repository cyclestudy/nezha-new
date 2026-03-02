# 哪吒v1 Docker CloudFlare Tunnel版

无需公网IP，全程都在CF下，项目优势：
1. 不暴露公网IP，防止被攻击
2. 单栈转双栈，IPv4/IPv6 都能用，纯IPv6也方便挂探针
3. 除境内网络外，走CF基本都优化
4. 开箱即用，迁移备份方便

## Dashboard安装

1. 安装好 Docker
2. 安装 CloudFlare Tunnel（cloudflared）并获取 Token
   https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/
3. CloudFlare 开启 gRPC 流量代理
   https://developers.cloudflare.com/network/grpc-connections/
4. 启动服务
    ```shell
    git clone https://github.com/cyclestudy/nezha-new.git
    cd nezha-new
    cp .env.example .env  # 按需修改配置
    docker compose up -d
    ```
5. 服务端映射到 CF
   CloudFlare Tunnel 管理页 https://one.dash.cloudflare.com/ 加 1 个 Public hostname 指向 `http://localhost:8080`
6. （可选）探针IP加到CF拦截白名单
   由于探针上报日志频繁，且VPS的IP质量参差不齐，可能会被CF误拦截导致无法正常工作。可以添加白名单。
   操作路径：安全性 - WAF - 工具
   或者参考文档 https://developers.cloudflare.com/waf/tools/ip-access-rules/
7. （可选）配置自动更新
   取消 compose.yml 中 watchtower 相关注释，修改 .env 里的 `TELEGRAM_BOT_TOKEN`、`TELEGRAM_CHAT_ID` 以实现更新通知。
8. （可选）自动备份到 GitHub
   取消 compose.yml 中 backup 相关注释，修改 .env 里的 `GITHUB_USER`、`GITHUB_TOKEN`、`GIT_REMOTE_URL`、`CRON_SCHEDULE` 以实现自动备份。

## Dashboard配置

在 `/dashboard/settings` 里设置：
1. **Agent对接地址**：上面的 Public hostname:443，勾选 `Agent 使用 TLS 连接`
2. **真实IP请求头**：填 `nz-realip` 或 `CF-Connecting-IP`

## Dashboard更新

进入项目目录下（compose.yml 同级）：
```shell
docker compose pull
docker compose up -d
```

## Agent安装

Dashboard 右上角复制安装命令，注意手动修改参数中的 8008 端口为 443（如果你没有修改 Agent 对接地址），TLS 改为 True（如果你没有将配置文件中的 TLS 设置为 True）。

## 其他

- 后台地址：`/dashboard`
- 默认密码：`admin / admin`
