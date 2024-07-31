dd分割命令
============================

分割
dd if=dgjd of=zz1 bs=1 count=20000000

dd if=dgjd of=zz2 bs=1 count=20000000 skip=20000000

dd if=dgjd of=zz3 bs=1 count=20000000 skip=40000000

dd if=dgjd of=zz4 bs=1 count=20000000 skip=60000000

dd if=dgjd of=zz5 bs=1 count=18336321 skip=80000000


if=后面是原文件名称， of是分割输出的文件名称，count是本次分割多大的包，skip是跳过多少既从多少开始

合并

dd if=zz1 of=xdgjd bs=1 count=20000000

dd if=zz2 of=xdgjd bs=1 count=20000000 seek=20000000

dd if=zz3 of=xdgjd bs=1 count=20000000 seek=40000000

dd if=zz4 of=xdgjd bs=1 count=20000000 seek=60000000

dd if=zz5 of=xdgjd bs=1 count=18336321 seek=80000000



if=后面是原文件名称， of是合并后生成的文件名称，count是本次分割多大的包，seek是从什么位置开始合并

分割之前需要md5sum校验，合并完成也需要进行md5sum进行校验，前后要
 保 持 一致致



=============================================
例子
=============================================
[root@test-easyops-11 lexin]#  dd if=easyops-base-amd64-install-enterprise.6.2.1.tar of=easyops-base-amd64-install-enterprise.6.2.1.tar_1 bs=1k count=2000000
记录了2000000+0 的读入
记录了2000000+0 的写出
2048000000字节(2.0 GB)已复制，8.34277 秒，245 MB/秒
[root@test-easyops-11 lexin]# dd if=easyops-base-amd64-install-enterprise.6.2.1.tar of=easyops-base-amd64-install-enterprise.6.2.1.tar_2 bs=1k skip=2000000

记录了2920947+0 的读入
记录了2920947+0 的写出
2991049728字节(3.0 GB)已复制，11.8666 秒，252 MB/秒
[root@test-easyops-11 lexin]# 
[root@test-easyops-11 lexin]# ll
总用量 9841908
-rw-r--r--. 1 root root 5039049728 6月  21 10:10 easyops-base-amd64-install-enterprise.6.2.1.tar
-rw-r--r--. 1 root root 2048000000 6月  22 17:55 easyops-base-amd64-install-enterprise.6.2.1.tar_1
-rw-r--r--. 1 root root 2991049728 6月  22 17:56 easyops-base-amd64-install-enterprise.6.2.1.tar_2

[root@test-easyops-11 lexin]# md5sum easyops-base-amd64-install-enterprise.6.2.1.tar
75043bd36bc15b769c3c403577700e7e  easyops-base-amd64-install-enterprise.6.2.1.tar
[root@test-easyops-11 lexin]# 

[root@test-easyops-11 lexin]# md5sum easyops-base-amd64-install-enterprise.6.2.1.tar_1
e11f176f7566d315755a22cfbc4d7c8d  easyops-base-amd64-install-enterprise.6.2.1.tar_1
[root@test-easyops-11 lexin]# md5sum easyops-base-amd64-install-enterprise.6.2.1.tar_2
b7b6790612539fc0fe893eddd9121f89  easyops-base-amd64-install-enterprise.6.2.1.tar_2



合并

dd if=easyops-base-amd64-install-enterprise.6.2.1.tar_1 of=easyops-base-amd64-install-enterprise.6.2.1.tar bs=1k count=2000000
dd if=easyops-base-amd64-install-enterprise.6.2.1.tar_2 of=easyops-base-amd64-install-enterprise.6.2.1.tar bs=1k seek=2000000