# SteganographierGUI
将文件隐写进MP4文件中

## 免责声明:

<font color="#c0504d"><b>本程序仅用于保护个人信息安全，请勿用于任何违法犯罪活动</b></font>

<font color="#c0504d"><b>否则[后果](https://mps.gjzwfw.gov.cn/)自负，开发者对此不承担任何责任</b></font>

## 更新

v1.0.1 修复了无法隐写ZIP格式文件的bug

v1.0.2 新增隐写为mkv文件的逻辑，引入第三方工具用于处理mkv文件

```
.\tools\mkvextract.exe
.\tools\mkvinfo.exe
.\tools\mkvmerge.exe
```

v1.0.3 改进
```
1. 隐写文件夹时现在会在压缩包内生成同名的文件夹  
2. 修改tools的判断逻辑，如果不使用mkv模式则不会弹出警告  
3. 新增mkv文件隐写大小警告（单个mkv文件不能隐写总量超过2GB的资源）  
4. 修正一些其他bug
```

v1.0.4 改进：新增外壳文件选择菜单

v1.0.5 改进：隐写时文件末尾增加随机字节，以使得每次生成的文件哈希值不同

v1.0.6 修改：密码获取的逻辑变更，现在可以不指定密码

v1.0.7 改进：外壳文件菜单中的文件现在会按照时长降序排列


## 1. 背景

如今，国内各大网盘审查政策日趋严格，分享链接炸链的可能性越来愈大。

传统来说，我们采用**带密码的多层压缩包**来应对审查问题。这样的做法非常麻烦，并且当层数多、文件大时，频繁解压对于硬盘的损耗也是难以忽视的。围绕这个问题，过去产生了多种利用网盘特性进行**秒传**的解决方案，但是随着网盘政策的收紧，这些方案大多已经失效。

此外，随着网盘政策的变化，某些格式的压缩包在未来可能不再被允许上传，如目前百度网盘不允许上传带密码的 rar 格式压缩包。

综上，这使得研究和开发更加隐蔽的数据传输方法变得尤为重要。

  

## 2. 方法介绍：把文件隐写到MP4文件中

本程序受到[仓库文章](https://cangku.moe/archives/211591)（以下简称文章[1]）的启发，利用文件隐写技术来隐藏数据从而绕过常规审查。

隐写技术通过将数据嵌入到其他媒体文件中，使数据的存在对于普通观察者而言不可见，从而实现在不引起注意的情况下进行信息传输。

隐写技术已经有很多先例，传统做法主要有**图种**，即把数据嵌入图片中，表面上看起来是一张图片，但修改后缀名即可解压然后得到隐藏的数据。

图种的原理如下：

```sh
copy /b "图片.jpg" + "压缩包.zip" "生成目标.jpg"
```

但是这样的做法容易引起怀疑，毕竟一张清晰度分辨率都并不算高的图片居然有几个G，并且还有非常高的下载转存记录，这实在太可疑了[[1]](https://cangku.moe/archives/211591)。

因此，考虑伪装的有效性，使用MP4文件作为隐写的外壳文件更为合理一些，大视频引起怀疑的可能性显然低于大图片。

我们的目标是通过隐写伪装来降低可疑度，从而尽可能以最低的成本实现安全分享。因为假如被频繁举报，即使压缩包层数再多，密码再复杂，在已经被强烈怀疑的情况下大概也回天乏术。

**最好的防御不是叠甲，而是伪装。**

具体实现方面：将 ZIP 格式的压缩包嵌入到海绵宝宝的 MP4 视频文件中，当文件以 MP4 格式被打开时，只能看到海绵宝宝的视频，看不到 ZIP 部分；而当文件后缀名改为 ZIP 以后，解压软件（如WinRAR）可以寻找到 ZIP 部分进行正常解压。如此实现文件的安全分享。

## 3. 问题与改进

虽然文章[\[1\]](https://cangku.moe/archives/211591)提供了一个有效的代码实现用于文件隐写，但该方法缺乏一个简单易用的操作界面，这限制了其推广与普及。

本程序在文章[[1]](https://cangku.moe/archives/211591)的基础上进行简化，开发了一个包含图形用户界面（GUI）的隐写程序，使用户能够通过简单的拖放和点击操作完成文件的隐写和解隐写。

2024.4.24 新增：根据文章[[2]](https://cangku.moe/archives/199992)提出的方法，也可以把文件以附件的形式嵌入到MKV文件中，在 v1.0.2 版本中新增了此逻辑。


## 4. GUI设计与功能介绍

![Clip_2024-04-27_17-16-05](https://github.com/cenglin123/SteganographierGUI/assets/167851968/fac293f3-39ca-4bbe-9c98-da4ac781ba45)


演示视频：
[https://github.com/cenglin123/SteganographierGUI/assets/167851968/e632d2da-1e06-4e78-b834-553844721bec](https://youtu.be/8HmKQRUL7FQ)

本程序允许通过**输入密码**和**拖入文件**的方式来直接进行文件的隐写和解隐写。

程序具有以下特点：

**一体化操作**：既可以进行**隐写**，也可以在同一个界面进行**解除隐写**操作，提升了程序的整体效率和便利性。

**拖放功能**：支持拖放文件或文件夹到指定区域，简化了文件选择的过程。

**通用性**：产生的隐写MP4文件也可以**手动**修改后缀名解压，**并不强制要求使用本程序**。

**密码保护**：~**必须**输入密码才能进行隐写或解隐写操作。~ (2024.4.29 于 `1.0.6` 版本中修改，现在可以不指定密码)

  

## 5. 不足与展望

目前的程序仍然存在一些问题，比如合并方法是简单地将ZIP文件附加到视频文件的末尾。这种方法虽然易于实现但也容易被检测到。

后续也许可以考虑使用一种更加隐蔽的方式，例如将ZIP文件的内容嵌入到视频文件的某些不太关键的部分，在每个I帧后插入一小段数据等。这类做法需要分析视频文件的编码细节，可能需要用到其他库如 FFmpeg 等，具体留待后续研究。

尽管如此，根据文章[\[1\]](https://cangku.moe/archives/211591)作者 [@亜璃紗](https://cangku.moe/user/272506/post) 的测试结果，**在不被特意针对性举报的情况下，这样的隐写方法已经足以认为是一个可以推广的解决方案了**。

今后随着技术的进一步完善，此类隐写方法或许能成为替代秒传链接的一个有效手段。

**此程序权当抛砖引玉，目前测试仍不充分，可能有bug，欢迎各位积极参与研究**。


