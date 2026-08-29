# Python 基础速览  [^1]
本指南旨在快速梳理 Python 最基础的知识点，帮助你在 CTF 解题或者其他使用时候时更加得心应手。

---

**安装 Python**

官方下载页面（Windows）：https://www.python.org/downloads/windows/

请选择`Stable Releases` 版本，安装时务必勾选 **`Add Python to PATH`**

## 基础语法
### 一切的开始
```
print("Hello World")
```
### 基本数据类型与运算
**赋值与类型**

赋值，右值赋到左值
```
a = 1        # int
b = 1.0      # float
c = "abc"    # string
d = False    # boolean
```
**算术运算**
```
print(1 + 1)      # 2
print(1 + 1.0)    # 2.0
# print(1 + "1")  # TypeError
# print("1" + 1)  # TypeError
print("1" + "1")  # "11"

print(2 + 1)      # 3
print(2 - 1)      # 1
print(2 * 1)      # 2
print(2 / 1)      # 2.0
print(2 % 1)      # 0
print(2 ** 1)     # 2
print(3 // 2)     # 1（整除）
```
**比较运算**
```
print(1 > 2)   # False
print(1 < 2)   # True
print(1 == 2)  # False
print(1 >= 2)  # False
```
**复合赋值**
```
a = 1
a += 1
a -= 1
a *= 2
a /= 2
```
位运算符不讲

逻辑运算符 `AND` `OR` `NOT`

成员运算符 `in`, `not in`

---

**字符串格式化**
```
name = "Starwalking"
age = 19

print(f"My name is {name}, I'm {age} years old")          # f-string
print("My name is {}, I'm {} years old".format(name, age)) # format()
```
---

## 流程控制
**if-elif-else**
```
if (1 == 1):
    pass   # 执行这里
elif (2 == 1):
    pass   # 不会进入
else:
    pass   # 也不会进入
```
**for 循环**
```
for i in range(10):
    print(i)   # 输出 0 到 9
```
**while 循环**
```
while(1):
    pass   # 无限循环
 # 此处代码不会被执行（因为上面是死循环）
```
----

##  复合数据类型及常用方法
**List（列表）**
```
a = [1, 2, 3, 4, 5]
a.append(6)      # 尾部添加 6
a.pop()          # 删除最后一个元素（6）
print(a[2])      # 输出第三个元素（索引从 0 开始）
```
**Tuple（元组）—不可变**
```
a = (1, 2, 3)
# a.append(4)      # 错误！元组不可修改
b = (4,)
c = a + b          # 可以拼接两个元组
print(a[1])        # 输出第二个元素（索引从 0 开始）
```
**Set（集合）-用的不是很多**
```
a = {1, 2, 3, 4}                 # 直接定义
b = set([1, 2, 3, 4])            # 从列表创建
c = set()                        # 创建空集合（注意不能使用 {}）

print(1 in a)   # True
print(6 in a)   # False

d = a - b       # 差集
d = a | b       # 并集
d = a & b       # 交集
d = a ^ b       # 对称差
```
**Dict（字典）—这个在网络请求处理json的时候用的比较多**
```
a = {"key": "info"}
print(a["key"])   # 输出 "info"
```
----

## 函数
```
def add(a: int, b: int) -> int:
    return a + b
```
类、对象、Pickle这些会在Web题当中出现，此处不讲。

----

## bytes 与编码（CTF 最高频知识点）

**重要**：Python 3 里字符串 `str` 和字节 `bytes` 是两种东西。网络收发的、文件里存的、题目给的 flag，大多是 **bytes**。

```python
s = "flag{hello}"          # str（文本）
b = b"flag{hello}"         # bytes（字节），前面加 b
print(s.encode())          # str -> bytes：b'flag{hello}'
print(b.decode())          # bytes -> str：'flag{hello}'

# 十六进制（Misc 里满大街都是）
hex_str = "666c61677b68656c6c6f7d"
print(bytes.fromhex(hex_str))          # b'flag{hello}'
print(b"flag".hex())                  # '666c6167'

# Base64
import base64
print(base64.b64encode(b"flag"))       # b'ZmxhZw=='
print(base64.b64decode("ZmxhZw=="))   # b'flag'
```

> 报 `TypeError: can't concat str to bytes` 时，就是 str/bytes 混用了——统一用 `encode()` 或 `decode()` 转一下。

## 文件读写

解 Misc 题经常要处理文件：

