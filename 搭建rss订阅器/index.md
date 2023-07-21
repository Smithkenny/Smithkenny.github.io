# 搭建rss订阅器


## 搭建rss订阅器

```bash
mkdir -p /data/freshrss

docker run -d   --name=freshrss   -e PUID=1000   -e PGID=1000   -e TZ=Asia/Shanghai   -p 8000:80   -v /data/freshrss:/config   --restart unless-stopped   freshrss/freshrss:latest
```

