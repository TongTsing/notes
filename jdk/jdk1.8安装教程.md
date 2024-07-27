# [Linux安装jdk1.8和配置环境变量](https://www.cnblogs.com/zs-notes/p/8535275.html)

　　 每次感觉配这个都很简单，但每次都要查一下，毕竟配错一点 后面都比较麻烦，记录一下，方便以后查看。

　 　linux 下安装jdk和windows下的安装是一样的，之前在windows安装的时候是先下载压缩包，然后再配置环境变量。其实在linux下也是一样的。

　　 第一步：创建jdk安装目录(该/usr/local/src 目录是空的,最好把我们自己下载的放到这,容易区分)

   　　命令：mkdir -p /usr/local/src/jdk

 　第二步：查看安装程序

  　  命令：rpm -qa | grep -i jdk

  　　  若之前安装过jdk，下次安装一定把之前的删除干净

 　第三步：命令下载jdk包 （需要联网，下载也需要点时间）

 　　  本章使用的为后缀为tar.gz的文件（不需要安装），如jdk-8u131-linux-x64.tar.gz

  　  命令：[root@bogon jdk]# wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie"  http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz

  　 注意：如果上面wget命令不能用,报错：-bash: wget: command not found。执行一下该命令(安装依赖包) yum -y install wget

  　 注意 : 记住需要加上：--no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie"   这段是为了避开用户验证用的

 　第四步：解压

 　　  [root@bogon jdk]# tar -zxvf jdk-8u131-linux-x64.tar.gz

 　　  系统会生成一个名为：jdk1.8.0_131 的文件夹。可以使用mv命令自定义文件名。

 　   [root@bogon jdk]# mv jdk1.8.0_131 jdk1.8

 　第五步：配置环境变量

  　　 [root@bogon bin]# vi /etc/profile

  　　 可以看到这个文件的内容，profile文件有点类似于windows系统里面的环境变量的配置，

   　 shift + g 定位到最后一行

  　　 这个时候按一下a或i键，进入编辑模式

![img](../1328409-20180309175813890-476918162.png)

  　　 中间用红色的线圈起来的就是需要加的( JAVA_HOME 是jdk安装目录，和在Windows下配置一样 )

> **export JAVA_HOME=/usr/local/src/jdk/jdk1.8**
>
> **export PATH=$PATH:$JAVA_HOME/bin**
>
> **export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar**

 　第六步：退出文件，保存

   　 按Esc键  输入:wq  保存并退出

 　第七步：让配置文件生效

　　　　[root@bogon jdk]# source /etc/profile

 　第八步：查看是否配置成功

   　  [root@bogon java1.8]# java -version   

　　　　若出现jdk版本号，则安装并配置环境变量成功

​    　如果提示命令找不到的话，查看一下jdk的配置路径是否错误。

![img](../1328409-20180309180852815-936433149.png)

 