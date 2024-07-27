# Centos7安装Python3.7详细教程

注：如果安装更高的python版本，只需修改wget 后面的地址即可，然后注意执行命令时候的路径等问题。

如：安装python3.7.5

则：

```
wget https://www.python.org/ftp/python/3.7.5/Python-3.7.5.tar.xz
tar -xvJf  Python-3.7.5.tar.xz
```

1.安装编译相关工具

```
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
yum install libffi-devel -y
```

2.下载安装包解压

```
cd #回到用户目录
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
tar -xvJf  Python-3.7.0.tar.xz
```

3.编译安装python

```
mkdir /usr/local/python3 #创建编译安装目录
cd Python-3.7.0
./configure --prefix=/usr/local/python3
make && make install
```

4.创建软连接

```
ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3
```

5.验证是否成功

```
python3 -V
pip3 -V
```

————————————————
版权声明：本文为CSDN博主「Saggitarxm」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/xuezhangjun0121/article/details/103903984