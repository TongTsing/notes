# linux常用命令

### 1.cut命令（https://blog.csdn.net/heguanghuicn/article/details/79383479）

- 作用
  cut命令是一个选取命令，其功能是将文件中的每一行”字节” ”字符” ”字段” 进行剪切，选取我们需要的，并将这些选取好的数据输出至标准输出
- 格式
  cut -[n]b file 
  cut -c file 
  cut -d[分隔符] -f[域] file
- 参数解释
  -b(bytes) ：以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了 -n 标志。 
  -c(characters) ：以字符为单位进行分割。 
  -d ：自定义分隔符，默认为制表符。 
  -f(filed) ：与-d一起使用，指定显示哪个区域。 
  -n ：取消分割多字节字符。仅和 -b 标志一起使用。如果字符的最后一个字节落在由 -b 标志的 List 参数指示的
  范围之内，该字符将被写出；否则，该字符将被排除。

### 2. free命令

- free -h 查看内存大小

7.9--7.11

### 3. 批量kill指定用户的进程

```shell
pgrep -u easyops | sudo xargs kill -9
```

#### 4.修改linux的时区

#####  4.1.--timedatectl命令

- timedatectl：显示当前机器设置的时区
- timedatectl list-timezones：列出全世界可用的时区
- timedatectl set-timezones xxx/xxx: 设置时区 

#####  4.2. 通过软连接修改系统时区

Linux系统使用`/etc/localtime`文件存储着系统的时区，它是一个软连接文件（也叫符号链接文件）。它指向`/usr/share/zoneinfo/`目录以及子目录下的时区文件。

这些时区文件以二进制的存储着时区的信息。当应用程序需要用户展示时区时。应用程序将读取`/etc/localtime`最终指向的二进制时区文件。

- 因此 只要在/etc目录下创建一个名为localtime的软连接文件，并且文件localtime指向/usr/share/zoneinfo/{亚洲目录}/Shanghai 即可

