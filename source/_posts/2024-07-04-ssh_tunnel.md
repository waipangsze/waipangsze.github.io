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

# File explorer to read files

- A to B to C

To read files on host C from host A through an SSH tunnel via host B using a file explorer, follow these steps:

## 1. Set Up the SSH Tunnel

You need to create a tunnel from A to C through B. Use the following command on your terminal in host A:

```bash
ssh -L 2222:C.example.com:22 user_B@B.example.com
```

### Explanation:
- **2222**: Local port on host A.
- **C.example.com**: Hostname or IP address of host C.
- **user_B**: Your SSH username for host B.
- **B.example.com**: Hostname or IP address of host B.

## 2. Use a File Explorer to Access C

After setting up the SSH tunnel, you can use a file explorer to connect to C through the tunnel.

### For Linux (Using File Manager)

1. **Open your file manager** (e.g., `Nautilus`).
2. In the address bar, type:

   ```
   sftp://username_C@localhost:2222
   ```

3. Enter the password for `username_C` when prompted.

### For macOS (Using Finder)

1. **Open Finder**.
2. Click on **Go > Connect to Server** (or press `Command + K`).
3. Enter:

    ```
    sftp://username_C@localhost:2222
    ```

    Again, replace `username_C` with the correct username for host C.

1. Click **Connect** and enter the password for `username_C` when prompted.

### For Windows (Using File Explorer)

1. **Open File Explorer**.
2. In the address bar, type:

    ```
    sftp://username_C@localhost:2222
    ```

    Replace `username_C` with the actual username for host C.

3. When prompted, enter the password for `username_C`.


### Notes
- Make sure the SSH server on C is configured to allow SFTP connections.
- If you need to specify the username for host C, you can modify the connection string to:

  ```
  sftp://username_C@localhost:2222
  ```

- Ensure that your SSH tunnel is active while accessing files through the file explorer.

# From host A to host E via B, C, and D

- A to B to C to D to E

To set up SSH tunneling from host A to host E via B, C, and D, you will need to configure your `.ssh/config` file and then access files on E. Here's how to do it:

### 1. Setup `.ssh/config`

Open your SSH config file on host A:

```bash
vim ~/.ssh/config
```

Add the following configuration:

```plaintext
Host B
    HostName B.example.com
    User user_B

Host C
    HostName C.example.com
    User user_C
    ProxyJump B

Host D
    HostName D.example.com
    User user_D
    ProxyJump C

Host E
    HostName E.example.com
    User user_E
    ProxyJump D
```

### Explanation:
- **Host B**: Configuration for the first hop to B.
- **Host C**: Configuration for the second hop to C, using B as a proxy.
- **Host D**: Configuration for the third hop to D, using C as a proxy.
- **Host E**: Configuration for the final destination E, using D as a proxy.

### 2. Accessing Files on Host E from Host A

#### Step 1: Set Up the SSH Tunnel

From host A, create an SSH tunnel that forwards a local port to host E. You can use the following command:

```bash
ssh -L 2222:E.example.com:22 user_D@D.example.com -J user_C@C.example.com -J user_B@B.example.com
```

### Step 2: Use a File Explorer to Access Host E

#### For Linux (Using File Manager)

1. **Open your file manager** (e.g., Nautilus).
2. In the address bar, type:

   ```
   sftp://user_E@localhost:2222
   ```

3. Enter the password for `user_E` when prompted.

### Summary

1. The `.ssh/config` file is set up to facilitate SSH connections through multiple hops.
2. An SSH tunnel is created to allow access to host E.
3. You can access files on host E using the file explorer on host A by connecting to `sftp://localhost:2222` and providing the appropriate credentials.

# Dynamic Port Forwarding (SOCKS Proxy)

You can set up a `SOCKS proxy` to **route all your web traffic through an SSH server**:

```bash
ssh -D 1080 user@B.example.com
```

Then, configure your web browser or applications to use `localhost:1080` as a SOCKS proxy.

# Implement Lock Files

Setting up `crontab` on different nodes without conflicts involves a systematic approach to ensure that tasks are well-coordinated. You can set up `crontab` on different nodes effectively while avoiding conflicts.

**Lock Mechanism**: Use lock files to prevent multiple nodes from executing the same job simultaneously. For example, modify your cron job script to check for a lock file before execution:

   ```bash
   #!/bin/bash
   LOCKFILE="/tmp/myjob.lock"

   if [ -e $LOCKFILE ]; then
     echo "Job is already running."
     exit 1
   fi

   touch $LOCKFILE

   # Your job code here

   rm $LOCKFILE
   ```

# References
[What is a Jump Server?](https://www.ssh.com/academy/iam/jump-server)\
[SSH Tunneling (Port Forwarding) 詳解](https://johnliu55.tw/ssh-tunnel.html)