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

[^1]: [Wikipedia: Netcat](https://en.wikipedia.org/wiki/Netcat)