# 文章中图片优化

### 一、需求
由于文章中图片比较大，所以迫切需要将原图片压缩但质量又不会丢失太多。`google`发现可以将`png`、`jpeg`等格式的图片转换成`webp`格式。体积缩小的同时又不失真。

工具[<span style="color:orange">官网</span>](https://storage.googleapis.com/downloads.webmproject.org/releases/webp/index.html)。

我的环境是windows,下载：`libwebp-1.3.1-windows-x64.zip`。解压后添加环境变量。
我的电脑->属性->高级->环境变量->系统变量中找到path->新增`cwebp`程序路径：`C:\Users\1\Downloads\libwebp-1.3.1-windows-x64\bin`。

这样可以在任何文件夹内都可以使用`cwebp`命令压缩图片。

### 二、图片压缩命令
首先进入需要压缩的图片文件夹内，在当前文件夹内输入`cmd`命令打开dos窗口。例如我们需要将所有`png`、`jpg`格式的图片转换成`webp`格式。输入以下命令:
```bash
for %f in (*.jpg *.png) do cwebp -q 80 "%f" -o "%~nf.webp"
```
然后将转换完成后的图片转移到我们项目的图片路径中（我这里是`/postimages`），最后在文章中引入图片路径就完成了。





