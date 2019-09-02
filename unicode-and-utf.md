# Unicode 与 UTF 的关系

## Unicode的由来
为了在计算机中表示我们平时写的字符（如数字0、1、2，字母a、b、c、A、B、C，标点符号，汉字等），需要对这些字符进行编码，即在计算机中用特定的数字表示这些字符。

### 美国的 ASCII 字符集
最初，计算机在美国被使用时，由于英文的只有26个字母，算上大小写即其他符号，字符数也不多，于是只需要7个bit（可表示128个字符）就足够了。于是出现了最初的`ASCII`字符集，该字符集用0~127，共128个**码位**（Code Point，或称码点，即一个唯一的整数）来表示128个字符。在计算机中具体应用起来也比较简单，一个字符仅用一个字节即可表示，且只用到了一个字节的低7位，其最高位始终置为0。

`ASCII`字符集的全称是 American Standard Code for Information Interchange（美国信息交换标准代码），等同于国际标准的`ISO/IEC 646`字符集。

### 欧洲的 ISO/IEC 8859 字符集
後来，欧洲开始使用计算机，发现他们用的一些字符并不在`ASCII`字符集中，需要对`ASCII`进行扩充，于是出现了一系列的`ISO/IEC 8859`（又称`ISO 8859`）字符集。`ISO/IEC 8859`是国际标准化组织(ISO)及国际电工委员会(IEC)联合制定的一系列8位字符集的标准，现时定义了15个字符集。

`ISO/IEC 8859`字符集也只用一个字节编码单个字符，但扩展了ASCII中单字节最高位的含义：**字节的最高位为1时，用来标识那些新增的拉丁字母与西里尔字母等；而最高位为0时，含义等同于ASCII字符集**。

### 汉字的字符集
在中国大陆，汉字的主要字符集包括：
1. 首先是1980年发布的`GB2312`编码方案，收录汉字6763个和非汉字图形字符682个；
1. 接着是1995年发布的`GBK`（国标扩展）编码方案，向下与`GB2312`编码兼容，有23940个码位，共收录21003个汉字（包括传统繁体字）；
1. 以及2000年发布的`GB18030`，收录汉字70244个，其对`GB2312`完全兼容，与GBK基本兼容，并支持`Unicode(GB13000)`的所有码位。

在台湾、香港与澳门等繁体中文（正体中文）通行区，用的较多的是俗称大五码的Big5字符集标准。

### 越来越多的字符集
随着计算机在全世界的普及，全世界文字的字符用1个字节（最多表示256个不同的字符）根本无法表示，尤其是像汉字这种含有几万十几万个不同字符的文字，另外还有一些科学上用到的符号、表情符号等。于是世界上出现了越来越多的字符集及相应的编码方案，由于字符量的增多，编码单个字符的字节数也不局限于1个。


 所以这就成了不同国家有不同国家的编码方式，所以如果给你一串二进制数，想要解码，就必须知道它的编码方式，不然就会出现我们有时候看到的乱码 。

随着编码方案的增多，各国计算机的字符编码出现了不兼容的情况，不利于彼此的信息交流。为了统一世界范围内所有字符的编码，于是出现了`Unicode`（统一码）字符集。`Unicode`字符集由Unicode联盟（Unicode Consortium）维护，研发始于1990年，1994年正式公布。

## Unicode字符集
`Unicode`字符集分为17个平面（Plane），每平面拥有65536个码位，共1114112个（码位范围为 0x0000~0x10FFFF）。`Unicode`目前只用了少数几个平面，收录了约15万个字符。

第0平面(又称Basic Multilingual Plane，BMP)收录了最常用的字符，其码位范围为0x0000 ~ 0xFFFF，其中中日韩统一表意符号 (CJK Unified Ideographs)的码位范围为0x4E00 ~ 0x9FBF，其中的0x4E00~0x9FA5为汉字。

`Unicode`在表示一个字符时，通常以“`U+`”开头，後面跟着16进制的数字。如汉字“中”用`Unicode`表示为`U+4E2D`，其中16进制的4E2D的10进制值为20013，即20013这个码位在`Unicode`字符集中表示汉字“中”。

第0平面的所有字符，由于其码位范围为0x0000~0xFFFF，因此只用4位16进制数即可表示。而在0号平面以外的字符，则需要使用5或6位16进制数表示了。

## UTF编码方式

#### 字符集 VS 编码方式
广义的`Unicode`可分为字符集和编码方式两个层次，而狭义的`Unicode`只是一种将码位与字符对应起来的字符集，但是具体如何在计算机中进行编码（如用几个字节表示一个字符，每个字符所用字节数是否相同，若所用字节数不同则如何确定单个字符的字节边界等），还需要更为细致的实现方案即编码方式。