```python
# 读文本
with open("data.txt", "r", encoding="utf-8") as f:
    content = f.read()          # 全读成字符串

# 读二进制（图片、压缩包、加密文件）
with open("secret.bin", "rb") as f:
    data = f.read()             # 全读成 bytes

# 写文件
with open("out.txt", "w") as f:
    f.write(content)
```

`with open(...) as f:` 会自动关闭文件，永远用这个写法。

## 异或 XOR（密码学/逆向常客）

XOR 的特性：`a ^ b = c`，则 `a ^ c = b`。加密和解密是同一个操作，所以 CTF 里最常见的解密脚本就是循环异或：

```python
data = bytes.fromhex("1b1c1b1a1e")      # 假设这是密文
key = 0x66                              # 单字节密钥

flag = bytes([c ^ key for c in data])   # 逐字节异或
print(flag)

# 多字节密钥循环异或
def xor_with_key(data: bytes, key: bytes):
    return bytes([d ^ key[i % len(key)] for i, d in enumerate(data)])
```

> 看到"异或加密""XOR"字样，先试单字节爆破（key 从 0 到 255 全试一遍，看哪个输出像 flag）。

## 正则表达式（提取 flag 神器）

解 Web/Misc 题，从一堆输出里抓 flag 全靠它：

```python
import re

text = "random...flag{Th1s_1s_fl4g}...random"
m = re.search(r"flag\{[^}]+\}", text)   # 匹配 flag{...}
print(m.group(0))                       # flag{Th1s_1s_fl4g}

# 找所有数字
print(re.findall(r"\d+", "a1b22c333"))  # ['1', '22', '333']
```

## 列表推导式与常用内置函数

```python
# 列表推导式：一行生成列表（比 for 循环简洁）
squares = [x * x for x in range(5)]     # [0, 1, 4, 9, 16]
evens   = [x for x in range(10) if x % 2 == 0]

# 常用内置函数
for i, v in enumerate(["a", "b", "c"]):  # 同时拿下标和值
    print(i, v)

names = ["bob", "alice", "cindy"]
print(sorted(names))                     # 排序
print("".join(reversed("abc")))          # 反转字符串

# 字符 <-> ASCII（密码题常用）
print(ord("A"))                          # 65
print(chr(97))                           # 'a'
```

### 依赖管理

Python 拥有丰富的第三方库。
导入库
```
import requests
import numpy as pd   # 玩笑：把 numpy 重命名为 pd（实际 pandas 通常为 pd）
```

安装依赖
```
pip install requests
pip install -r requirements.txt
```
----

## 补充：虚拟环境 venv 与 pip 换源

装多了库容易互相打架（版本冲突），用虚拟环境隔离：

```powershell
python -m venv myenv      # 创建虚拟环境
.\myenv\Scripts\activate  # 激活（Windows）
# Linux/WSL: source myenv/bin/activate
```

国内下载慢，换清华源：

```powershell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

# requests & flask

### requests —— 发送 HTTP 请求
```
import requests

# GET 请求
a = requests.get("http://github.com/")
print(a.ok)   # 是否成功

# POST 请求（携带 JSON 数据）
data = {"something": "somedata"}
b = requests.post("http://github.com/", json=data)
print(b.text)   # 响应内容
```
~~关于 HTTP 请求类型的更多内容，请参考 Web 方向入门指南~~

**flask —— 轻量级后端框架**
```
from flask import Flask
app = Flask(__name__)

@app.get("/")
def index():
    return "Hello"

if __name__ == "__main__":
    app.run()
```

> 报错永远从**最下面一行**（异常类型和位置）读起，`Traceback` 的最后一帧才是出错点。

## 补充：CTF 解题脚本模板

把下面这份存成 `solve.py`，以后解脚本题直接改：

```python
import requests, re, base64

# 1. 拿题目数据（Web 题）
resp = requests.get("http://target/flag.txt")
data = resp.text

# 2. 处理（解码/异或/正则）
flag = re.search(r"flag\{[^}]+\}", data).group(0)

# 3. 提交或打印
print(flag)
```

进阶玩法：`requests` + 循环 = 自动提交/爆破；`pwntools` + `nc` = 打 Pwn 题；`z3` = 解约束方程。

[^1]: 本文来自moectf  `3-2 Python环境配置与基础语法`（原版基础上补充了 bytes/编码、文件读写、异或、正则、调试与实战模板）
