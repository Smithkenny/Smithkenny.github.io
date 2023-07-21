# linux垃圾桶


## rm垃圾桶

rm命令杀伤力很大，如果误删了系统文件，会很麻烦。所以，给linux系统建立一个垃圾桶，是很有必要的，详情参考[Make “rm” Command To Move The Files To “Trash Can”](https://www.2daygeek.com/rm-command-to-move-files-to-trash-can-rm-alias/)。

1、下载安装saferm.sh

```
git clone https://github.com/lagerspetz/linux-stuff
sudo mv linux-stuff/scripts/saferm.sh /bin
rm -Rf linux-stuff
```

2、编辑`.bashrc`文件，末尾添加

```
alias rm=saferm.sh
alias sudo='sudo '
```

3、使环境生效
`source .bashrc`
之后，再使用`rm`或者`sudo rm`命令，实际上执行的是mv操作，移动文件到Trash目录。

## 本机外网IP获取

```bash
https://ifconfig.me/ 
或
curl ifconfig.me
```




