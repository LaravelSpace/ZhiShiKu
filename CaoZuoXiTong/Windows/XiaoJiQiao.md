## Windows中的一些小技巧

### hosts文件

hosts文件是一个没有扩展名的系统文件，是一个用于储存计算机网络中各节点信息的计算机文件，这个文件负责将主机名称映射到相应的IP地址 。其作用就是用来存储一些常用的网络域名和与其对应的IP地址。hosts文件通常用于补充或取代网络中DNS的功能。当用户输入一个需要登录的网址时，系统就会先去host文件中查找，如果找到了就立即打开该网址，如果找不到就去DNS域名解析服务器中查找。和DNS不同的是，计算机的用户可以直接对hosts文件进行控制。

### 把压缩文件隐藏在图片中

我们需要准备一张图片**1.jpg**和一个压缩包**2.rar**。然后新建一个**.bat**后缀的批处理文件，把下面的代码放进去。

```bat
copy /b 1.jpg+2.rar 3.jpg
```

把图片、压缩包和批处理文件放在同一个目录，执行批处理文件就会得到一张图片**3.jpg**。这个图片实际上同时包含了图片和压缩包。如果把图片**3.jpg**的后缀名改成**.rar**。这个时候这个压缩包是可以解压的，压缩包的内容就是**2.rar**的内容。