# hexo文章发布和删除


### 创建文章

1. 进入博客目录，在 /source/_posts 文件夹下直接建立一个.md文件

    > hexo new “这里填入文章的标题”

2. .md文件注意使用带有makedown语法的编辑器打开，对文章进行编辑，你可以在Hexo->Source->_post目录下看到你新创建的那个文章，还有一个配套的文件夹，里面放这边博文的图片资源

    **文章标题，日期，标签，分类**

    ```Shell
    layout: title
    title: hexo文章发布和删除
    date: 2022-02-06 17:42:04
    tags: 标签
    categories: 分类
    ```

3. 文章插入图片使用**jsdelivr**进行cdn加速使图片访问更快。

    图片格式：

    ```Shell
    <img src="https://cdn.jsdelivr.net/gh/Smithkenny/Myimage/img/zsh插件.png"  />
    ```

### 发布新建文章

文章写好后，直接在git bash中执行以下命令即可直接发布文章

> hexo d -g

如果发布的时候出现错误

> ERROR Deployer not found: git

执行

> npm install hexo-deployer-git –save

或者直接使用git推送到远程仓库中,等待github action 自动部署网站。

```Shell
git add . 
git commit -m "说明"
git push origin master
```

### 删除文章

删除文章的过程一样也很简单，先删除本地文件，然后通过生成和部署命令进而将远程仓库中的文件也一并删除。具体来说，以最开始默认形成的helloworld.md这篇文章为例。

首先进入到source / _post 文件夹中，找到helloworld.md文件，在本地直接执行删除。然后依次执行

> hexo d -g

再去主页查看你就会发现你的博客上面已经空空如也了，这就是如何删除文章的方法。

