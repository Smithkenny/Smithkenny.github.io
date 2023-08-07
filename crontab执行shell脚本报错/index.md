# Crontab执行shell脚本报错


shell执行脚本报错如下(环境centos7)

```sh
[: ==: unexpected operator
```

脚本如下：

```sh
#!/bin/sh

if [ $1 == "hello" ]; then
    echo "Well done!"
fi
```

这是因为 shell 脚本开头使用 `#!/bin/sh`，而不是 `#!/bin/bash`。而 `/bin/sh` 使用的是 dash，dash 中默认的判断语句是 `=`，而不是 bash 使用的 `==`。

将`#!/bin/sh`改为`#!/bin/bash`后，执行成功。
