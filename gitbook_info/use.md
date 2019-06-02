# 输出格式

gitbook可以生成各种格式的电子书

1. 静态网站
这是默认的格式。它生成一个可交互的静态站点。

2. PDF (Portable Document Format)
PDF是一种独立于软硬件，以及操作系统的方式来保存文档的格式。文件拥有的扩展名为.pdf。
生成命令：  
> $ gitbook pdf ./ ./mybook.pdf  

3. ePub (electrontic publication)
EPUB是一种由国际电子出版物论坛(IDPF)指定的免费并开放的电子书标准。扩展名 `.epub`,苹果和谷歌的设备支持ePub格式。
生成命令：  
> $ gitbook epub ./ ./mybook.epub   

4. Mobi (Mobipocket)
Mobipocket电子书格式是基于使用XHTML的开放电子书标准，并且可以包含JavaScript以及框架。亚马逊的设备 (Kindle) 支持这样的格式。扩展名`.mobi`  
生成命令：  
> $ gitbook mobi ./ ./mybook.mobi   

# 章节和子章节
包含子章节的例子  
```
# 概要

* [卷 I](part1/README.md)
    * [写作很棒](part1/writing.md)
    * [GitBook很酷](part1/gitbook.md)
* [卷 II](part2/README.md)
    * [期待反馈](part2/feedback_please.md)
    * [更好的写作工具](part2/better_tools.md)

```

