```bash
# 设置内核参数
sysctl -w net.ipv4.ip_forward=1
# 安装软件
yum -y update
yum -y install openvpn
# 创建目录
mkdir /etc/openvpn/CA
mkdir /etc/openvpn/CCD
mkdir /etc/openvpn/CA/private
mkdir /etc/openvpn/CA/certs
mkdir /etc/openvpn/CA/reqs

# 制作CA证书和密钥
cd /etc/openvpn/CA
# 获取openssl版本：
openssl_version=openssl version|awk '{print $2}'|awk -F '.' '{print $1 "." $2}'
openssl genpkey -algorithm RSA -out private/ca.key
if [ $openssl_version $eq "1.0" ]; then
	openssl req -x509 -newkey rsa:2048 -keyout private/ca.key -out certs/ca.crt -days 36500 -nodes
# openssl1.0.0
else
	openssl req -x509 -new -key -days 36500 private/ca.key -out certs/ca.crt
# 制作服务端证书和密钥
openssl genpkey -algorithm RSA -out private/server.key

openssl req -new -subj "/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd/CN=macOS" -key private/server.key -out reqs/server.req 

openssl x509 -req -days 36500 -in reqs/server.req -CA certs/ca.crt -CAkey private/ca.key -out certs/server.crt -CAcreateserial -extfile <(printf "keyUsage=keyCertSign,digitalSignature\n")
# 配置服务端证书
cd /etc/openvpn/server
cat >> server.conf << EOF
;local a.b.c.d

port 65300

;proto tcp
proto udp

;dev tap
dev tun

;dev-node MyTap

ca /etc/openvpn/server/ca.crt
cert /etc/openvpn/server/server.crt
key /etc/openvpn/server/server.key

dh /etc/openvpn/server/dh2048.pem


server 10.8.0.0 255.255.0.0

ifconfig-pool-persist ipp.txt

client-config-dir /etc/openvpn/CCD
;client-to-client
;duplicate-cn
keepalive 10 120
;cipher AES-256-CBC
persist-key
persist-tun
;status openvpn-status.log
;log         /tmp/openvpn.log
;log-append  /tmp/openvpn.log
verb 5
;explicit-exit-notify 1
;route-nopull #don't pull routes from the server
;route 10.8.0.0 255.255.0.0
EOF

# 生成dh证书
openssl dhparam -out /etc/openvpn/server/dh2048.pem 2048

# 启动openvpn 服务
nohup /usr/sbin/openvpn --config /etc/openvpn/server/server.conf &

```

