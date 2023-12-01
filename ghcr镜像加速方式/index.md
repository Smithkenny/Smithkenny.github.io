# Ghcr镜像加速方式


直接拉取镜像速度很慢，网上看到一个加速方法很好用，特此记录一下。

```bash
docker pull ghcr.io/home-assistant/home-assistant:stable
# 这里的ghcr.io是github的docker镜像地址
```

加速方法：将`ghcr.io`替换为`ghcr.nju.edu.cn`。

```bash
docker pull ghcr.nju.edu.cn/home-assistant/home-assistant:stable
```

[原文章](https://zhuanlan.zhihu.com/p/602161992)
