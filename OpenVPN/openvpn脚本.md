## 常用脚本

### 一、openvpn从服务端获取新的客户端配置文件，并重启vpn

```bash
#!/bin/bash

read -p "input client name to update client.ovpn and restart openvpn: " client

scp -P22211 root@1.92.68.135:/etc/openvpn/CA/${client}/${client}.conf /root/openvpn/client.ovpn

pkill -9 "openvpn --config"

nohup openvpn --config /root/openvpn/client.ovpn >/tmp/openvpn.log 2>&1 &

```

### 二、创建新的客户端证书、配置文件

```shell
#!/bin/bash

read -p '请输入用户名称：' client 

cd /etc/openvpn/CA

echo "making dir ........"
mkdir -p /etc/openvpn/CA/${client}
echo "create client dir: /etc/openvpn/CA/${client} success ........."


client_dir="/etc/openvpn/CA/${client}"

echo "creating client private key!........."
openssl genpkey -algorithm RSA -out ${client_dir}/${client}.key
echo "client private key created!..........."

echo "creat client req file..........."
openssl req -new -key ${client_dir}/${client}.key -out ${client_dir}/${client}.req -subj "/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd/CN=${client}"

echo "req file created!.........."

echo "creating client crt!........"
openssl x509 -req -days 36500  -in ${client_dir}/${client}.req -CA /etc/openvpn/server/ca.crt -CAkey /etc/openvpn/server/ca.key -out ${client_dir}/${client}.crt -CAcreateserial -extfile <(printf "keyUsage=keyCertSign,digitalSignature")

echo "crt created!..........."

echo "exited="

ca_value=`cat /etc/openvpn/server/ca.crt`
key_value=`cat ${client_dir}/$client.key`
cert_value=`cat ${client_dir}/$client.crt`

awk -v new="${ca_value}" -v old="ca-value" '{gsub(old, new); print}' /etc/openvpn/CA/example.conf > ${client_dir}/${client}1.conf


awk -v new="${key_value}" -v old="key-value" '{gsub(old, new); print}' ${client_dir}/${client}1.conf > ${client_dir}/${client}2.conf



awk -v new="${cert_value}" -v old="cert-value" '{gsub(old, new); print}' ${client_dir}/${client}2.conf > ${client_dir}/${client}.conf 




if [ $? -eq 0 ]; then
    num=`cat /etc/openvpn/CCD/num`
    echo "ifconfig-push 10.8.${num}.249 10.8.${num}.250" > /etc/openvpn/CCD/${client}
    num=$(($num+1))
    echo "$num" > /etc/openvpn/CCD/num
else
    echo "创建失败!"
fi
```

### 三、客户端检测openvpn服务器的存活

```
#!/bin/bash

ip_to_ping="10.8.0.1"

current_time=`date`
# 使用ping命令进行检测
ping -c 2 $ip_to_ping > /dev/null 2>&1
# 检查ping命令的退出状态
if [ $? -eq 0 ]; then
    echo $current_time:"openvpn server alive!" >> /tmp/openvpn-detect.log
else
    amz-openvpn-conf-update <<EOF
jump3
EOF
   echo $current_time: "update openvpn conf succed!!" >> /tmp/openvpn-detect.log
fi
```

### 四、服务端给所有配置文件（包含服务端配置和客户端配置）更换端口

```
#!/bin/bash

port_file="/etc/openvpn/tmp/port"
server_conf="/etc/openvpn/server.conf"
service_name="openvpn@server.service"
# 生成新的port
gen-port.sh
# 读取最后一行数字到变量port
port=$(tail -n 1 $port_file)

# 执行sed命令替换server.conf中的端口号
sed -i "s/\(port \)[0-9]\{1,5\}/\1${port}/g" $server_conf

# 执行sed命令替换client.conf中的端口
client_conf_basedir="/etc/openvpn/CA"
client_dirs=$(ls /etc/openvpn/CA/)
sed -i "s/\(www.kekebayby.love \)[0-9]\{1,5\}/\1${port}/g" $client_conf_basedir/example.conf
for client in $client_dirs;do
    sed -i "s/\(www.kekebayby.love \)[0-9]\{1,5\}/\1${port}/g" $client_conf_basedir/$client/${client}.conf
done
# 重启OpenVPN服务
systemctl restart $service_name

echo "端口号已更新为 $port，并且 OpenVPN 服务已重启。"
```

### 五、生成随机端口供脚本四使用

```
#!/bin/bash

port_file="/etc/openvpn/tmp/port"

# 生成一个50000到65535之间的随机数字
random_port=$(shuf -i 50000-65535 -n 1)

# 检查是否端口已存在，如果存在则重新生成
while grep -q $random_port $port_file; do
    random_port=$(shuf -i 50000-65535 -n 1)
done

# 保存到文件
echo $random_port >> $port_file

echo "生成的唯一随机端口号是：$random_port，并已保存到 $port_file 文件中。"
```

