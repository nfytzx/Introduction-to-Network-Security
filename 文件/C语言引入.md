# 环境配置（Windows）

### 为了编译 C 代码，你需要一个 C 编译器。推荐使用 MinGW-w64。
**下载 MinGW-w64**

官方下载地址：https://sourceforge.net/projects/mingw-w64/files/

 选择适合你系统的版本(~~看你电脑是x86还是x64，不过没人电脑还是x86罢~~)，下载安装包。

安装与配置环境变量

  安装过程中，建议将安装路径（例如 C:\mingw64\bin）添加到系统 PATH 中。 如果你忘记勾选，可以手动添加：

通过注册表编辑

>按 Win + R，输入 regedit 打开注册表编辑器。
>
>定位到：`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment`
>
>找到 Path 变量，双击编辑，在末尾添加你的 MinGW bin 目录路径（用分号 ; 分隔）。

方法二：通过系统设置

    右键“此电脑” → 属性 → 高级系统设置 → 环境变量。

    在“系统变量”中找到 Path，点击编辑，新建一条，填入 MinGW 的 bin 路径。

### 验证安装

打开 命令提示符，输入：
```
gcc --version
```
如果显示版本信息，则配置成功

---

## 第一个 C 程序

环境配好了，写第一个程序吧。

1. 在 VSCode 里新建文件，命名 `hello.c`
2. 粘贴以下代码并保存（`Ctrl + S`）

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

3. 在终端（VSCode 里 `Ctrl + ~` 打开）编译并运行：

```powershell
gcc hello.c -o hello
.\hello.exe
```

看到 `Hello, World!` 输出

## 补充：常用编译命令与参数

| 命令 | 作用 |
|---|---|
| `gcc hello.c -o hello` | 编译并指定输出文件名 |
| `gcc hello.c -Wall` | 显示所有警告（写代码养成好习惯） |
| `gcc hello.c -g` | 生成调试信息（配合 gdb/调试器） |
| `gcc math.c -o math -lm` | 链接数学库（用到 sqrt 等函数时） |
| `gcc a.c b.c -o app` | 一次编译多个源文件 |
| `gcc -O2 hello.c -o hello` | 开启优化（比赛常用） |

> 报错时**永远先看第一条错误**，从上往下修，别盯着最后一行看。

## 补充：Linux / WSL 下的 gcc

大部分 CTF 题目的运行环境是 Linux，建议在 WSL或Linux虚拟机 里也装一份：

```bash
sudo apt update && sudo apt install -y gcc
gcc --version
```

编译运行流程与 Windows 相同，只是输出文件没有 `.exe` 后缀：

```bash
gcc hello.c -o hello
./hello
```

---

## 使用
打开~~微软大战代码~~VSCode，找到`拓展`搜索并下载`C/C++`
>什么，你没有~~微软大战代码~~？
>
>**还在等什么，快去下载啊!!!**

### 补充：Code Runner 一键运行

只装 C/C++ 扩展还得手动开终端编译，太麻烦了。再装一个 **Code Runner** 扩展：

1. 扩展商店搜索 `Code Runner` 安装
2. 打开你的 `.c` 文件，点右上角的**播放按钮**（或按 `Ctrl + Alt + N`）
3. 下方输出面板直接显示运行结果

想改默认编译参数（比如加 `-Wall`），在设置里搜 `code-runner.executorMap`，把 `"c": "cd $dir && gcc $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt"` 改成带 `-Wall` 的版本即可。

## 调试入门（Debug）

程序不报错但结果不对，就要调试了。

- **新手**：在可疑行前面加 `printf("这里执行了吗 %d\n", 变量名);` 打印观察
- **正经调试**：VSCode 左侧 `Ctrl + Shift + D` 打开调试面板 → 运行和调试 → 选择 `C/C++: gcc.exe 生成和调试活动文件`，然后 `F5` 启动，`F10` 单步执行，`F11` 进入函数，左侧窗口实时看变量值
- 命令行选手用 `gdb`：`gdb ./hello` 里 `break main`、`run`、`next`、`print 变量`

## 补充：常见报错

| 报错/现象 | 原因 | 解决 |
|---|---|---|
| `'gcc' 不是内部或外部命令` | PATH 没配好 | 重查环境变量，或重装时勾选添加 PATH |
| `fatal error: stdio.h: No such file or directory` | 编译器不完整 | 重新安装 MinGW-w64（别选到奇怪的"半截"版本） |
| `undefined reference to 'main'` | 没写 main 函数/名字打错 | 检查是否有 `int main()` |
| `undefined reference to 'sqrt'` | 数学库没链接 | 加 `-lm` |
| `Segmentation fault` | 指针/数组越界 | 检查数组下标、指针是否空/越界 |
| 中文乱码 | 源文件编码问题 | 文件右下角把编码改为 UTF-8 |

## 为什么学 C？——写给想打 CTF 的你

- **Pwn（二进制漏洞）**：题目就是用 C 写的，栈溢出、堆利用全是 C 的内存模型
- **Reverse（逆向）**：逆向的第一步是把汇编还原成"类 C 伪代码"来读
- **算法题**：C 运行快，很多题目卡时间只有 C/C++ 能过

所以大一上学期把 C 的基础打牢，指针、数组、结构体、内存布局这四个概念务必吃透：

```c
int a = 5;        // 变量：内存里的一块"盒子"
int *p = &a;      // 指针：盒子的"门牌号"
printf("%d\n", *p);  // 通过门牌号取盒子里的东西
```

指针就是地址，地址就是门牌号——想通了这一点，C 就通了，代码就太平了（）

## 补充
### 大一有一门课叫《C语言程序设计》你可以
1. 把《C语言程序设计》前六章（变量/分支/循环/数组/函数/指针）的习题在电脑上敲一遍
2. 实现几个小工具：猜数字、简易计算器、字符串反转
### 然后你就是C语言大手子了（