`UTF`(Unicode Transformation Format，直译为Unicode字符集转换格式) ，就是规定怎样将`Unicode`定义的码位转换成程序数据，从而在计算机中使用`Unicode`字符集的编码方案。

UTF目前有3套编码方式，分别为UTF-8、UTF-16、UTF-32，它们分别以单个字节（8 bit）、2个字节（16 bit）、4个字节（32 bit）为编码的基本单位，且都是不等长的字符编码方式。

#### UTF-32
UTF-32对任意Unicode字符编码都需要4个字节。

#### UTF-8
UTF-8是一种变长的编码规则，它根据每个字符的码位范围，用1到6个字节来表示，具体编码规则如下：
```
U+ 0000 ~ U+ 007F: 0XXXXXXX
U+ 0080 ~ U+ 07FF: 110XXXXX 10XXXXXX
U+ 0800 ~ U+ FFFF: 1110XXXX 10XXXXXX 10XXXXXX
U+10000 ~ U+10FFFF: 11110XXX 10XXXXXX 10XXXXXX 10XXXXXX
```
常用的汉字基本都是3个字节编码的，部分生僻的汉字以及表情符号等，则需4个字节编码。

从编码规则可以看出：
- **当字符的码位落在区间[0, 127]时，则只用1个字节表示即可**。该字节最高位为0，低7位用来表示0~127这128个数值。

>> 如字母“a”的码位为U+0061（10进制值为97，2进制值为1100001），按UTF-8编码规则，在计算机中用 01100001（0x61） 这样1个字节表示。这其实就是`ASCII`字符集的范围。

- **当字符的码位落在区间[128, 2047]（共1920个值）时，则用2个字节表示**。第1个字节的高3位固定为110，剩余5位可变；第2个字节的高2位固定为10，剩余6位可变。因此一共有5+6=11位可变，有2^11=2048个不同取值（多于1920个），因此用来一一映射到[128, 2047]这些数是没问题的。

>> 如乘号标记×，其Unicode码位为U+00D7（10进制值为215，2进制值为11010111）。转换成UTF-8编码时，将11010111的低6位单独取出，为010111，前面加上10前缀，成为10010111，即16进制的0x97；剩余的高2位（11）补足成5位（00011），再加上前缀110後为11000011，即16进制的0xC3。因此乘号×的UTF-8表示法为2个字节，高字节为0xC3，低字节为0x97。

- **当字符的码位落在区间[2048, 65535]（共63488个值）时，则用3个字节表示**。第1个字节的高4位固定为1110，第2个字节的高2位固定为10，第3个字节的高2位固定为10。因此一共有4+6+6=16位可变，有2^16=65536个不同取值（多于63488个），因此用来一一映射[2048, 65535]这些数也不会有问题。

>> 如汉字“中”的Unicode码位为U+4E2D（10进制值为215，2进制值为1001110_00101101）（下划线_是为了阅读方便加上的）。转换成UTF-8编码时，先将低6位101101单独取出，加上10前缀後为10101101，即16进制的0xAD；再将中间6位111000取出，加上前缀10後为10111000，即16进制的0xB8；最後剩余3位100，补足成4位0100，再加上前缀1110後为11100100，即16进制的0xE4。因此汉字“中”在UTF-8编码规则中为3个字节，最高字节为0xE4，中间字节为0xB8，最低字节为0xAD。

#### 字节序
字节序有两种，分别是“大端”（Big Endian, BE）和“小端”（Little Endian, LE）。

## 附记：关于UCS和Unicode
国际标准化组织（ISO）和多语言软件制造商组成的`Unicode`联盟都曾独立地创立统一的标准字符集。ISO开发了通用字符集（Universal Character Set, UCS）`ISO 10646`（或称`ISO/IEC 10646`），`Unicode`联盟则开发了`Unicode`。
由于意识到不需要两个不兼容的字符集，两个组织便合并了双方的工作成果，并为创立一个单一编码表而协同工作。从Unicode 2.0开始，Unicode采用了与ISO 10646-1相同的字库和字码；ISO也承诺，ISO 10646将不会替超出U+10FFFF的UCS-4编码赋值，以使得两者保持一致。

#### 参考文档：
1. [Unicode Consortium](https://home.unicode.org/basic-info/overview/)
1. [Unicode 和 UTF-8 有什么区别？——知乎](https://www.zhihu.com/question/23374078)
1. [utf-8和Unicode的区别](https://www.cnblogs.com/dhsz/p/7737480.html)
1. [Unicode与UTF-8的区别](https://blog.csdn.net/hezh1994/article/details/78899683)
1. [Unicode——百度百科](https://baike.baidu.com/item/Unicode/750500)
1. [ISO/IEC 8859——百度百科](https://baike.baidu.com/item/ISO%2FIEC%208859/916777)
