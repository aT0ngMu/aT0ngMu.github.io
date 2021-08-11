---
title: USB流量分析
date: 2018-11-19 00:00:00
tags:
- 原创
- 流量分析
---

## USB接口简介

通过监听USB接口流量，可获取键盘击键，鼠标移动与点击，存储设备的明文传输通信，USB无线网卡网络传输内容等。

## 题目

wireshark打开数据包后发现为usb协议

![1.png](https://i.loli.net/2021/08/11/TsacRxh9b1BOv2M.png)

USB协议数据部分在Leftover Capture Data域中，使用tshark命令将其单独提取出来

``` shell
tshark -r udn.pcapng -T fields -e usb.capdata > usbdata.txt
```

cat命令查看分离出的usbdata.txt

``` shell
cat usbdata.txt
```

![2.png](https://i.loli.net/2021/08/11/M6Fl4GaJ7A3yVuP.png)

由于USB流量分为键盘流量和鼠标流量，而键盘数据包的数据长度为八个字节，鼠标数据包的数据长度为四个字节。

键盘数据包击键信息集中在第三个字节，每次key stroke都会产生一个keyboard event usb packet。

鼠标数据包第一个字节代表按键，当取0×00时代表没有按键；当取0×01时代表按左键；当取0×02时代表当前按键为右键。第二个字节可看作为signed byte类型，其最高位为符号位，当值为正时，代表鼠标右移像素位；值为负时，代表鼠标左移像素位。第三个字节代表垂直上下移动的偏移。

此题为键盘数据包

```
友情链接USB协议，查找值与具体键位的对应关系：
http://www.usb.org/developers/hidpage/Hut1_12v2.pdf
```

根据第53页 usb keyboard映射表写脚本解码得出数据包

![3.png](https://i.loli.net/2021/08/11/LQ9RZzuefENIcwT.png)

上脚本

``` python
mappings = { 0x04:"A",  0x05:"B",  0x06:"C", 0x07:"D", 0x08:"E", 0x09:"F", 0x0A:"G",  0x0B:"H", 0x0C:"I",  0x0D:"J", 0x0E:"K", 0x0F:"L", 0x10:"M", 0x11:"N",0x12:"O",  0x13:"P", 0x14:"Q", 0x15:"R", 0x16:"S", 0x17:"T", 0x18:"U",0x19:"V", 0x1A:"W", 0x1B:"X", 0x1C:"Y", 0x1D:"Z", 0x1E:"1", 0x1F:"2", 0x20:"3", 0x21:"4", 0x22:"5",  0x23:"6", 0x24:"7", 0x25:"8", 0x26:"9", 0x27:"0", 0x28:"\n", 0x2a:"[DEL]",  0X2B:"    ", 0x2C:" ",  0x2D:"-", 0x2E:"=", 0x2F:"[",  0x30:"]",  0x31:"\\", 0x32:"~", 0x33:";",  0x34:"'", 0x36:",",  0x37:"." }
nums = []
keys = open('usbdata.txt')
for line in keys:
if line[0]!='0' or line[1]!='0' or line[3]!='0' or line[4]!='0' or line[9]!='0' or line[10]!='0' or line[12]!='0' or line[13]!='0' or line[15]!='0' or line[16]!='0' or line[18]!='0' or line[19]!='0' or line[21]!='0' or line[22]!='0':
     continue
nums.append(int(line[6:8],16))
keys.close()
output = ""
for n in nums:
    if n == 0 :
        continue
    if n in mappings:
    output += mappings[n]
print 'output :\n' + output
```

![4.png](https://i.loli.net/2021/08/11/hGNXaxUEf5l6Pni.png)

另附鼠标脚本

``` python
nums = [] 
keys = open('data.txt','r') 
posx = 0 
posy = 0 
for line in keys: 
if len(line) != 12 : 
     continue 
x = int(line[3:5],16) 
y = int(line[6:8],16) 
if x > 127 : 
    x -= 256 
if y > 127 : 
    y -= 256 
posx += x 
posy += y 
btn_flag = int(line[0:2],16)  # 1 for left , 2 for right , 0 for nothing 
if btn_flag == 1 : 
    print posx , posy 
    keys.close()
```
