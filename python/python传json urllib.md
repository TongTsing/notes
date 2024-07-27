# **python传json urllib**

*万次阅读* *多人点赞*

2018-06-11 11:01:28

在爬取某网站的时候，碰到的一个问题，
在进行一个post请求时，postman 里面可以正常请求到数据，但是一模一样放到python里面就不行了，后面通过抓包发现了问题。
直接贴代码：

raw = {‘number’: ‘123456’}
print(raw)
data = parse.urlencode(raw).encode(‘utf-8’)
request = urllib.request.Request(collectUserCoin_url, headers=headers, data=data)
response = urlopen(request)
html = response.read().decode(‘utf-8’)

如上这种方式，通过抓包发现，在进行post请求时，请求将data转换成了“number=123456”这种对象格式进行发送的。但是后台接收到之后解析不了，返回了{“code”:-1,”msg”:”请求出错”,”data”:null}

于是乎，通过postman 请求，然后抓包观察到，postman里面发出的请求，请求体的格式为 {“number”: “123456”} 这种标准的json形式。问题找到了，解决方案如下：

raw = {‘number’: ‘123456’}
print(raw)
data = json.dumps(raw)
data = bytes(data, ‘utf8’)
request = urllib.request.Request(collectUserCoin_url, headers=headers, data=data)
response = urlopen(request)
html = response.read().decode(‘utf-8’)