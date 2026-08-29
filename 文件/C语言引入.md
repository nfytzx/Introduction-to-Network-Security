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

## 使用
打开~~微软大战代码~~VSCode，找到`拓展`搜索并下载`C/C++`
>什么，你没有~~微软大战代码~~？
>
>**还在等什么，快去下载啊!!!**

