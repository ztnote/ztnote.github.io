---
# 基本内容
title: VSCode SSH Config # 文章标题
description: VSCode SSH Config 配置记录 # 文章描述
slug: 003-vscode-ssh-config # 文章slug
date: 2025-11-30 18:30:00+0800 # 发布时间 创建时间
lastmod: 2025-11-30 18:30:00+0800 # 修改时间 会在文章末尾显示
# image: cover.png # 文章封面
# 分类与标签
categories:
    - daily
tags:
    - site
    - vscode
# 其他
draft: true
weight: 1  
# 全局配置覆盖
math: true
readingTime: true
toc: true
comment: true    
---

记录一下关于Vscode的ssh配置，当然也可能是ssh的通用配置。

## SSH Config 文件夹
SSH Config 文件是一个客户端配置文件，用于存储 SSH 客户端的配置信息。
通常存储在 `~/.ssh/` 文件夹（或 `/etc/ssh/ssh_config` 系统全局），其中包括两部分文件：
- `config` 文件
- `known_hosts` 文件

*重要*：  
为了安全起见，SSH 对文件权限非常严格。  
你需要确保 ~/.ssh 目录的权限是 700 (drwx------)，而 ~/.ssh/config 文件的权限是 600 (-rw-------)。

windows下面：
对于 %USERPROFILE%\.ssh 目录：  
应该只有当前用户有完全控制权限  
其他所有用户/组都应该没有权限  

对于 %USERPROFILE%\.ssh\config 文件：  
应该只有当前用户有读取权限  
其他所有用户/组都应该没有权限  

PS:这个是ai说的，但是我在vscode遇到过权限问题，所以还是要注意一下。


## config 文件
一个简单的config包括下面几个部分：

```
Host [别名或模式]
    [选项1] [值1]
    [选项2] [值2]


Host <hostname>
    HostName <hostname>
    User <username>
    Port <port>
    IdentityFile <path to private key>
```

| 选项 | 说明 | 示例 |
| :--- | :--- | :--- |
| `HostName` | 真实的主机名或 IP 地址。 | `HostName 192.168.1.100` |
| `User` | 登录用户名。 | `User alice` |
| `Port` | SSH 服务端口号（默认是 22）。 | `Port 2222` |
| `IdentityFile` | 指定使用的私钥文件路径。 | `IdentityFile ~/.ssh/id_ed25519_work` |
| `IdentitiesOnly` | 强制只使用配置文件中指定的密钥。 | `IdentitiesOnly yes` |
| `ProxyJump` | 通过跳板机连接目标主机（非常常用）。 | `ProxyJump jumpuser@jump.example.com:22` |
| `ForwardAgent` | 是否允许 SSH 代理转发。 | `ForwardAgent yes` |
| `LocalForward` | 设置本地端口转发。 | `LocalForward 8080 localhost:80` |
| `ServerAliveInterval` | 设置空闲超时间隔，防止连接断开。 | `ServerAliveInterval 60` |
| `StrictHostKeyChecking` | 是否自动添加新主机密钥（首次连接时有用）。 | `StrictHostKeyChecking no` |

### Host* block
可选，放在config的最后，相当于设置全局的选项。
```
Host *
    AddKeysToAgent yes
    UseKeychain yes
    ServerAliveInterval 60
    ServerAliveCountMax 3
    Compression yes
    ConnectTimeout 10

```
## github 配置示例
由于网络问题，github 的连接经常会断开，所以需要使用代理。

```
# Host 可以理解为别名
# HostName 真实的地址
# User 用户名
# Port 端口号
# IdentityFile 私钥
# IdentitiesOnly 只使用配置文件中指定的密钥
# ProxyCommand 代理命令

Host github.com-ztnote
  HostName github.com
  Port 22
  User git
  IdentityFile ~/.ssh/id_rsa_github
  IdentitiesOnly yes # 只使用配置文件中指定的密钥
  ProxyCommand "~\PortableGit\mingw64\bin\connect.exe" -S 127.0.0.1:7890 %h %p
# 这里使用了connect进行代理
```

### github测试
```
ssh -T git@github.com-ztnote
```
此处的`github.com-ztnote`其实就是个别名。

### 小插曲
IdentitiesOnly yes  
在一次尝试不同账户github的连接测试时，发现不加这个选项，会登录上错误的账户。  
SSH 默认会尝试所有可用的密钥（ssh-agent + 配置文件），使用第一个成功的。  

```
默认行为（无 IdentitiesOnly yes）：

1. 检查 ssh-agent 中缓存的密钥
   └── 尝试密钥 A (缓存中的某个密钥)
   └── 尝试密钥 B (缓存中的另一个密钥)
   
2. 检查配置文件中的 IdentityFile
   └── 尝试 ~/.ssh/id_rsa_github_1 (你真正想要的密钥)
   
3. 使用第一个被 GitHub 接受的密钥建立连接
```

