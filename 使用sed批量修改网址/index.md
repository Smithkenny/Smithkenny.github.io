# 使用sed批量修改网址


### 将时间戳转换为指定格式

### 文本如下

```sh
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article1.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article2.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article3.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article4.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article5.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article6.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article7.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article8.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article9.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article10.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article11.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article12.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/article13.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/animal.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/blue.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/cat.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/coffee.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/finch.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/lighthouse.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/nature.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/soda-water.jpg
https://fastly.jsdelivr.net/gh/Smithkenny/Myimage/img/swan.jpg
```

将`https://fastly.jsdelivr.net`都修改为`https://qq.com`

```shell
sed -i -e "s/https:\/\/fastly.jsdelivr.net/https:\/\/qq.com/" img.txt
```

结果如下：

```shell
https://qq.com/gh/Smithkenny/Myimage/img/article1.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article2.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article3.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article4.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article5.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article6.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article7.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article8.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article9.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article10.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article11.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article12.jpg
https://qq.com/gh/Smithkenny/Myimage/img/article13.jpg
https://qq.com/gh/Smithkenny/Myimage/img/animal.jpg
https://qq.com/gh/Smithkenny/Myimage/img/blue.jpg
https://qq.com/gh/Smithkenny/Myimage/img/cat.jpg
https://qq.com/gh/Smithkenny/Myimage/img/coffee.jpg
https://qq.com/gh/Smithkenny/Myimage/img/finch.jpg
https://qq.com/gh/Smithkenny/Myimage/img/lighthouse.jpg
https://qq.com/gh/Smithkenny/Myimage/img/nature.jpg
https://qq.com/gh/Smithkenny/Myimage/img/soda-water.jpg
https://qq.com/gh/Smithkenny/Myimage/img/swan.jpg
```


