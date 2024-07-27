# [linux添加环境变量](https://www.cnblogs.com/yaoqingzhuan/p/10889718.html)

**添加环境变量之前需要明白以下几点：**

1、Linux的环境变量是保存在变量PATH中(window 也是保存在PATH中)，可通过命令 echo $PATH 输出查看

2、Linux环境变量值之间是通过冒号分隔的( : )

　　其格式为：PATH=$PATH:<PATH 1>:<PATH 2>:<PATH 3>:------:<PATH N>

**临时添加环境变量PATH：**

可通过export命令，如

export PATH=/usr/local/nginx/sbin/:$PATH，将/usr/local/nginx/sbin/目录临时添加到环境变量中

**当前用户永久添加环境变量：**

编辑.bashrc文件 vim ~/.bashrc  **<<---- 通过这种方式，在关闭xshell后，添加的环境变量仍然生效**

文件末尾添加：export PATH="/usr/local/nginx/sbin/:$PATH"

source ~/.bashrc

 

**所有用户永久添加环境变量：**

　　编辑/etc/profile文件 vim /etc/profile  **<<---- 通过这种方式，在关闭xshell后，添加的环境变量不生效**

　　文件末尾添加：export PATH="/usr/local/nginx/sbin/:$PATH"

　　source /etc/profile

总结：

　　linux添加环境变量注意几点：

　　　　1、变量之前使用冒号分隔

　　　　2、使用命令export

　　　　3、export时，需要有$PATH

　　　　4、在文件的末尾添加

　　　　5、配置文件有，/etc/profile 和 ~/.bashrc

　　　　6、添加bin或者sbin目录即可

 