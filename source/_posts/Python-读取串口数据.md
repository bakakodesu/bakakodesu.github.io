---
title: Python 读取串口数据
date: 2023-08-11 22:21:21
categories:
- [Code, Python]
tags:
- Python
math: false
---

### serial 读取串口数据

初始化ser

```python
import serial

ser = serial.Serial('com1', 9600, timeout=1)
```

初始化的参数

```python
ser = serial.Serial(
port=None,              
# number of device, numbering starts at
# zero. if everything fails, the user
# can specify a device string, note
# that this isn't portable anymore
# if no port is specified an unconfigured
# an closed serial port object is created

baudrate=9600,          # baud rate
bytesize=EIGHTBITS,     # number of databits
parity=PARITY_NONE,     # enable parity checking
stopbits=STOPBITS_ONE,  # number of stopbits
timeout=None,           # set a timeout value, None for waiting forever
xonxoff=0,              # enable software flow control
rtscts=0,               # enable RTS/CTS flow control
interCharTimeout=None   # Inter-character timeout, None to disable
)
```

ser的属性

```yaml
name：设备名字
port：读或者写端口
baudrate：波特率
bytesize：字节大小
parity：校验位
stopbits：停止位
timeout：读超时设置
writeTimeout：写超时
xonxoff：软件流控
rtscts：硬件流控
dsrdtr：硬件流控
interCharTimeout:字符间隔超时
```

ser的方法

```python
ser.isOpen()                    # 查看端口是否被打开。
ser.open()                      # 打开端口。
ser.close()                     # 关闭端口。
ser.read()                      # 从端口读字节数据。默认1个字节。
ser.read_all()                  # 从端口接收全部数据。
ser.write("hello")              # 向端口写数据。
ser.readline()                  # 读一行数据。
ser.readlines()                 # 读多行数据。
in_waiting()                    # 返回接收缓存中的字节数。
flush()                         # 等待所有数据写出。
flushInput()                    # 丢弃接收缓存中的所有数据。
flushOutput()                   # 终止当前写操作，并丢弃发送缓存中的数据。
```

### `read()`, `readline()`, `readlines()` 三者的区别

#### 一般情况

这里有个txt的文件，其内容为：

```yaml
111
222
333
```

用 `read()` 的输出为：

```python
# read(size)方法从文件当前位置起读取size个字节，若无参数size，则表示读取至文件结束为止，它范围为字符串对象
file_read = open('file_path')
print(file_read.read())
print(type(file_read.read()))
file_read.close()

# output:
# 111
# 222
# 333
# <class 'str'>
```

**`read()` 每次读取整个文件，它通常将读取到底文件内容放到一个字符串变量中，也就是说 `read()` 生成文件内容是一个字符串类型。**

用 `readline()` 的输出为：

```python
file_readline = open('file_path')
print(file_readline.readline())
print(type(file_readline.readline()))
file_readline.close()

# output:
# 111
# <class 'str'>
```

**`readline()` 每只读取文件的一行，通常也是读取到的一行内容放到一个字符串变量中，返回str类型。**

用 `readlines()` 的输出为

``` python
file_readlines = open('file_path')
print(file_readlines.readlines())
print(type(file_readlines.readlines()))
file_readlines.close()

# output:
# ['111\n', '222\n', `333\n`]
# <class 'list'>
```

**`readlines()` 每次按行读取整个文件内容，将读取到的内容放到一个列表中，返回 list 类型。**

#### ser

串口发送数据内容为：

```yaml
b'Hello/r/n'
Arduino 端的代码为 Serial.println() 所以带/r/n。省麻烦可以用 Serial.print()
```

用 `read()` 读取：

```python
ser = serial.Serial('com1', 9600, timeout=1)
value = ser.read()
print(value)
print(type(value))

# output:
# b'H'
# <class 'bytes'>
```

用 `readline()` 读取：

``` python
ser = serial.Serial('com1', 9600, timeout=1)
value = ser.readline()
print(value)
print(type(value))

# output:
# b'Hello/r/n'
# <class 'bytes'>
```

目前我还没有遇到需要用 `readlines()` 的情况，等遇到了再补充。

### 去掉换行符和字符串前面b

利用 `strip()` 或者 `rstrip()` 删除 value 末尾的空格、换行符、回车符、制表符。

再利用 `decode()` 或者 `decode(’utf-8‘)` 对其进行解码，将 bytes 转换为 string。

```python
value = value.strip().decode()
print(value)
print(type(value))

# output:
# Hello
# <class 'str'>
```

### 关于 timeout 参数

这块内容来自[python serial库时read、readline、read_all等方法的区别](https://blog.csdn.net/linZinan_/article/details/114804811)。

虽然说得很有道理，但我不能复现下面内容，以 MARK 为主。

> 初始化的时候这里的 `timeout` 是指在设定的 `timeout` 时间范围内，如果读取的字节数据是有效的（就是非空）那就直接返回，否则一直会等到这个设定的 `timeout` 时间并返回这段时间所读的全部字节数据。
>
> 简单来说你可以理解为每 `timeout` 秒读一次数据，而如果 `timeout` 太小，数据量太大，可能一次发送不完，会数据未读完的状态。
>
> 也就是说，如果我的 `timeout` 设置为 0.01，现在我有一个串口通信的传感器，传感器通过串口使用发送一次数据，传感器如果在 0.01 秒内将所有数据都传输过来，就保存到缓存 `buffer` 中，到了 0.01 秒后就可以读取到传感器发送到 `buffer` 中的数据了；如果传感器在 0.01 秒内没有将数据发送全的话，`buffer` 中的数据就是不完全的，就会返回不完全的数据；如果传感器在 0.01 秒内发送了数据的话，`buffer` 也会返回两组合在一起的数据。
>
> `timeout` 的设置至关重要，如果不是特别高频的数据的话，`timeout=0.01` 的 100Hz 完全够了，当然 `timeout` 最小支持多少要看你的波特率。如果我 0.3 秒发送了一次数据，`timeout` 设置 0.01，就可以返回一次完整的数据；如果我 0.01 秒发送一次数据，`timeout` 设置 0.3，那么一次就会返回 30 次数据，所以这就是 `timeout` 的作用。
>
> 前面引入了 `buffer` 的概念，可以直接把 `buffer` 理解为一个文件夹，读数据的方式是和 `file` 是一样的，只是引入的 `timeout` 的概念，使得 `buffer` 内的数据会以 `1/timeout` 的频率进行刷新。
>
> 如果你把 `buffer` 当做 `file` 理解，那么 `readline()` 就是读取 `buffer` 中的一行，如果你的数据中存在 `\n` 这样的换行，那么用 `readline()` 先让不合适，很容易产生漏读的问题，而且如果你在一个 `timeout` 周期内没有用 `readline()` 处理完所有你想要的数据的话，`buffer` 就刷新了，因此推荐用 `read_all()` 或者 `readlines` 来读取。
