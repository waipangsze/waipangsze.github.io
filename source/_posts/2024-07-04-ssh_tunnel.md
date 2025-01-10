---
layout: post
title: ssh tunnel
categories: [Linux]
tags: [ssh]
author: wpsze
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Introduction to SSH Tunnels

Secure Shell, or SSH, is used to create a secure channel between a local and remote computer. While SSH is commonly used for secure terminal access and file transfers, it can also be used to create a secure tunnel between computers for forwarding other network connections that are not normally encrypted. SSH tunnels are also useful for allowing outside access to internal network resources.

An **SSH tunneling (SSH Port Forwarding)** establishes a connection between your local machine and the remote machine via a TCP port. When you configure your local application to use an SSH tunnel you tell it to connect to your local machine at a specified port, rather than the remote machine. The tunnel then carries your traffic securely to the remote machine.

SSH Port Forwarding：
- Local Port Forwarding
- Remote Port Forwarding
- Dynamic Port Forwarding

For both local and remote port forwarding, there are three roles:

- Client
    - Any machine from which you can type ssh to start Port Forwarding
- SSH server
    - Clients can use SSH connections for machine learning
- target server
    - Some machines you want to establish a connection to, usually to open services on this machine to the outside world

**Note that** both the client and the SSH server can themselves be the target server. It does not mean that only three machines can be used for port forwarding!

![](https://img1.xenby.com/269/89b59f7b.png){width=500}

## 常用的 SSH 指令參數

```sh
sh -N -L 0.0.0.0:local-port:target-server:target-port username@proxy-server
```

- `-L`
  - **建立 Local forwarding**
- `-N`
  - **不要執行任何遠端指令**。沒有加這個參數時，建立 Port Forwarding 的同時也會開啟 Remote Shell，讓你可以對 SSH Server 下指令，而這個參數可以讓 Remote Shell 不要打開。
- `-f`
  - **讓 ssh 指令在背景執行**，讓你可以繼續用 Shell 做事情。通常會搭上面的 -N 使用。
- `lsof -i -n | egrep '\<ssh\>'`
  - 列出目前有的 SSH tunnel


## Local Port Forwarding

Local forwarding is used to forward a port from the client machine to the server machine. 

Quite a few organizations for all incoming SSH access through a single **jump server**. The server may be a standard Linux/Unix box, usually with some extra hardening, intrusion detection, and/or logging, or it may be a commercial jump server solution.


To create a local port forward add the **-L** parameter to the ssh command line.

```sh
$ ssh -L 9090:localhost:8080 user@server-ip
```

```sh
$ ssh -L 9090:192.168.1.101:8080 user@server-ip
```
The 192.168.1.101 (like Target server) here is relative to user@server-ip, so it is the IP address of the server behind the firewall or VPN. Then, you can visit 192.168.1.101:9090

### Client -- SSH server -- Target server

- Client
  - 你的電腦
- SSH Server
  - 防火牆後你的機器
  - SSH Destination： user@server-ip
- Target Server
  - 防火牆後的伺服器
  - 192.168.1.101:8080

```sh
ssh -L 9090:192.168.1.101:8080 user@server-ip
```

### bind_address

如果你沒有給 bind_address ，預設會 Bind 在 localhost 上。如果你想把 Port 9090 開放給所有人用：

```sh
$ ssh -L 0.0.0.0:9090:localhost:8080 user@server-ip
```



## Remote Port Forwarding

```sh
ssh -R 9090:localhost:8080 user@server-ip
```

# References
[What is a Jump Server?](https://www.ssh.com/academy/iam/jump-server)\
[SSH Tunneling (Port Forwarding) 詳解](https://johnliu55.tw/ssh-tunnel.html)