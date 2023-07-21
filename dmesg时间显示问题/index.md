# dmesg命令时间显示问题


## dmesg时间显示问题

默认显示时间戳

```bash
# dmesg
[24241362.110367] rmlog.sh (14746): drop_caches: 1
[24241362.120529] rmlog.sh (14746): drop_caches: 2
[24241362.218306] rmlog.sh (14746): drop_caches: 3
[24846165.362560] rmlog.sh (7364): drop_caches: 1
[24846165.371097] rmlog.sh (7364): drop_caches: 2
[24846165.463682] rmlog.sh (7364): drop_caches: 3
```

添加 `-T`参数，显示人类可以读懂的时间戳。

```bash
# dmesg -T 
[四 10月 20 00:01:10 2022] rmlog.sh (14746): drop_caches: 1
[四 10月 20 00:01:10 2022] rmlog.sh (14746): drop_caches: 2
[四 10月 20 00:01:10 2022] rmlog.sh (14746): drop_caches: 3
[四 10月 27 00:01:13 2022] rmlog.sh (7364): drop_caches: 1
[四 10月 27 00:01:13 2022] rmlog.sh (7364): drop_caches: 2
[四 10月 27 00:01:13 2022] rmlog.sh (7364): drop_caches: 3
```