```
# 运行命令
$ ssh -T git@github.com-1

# SSH 内部尝试顺序：
1. 尝试 id_rsa_default (ssh-agent) → GitHub 接受！ → 使用 user1 身份
2. 不再尝试 id_rsa_github_1

# 运行另一个命令  
$ ssh -T git@github.com-2

# SSH 内部尝试顺序：
1. 尝试 id_rsa_default (ssh-agent) → GitHub 接受！ → 使用 user1 身份
2. 不再尝试 id_rsa_github_2

# 结果：两个命令都使用了同一个身份！
```

问题所在：

如果 ssh-agent 中的密钥 A 先被尝试且被 GitHub 接受
SSH 就直接使用密钥 A，不再尝试你配置的密钥
结果：错误的密钥被使用！

起因在于：

我基本上都是使用VSCODE，所以对于原始的ssh命令并不熟悉。毕竟是`小白`。

## ssh 系列命令
下面的大部分命令来自deepseek，ssh-keygen 的第二部分是自己总结的。

### ssh
```
# 最基本连接
ssh username@hostname

# 使用特定端口
ssh -p 2222 username@hostname

# 使用特定密钥
ssh -i ~/.ssh/my_key username@hostname

# 详细输出（调试用）
ssh -v username@hostname
ssh -vvv username@hostname  # 更详细

# 执行远程命令
ssh username@hostname "ls -la"

# 本地端口转发（本地→远程）
ssh -L 8080:localhost:80 username@hostname

# 远程端口转发（远程→本地）
ssh -R 9090:localhost:3000 username@hostname

# 动态 SOCKS 代理
ssh -D 1080 username@hostname

# 多端口转发
ssh -L 8080:localhost:80 -L 3000:localhost:3000 username@hostname

# 连接测试（不执行命令）
ssh -T git@github.com

# 强制使用 IPv4/IPv6
ssh -4 username@hostname  # IPv4
ssh -6 username@hostname  # IPv6

# 压缩数据传输
ssh -C username@hostname

# 控制连接超时
ssh -o ConnectTimeout=10 username@hostname  # 10秒超时
ssh -o ServerAliveInterval=60 username@hostname  # 60秒保活

# 跳过主机密钥检查（谨慎使用）
ssh -o StrictHostKeyChecking=no username@hostname


# 测试配置文件语法
ssh -G hostname  # 显示实际使用的配置

# 使用特定配置文件
ssh -F ~/.ssh/other_config username@hostname

# 查看详细配置过程
ssh -v -G hostname


```

### ssh-keygen
```
# 生成新密钥对
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-keygen -t ed25519 -C "your_email@example.com"  # 更安全

# 指定密钥文件名
ssh-keygen -f ~/.ssh/github_work -t ed25519 -C "work@company.com"

# 查看密钥指纹
ssh-keygen -lf ~/.ssh/id_rsa

# 更改密钥密码
ssh-keygen -p -f ~/.ssh/id_rsa

# 转换密钥格式
ssh-keygen -p -f ~/.ssh/id_rsa -m PEM  # 转成 PEM 格式

```

### ssh-keygen 自我总结版

```
# 创建秘钥
# -t type 加密算法
# -b bits 秘钥长度
# -f file 文件名 需要指定目录
# -C comment 注释
# -N NewPassphrase 秘钥的密码

# 创建秘钥
ssh-keygen -t rsa -b 4096 -f mykey -C "user@gmail.com" -N "password"

# 不添加 -N 以避免在终端留下记录
ssh-keygen -t rsa -b 4096 -f mykey -C "user@gmail.com"

# 给私钥添加密码 如果输入空密码则移除密码
# -p passphrase 修改密码
ssh-keygen -p -f mykey

# 验证私钥的密码是否正确
# -y yank 提取公钥
ssh-keygen -y -f mykey
```

### ssh-add
```
# 启动 ssh-agent
eval "$(ssh-agent -s)"

# 密钥管理
ssh-add ~/.ssh/id_rsa              # 添加密钥
ssh-add -l                         # 列出所有密钥
ssh-add -L                         # 列出公钥
ssh-add -d ~/.ssh/id_rsa           # 删除特定密钥
ssh-add -D                         # 删除所有密钥

# 设置密钥缓存时间
ssh-add -t 3600 ~/.ssh/id_rsa      # 缓存1小时

# 锁定/解锁代理
ssh-add -x                         # 锁定（需要密码）
ssh-add -X                         # 解锁

```

### scp rsync sftp
```
# SCP - 安全复制
scp file.txt username@hostname:/path/to/destination
scp username@hostname:/path/to/file.txt ./local/path
scp -r directory/ username@hostname:/path/  # 递归复制目录
scp -P 2222 file.txt username@hostname:/path/  # 指定端口

# RSYNC over SSH
rsync -avz -e ssh local_dir/ username@hostname:remote_dir/
rsync -avz -e "ssh -p 2222" local_dir/ username@hostname:remote_dir/

# SFTP - 交互式文件传输
sftp username@hostname
sftp -P 2222 username@hostname
```

## 总结
config大法好啊。

## 修改记录
2025-11-30 19:30:00 第一版