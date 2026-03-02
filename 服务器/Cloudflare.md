```
# ================================
# Cloudflare Tunnel 手动部署步骤记录
# 每条命令可单独执行
# ================================


# --- 第一步：登录 Cloudflare 账号 ---
# 会打开浏览器进行授权，并在 ~/.cloudflared/ 生成 cert.pem
cloudflared tunnel login



# --- 第二步：创建一个 Tunnel ---
# my-tunnel 可以改成你自己的名字
# 会生成一个 UUID.json 凭据文件
cloudflared tunnel create <名字>
cloudflared tunnel create my-tunnel



# --- 第三步：创建配置文件 ---
# 打开配置文件进行编辑（如果没有会创建）
# 将 tunnel UUID 和 ingress 写进去
nano ~/.cloudflared/config.yml



# --- config.yml 示例内容（手动粘贴） ---
# tunnel: <自定义>
# credentials-file:  C:\Users\用户名\.cloudflared\<UUID>.json
#
# ingress:
#   - hostname: app.example.com
#     service: http://localhost:80
#
#   - hostname: api.example.com
#     service: http://localhost:8080
#
#   - service: http_status:404



# --- 第四步：绑定 DNS 到 Tunnel ---
# 这一步会自动在 Cloudflare 中创建 CNAME
cloudflared tunnel route dns my-tunnel app.example.com
cloudflared tunnel route dns my-tunnel api.example.com



# --- 第五步：启动 Tunnel（测试运行） ---
# 前台运行用于确认是否能访问
cloudflared tunnel run my-tunnel



# --- 第六步（可选）：安装为系统服务 ---
# 适合长期运行
cloudflared service install
```
