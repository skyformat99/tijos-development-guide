# tijos.runtime.deviceaccess.TiOWMaster - 单总线，主机模式

1-Wire总线是一个简单的信号传输电路，可通过一根共用的数据线实现主控制器与一个或一个以上从器件之间的半双工双向通信。具体可参考https://baike.baidu.com/item/1-Wire或https://en.wikipedia.org/wiki/1-Wire。

TiJOS JDK提供TiOWMaster来支持1-Wire主机功能，其将OW Master按照port分组，共支持2组，组号范围0-1，即：port0-port1；其中每组内按照io划分4条输入输出，输入输出号范围0-3，即：io0-io3；每条输入输出都支持指定模式的单独设置。



## TiOWMaster工作模式定义

| 项目   | 参数                           | 说明        |
| ---- | ---------------------------- | --------- |
| 工作模式 | MODE_STANDARD/MODE_OVERDRIVE | 标准模式/高速模式 |



## TiOWMaster方法定义

TiOWMaster类中主要的方法：

| 方法                                       | 说明                                       |
| ---------------------------------------- | ---------------------------------------- |
| TiOWMaster open(int portID, int[] ioIDList) | 静态方法，通过指定port打开OW Master，返回TiOWMaster对象，**若需要再次打开或打开为其他设备时需要先调用close关闭当前对象** |
| void close()                             | 关闭当前对象                                   |
| setIoMode(int ioID, int mode)            | 设置工作模式                                   |
| boolean resetIo(int ioID)                | 复位I/O                                    |
| int readIoBits(int ioID, int bits)       | 读I/O位数据                                  |
| void writeIoBits(int ioID, int value, int bits) | 写I/O位数据                                  |

TiOWMaster类中他方法的技术说明请参考TiJOS JDK Runtime说明文档。



## TiOWMaster对象创建与销毁

TiOWMaster对象的创建需要调用open静态方法，传入指定port和ioID列表参数，返回创建的接口对象。

TiOWMaster对象的销毁需要调用close方法。

```java
...
int owPort0 = 0;
int owIo0 = 0;
int owIo1 = 1;
int[] ioIDList = {owIo0, owIo1};
TiOWMaster ow0 = TiOWMaster.open(owPort0, ioIDList);
...
...
...
ow0.close();
```



## TiOWMaster模式设置

TiOWMaster模式设置通过setIoMode方法设置，设置参数为ioID和工作模式。

```java
...
ow0.setIoMode(owIo0, TiOWMaster.MODE_STANDARD); //io0工作在标准模式
ow0.setIoMode(owIo1, TiOWMaster.MODE_OVERDRIVE); //io1工作在高速模式
...
```

注意：模式支持受平台特性限制，设置时须了解平台特性。



## TiOWMaster输入输出

TiOWMaster的输入输出操作通过resetIo、readIoBits和writeIoBits方法完成。

```java
...
boolean status = ow0.resetIo(owIo0);
if(!status) {
    System.out.println("reset io0 fail.");
  	return ;
}
ow0.writeIoBits(owIo0, 0x80, 8);
int value = ow0.readIoBits(owIo0, 8);
...
boolean status = ow0.resetIo(owIo1);
if(!status) {
    System.out.println("reset io1 fail.");
  	return ;
}
ow0.writeIoBits(owIo1, 0x01, 1);
int value = ow0.readIoBits(owIo1, 2);
...
```



具体可参考TiJOS JDK Runtime说明文档。