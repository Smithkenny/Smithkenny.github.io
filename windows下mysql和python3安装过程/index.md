# Windows下mysql和python3安装过程


## mysql8.0安装过程
https://blog.csdn.net/m0_62808124/article/details/126769669

## 开启远程访问
```sh
use mysql;
update user set host = '%' where host = 'localhost' and user = 'root';
flush privileges;
# 修改密码
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
flush privileges;
#设置允许远程访问
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
flush privileges;
```

## python3 安装参考
[参考](https://blog.csdn.net/stormjun/article/details/133806717#:~:text=Python3%20%E4%B8%8B%E8%BD%BD%E3%80%81%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B%EF%BC%8C%E9%99%84%E8%AF%A6%E7%BB%86%E5%9B%BE%E8%A7%A3%201%201.%20%E4%B8%8B%E8%BD%BDpython%E5%AE%89%E8%A3%85%E5%8C%85%202%202.%20%E5%AE%89%E8%A3%85python,4.%20%E7%82%B9%E5%87%BB%E4%B8%8B%E6%96%B9limit%EF%BC%8C%E8%AE%BE%E7%BD%AEMAX_PATH%EF%BC%8C%E5%90%8C%E6%97%B6%E6%8E%88%E4%BA%88%E7%AE%A1%E7%90%86%E5%91%98%E6%9D%83%E9%99%90%205%205.%20%E5%AE%89%E8%A3%85%E5%AE%8C%E6%88%90%206%206.%20%E9%AA%8C%E8%AF%81)




