# jupyter更改登录密码


## 前言

长时间不登陆jupyter竟然忘记了密码，所以记录下jupyter容器中更改密码过程。

## 更改过程

进入jupyter容器

```bash
docker exec -it vk-jupyter bash
cd .jupyter/
vi jupyter_notebook_config.json
```

生成新密码哈希值

```bash
ipython
```

复制新的哈希值替换jupyter_notebook_config.json文件中的哈希值

重启jupyter容器

```bash
docker restart vk-jupyter
```

尝试用新密码登录

