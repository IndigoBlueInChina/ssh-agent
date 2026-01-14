# Clash Verge Rev + SSH 隧道配置指南

## 第一步：建立 SSH SOCKS5 隧道

### 方法 A：使用 Windows 命令行（推荐）

1. 打开 PowerShell 或 CMD
2. 运行以下命令：
   ```bash
   ssh -D 7890 -N -C user@你的香港服务器IP
   ```
   - `-D 7890`：在本地 7890 端口创建 SOCKS5 代理
   - `-N`：不执行远程命令，只做端口转发
   - `-C`：启用压缩，加快传输
   - `user`：替换为你的服务器用户名

3. 输入密码后，窗口会保持打开状态（这是正常的，不要关闭）

### 方法 B：使用 PuTTY（图形界面）

1. 下载 PuTTY：https://www.putty.org/
2. 打开 PuTTY，配置如下：
   - **Session**：
     - Host Name: `你的香港服务器IP`
     - Port: `22`
   - **Connection → SSH → Tunnels**：
     - Source port: `7890`
     - 选择 `Dynamic`
     - 点击 `Add`
   - 回到 **Session**，保存配置（方便下次使用）
3. 点击 `Open` 连接

---

## 第二步：安装 Clash Verge Rev

1. 访问 GitHub 下载页面：
   https://github.com/clash-verge-rev/clash-verge-rev/releases

2. 下载 Windows 版本：
   - 64位系统下载：`Clash.Verge_x.x.x_x64-setup.exe`
   
3. 安装并启动 Clash Verge Rev

---

## 第三步：配置 Clash Verge Rev

### 3.1 创建配置文件

1. 在本地创建一个文件，命名为 `config.yaml`
2. 复制以下内容到文件中：

```yaml
# 端口配置
port: 7891
socks-port: 7892
mixed-port: 7893
allow-lan: false
mode: rule
log-level: info

# 代理服务器配置（指向你的 SSH 隧道）
proxies:
  - name: "香港云主机"
    type: socks5
    server: 127.0.0.1
    port: 7890

# 代理组
proxy-groups:
  - name: "代理选择"
    type: select
    proxies:
      - 香港云主机
      - DIRECT

# 分流规则
rules:
  # GitHub
  - DOMAIN-SUFFIX,github.com,代理选择
  - DOMAIN-SUFFIX,githubusercontent.com,代理选择
  - DOMAIN-SUFFIX,githubassets.com,代理选择
  - DOMAIN-SUFFIX,github.io,代理选择
  
  # Docker
  - DOMAIN-SUFFIX,docker.com,代理选择
  - DOMAIN-SUFFIX,docker.io,代理选择
  - DOMAIN-SUFFIX,dockerhub.com,代理选择
  - DOMAIN-SUFFIX,gcr.io,代理选择
  - DOMAIN-SUFFIX,ghcr.io,代理选择
  - DOMAIN-SUFFIX,registry.k8s.io,代理选择
  
  # NPM / Node.js
  - DOMAIN-SUFFIX,npmjs.org,代理选择
  - DOMAIN-SUFFIX,npmjs.com,代理选择
  - DOMAIN-SUFFIX,nodejs.org,代理选择
  
  # Python / PyPI
  - DOMAIN-SUFFIX,python.org,代理选择
  - DOMAIN-SUFFIX,pypi.org,代理选择
  - DOMAIN-SUFFIX,pythonhosted.org,代理选择
  - DOMAIN-SUFFIX,anaconda.org,代理选择
  - DOMAIN-SUFFIX,conda.io,代理选择
  
  # Google 全家桶
  - DOMAIN-SUFFIX,google.com,代理选择
  - DOMAIN-SUFFIX,google.com.hk,代理选择
  - DOMAIN-SUFFIX,googleapis.com,代理选择
  - DOMAIN-SUFFIX,googlesource.com,代理选择
  - DOMAIN-SUFFIX,googleusercontent.com,代理选择
  - DOMAIN-SUFFIX,gstatic.com,代理选择
  - DOMAIN-SUFFIX,ggpht.com,代理选择
  - DOMAIN-SUFFIX,youtube.com,代理选择
  - DOMAIN-SUFFIX,ytimg.com,代理选择
  - DOMAIN-SUFFIX,gmail.com,代理选择
  - DOMAIN-KEYWORD,google,代理选择
  
  # OpenAI / ChatGPT
  - DOMAIN-SUFFIX,openai.com,代理选择
  - DOMAIN-SUFFIX,chatgpt.com,代理选择
  - DOMAIN-SUFFIX,oaistatic.com,代理选择
  - DOMAIN-SUFFIX,oaiusercontent.com,代理选择
  
  # Vercel v0
  - DOMAIN-SUFFIX,v0.dev,代理选择
  - DOMAIN-SUFFIX,vercel.com,代理选择
  - DOMAIN-SUFFIX,vercel.app,代理选择
  
  # Hugging Face
  - DOMAIN-SUFFIX,huggingface.co,代理选择
  - DOMAIN-SUFFIX,hf.co,代理选择
  - DOMAIN-SUFFIX,huggingface.tech,代理选择
  
  # LinkedIn
  - DOMAIN-SUFFIX,linkedin.com,代理选择
  - DOMAIN-SUFFIX,licdn.com,代理选择
  - DOMAIN-SUFFIX,golang.org,代理选择
  - DOMAIN-SUFFIX,go.dev,代理选择
  - DOMAIN-SUFFIX,rust-lang.org,代理选择
  - DOMAIN-SUFFIX,crates.io,代理选择
  
  # 默认直连
  - MATCH,DIRECT
```

### 3.2 导入配置到 Clash Verge Rev

1. 打开 Clash Verge Rev
2. 点击左侧菜单 **「订阅」** 或 **「Profiles」**
3. 点击 **「新建」** → **「Local」**（本地文件）
4. 选择你刚才创建的 `config.yaml` 文件
5. 点击导入的配置文件，使其生效（会有高亮显示）

### 3.3 启用系统代理

1. 点击左侧菜单 **「设置」**
2. 开启 **「系统代理」**（System Proxy）
3. 可选：开启 **「开机自启」**

---

## 第四步：验证配置

### 测试 GitHub 访问
```bash
curl -I https://github.com
```

### 测试 Docker 拉取
```bash
docker pull hello-world
```

### 在浏览器中
直接访问 https://github.com，应该能快速打开

---

## 使用流程（每次使用）

1. **先启动 SSH 隧道**（保持窗口打开）
   ```bash
   ssh -D 7890 -N -C user@你的香港服务器IP
   ```

2. **启动 Clash Verge Rev**，确保系统代理已开启

3. 开始使用，GitHub/Docker 等会自动走代理

---

## 常见问题

### Q: SSH 连接断开怎么办？
A: 可以使用 autossh 保持连接，或者在 SSH 配置中添加心跳：
```bash
ssh -D 7890 -N -C -o ServerAliveInterval=60 user@服务器IP
```

### Q: 如何添加更多需要代理的网站？
A: 编辑 config.yaml，在 rules 部分添加：
```yaml
- DOMAIN-SUFFIX,example.com,代理选择
```

### Q: 如何让 Git 命令行也走代理？
A: Git 会自动使用系统代理，或者手动配置：
```bash
git config --global http.proxy socks5://127.0.0.1:7893
git config --global https.proxy socks5://127.0.0.1:7893
```

### Q: 如何取消 Git 代理？
```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```
