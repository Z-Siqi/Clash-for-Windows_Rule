### 基本的模板
<details><summary>点击以查看模板</summary>

````
# 配置中的 # 符号是注释符号，你可以任意修改/删除有 # 符号的那一行
# The # symbol in the configuration is a comment symbol,
# You can arbitrarily modify/delete the line with the # sign.

port: 7890
socks-port: 7891
allow-lan: false
mode: Rule
log-level: info
external-controller: 127.0.0.1:9090

# 节点信息
proxies:
- name: "ss1"
  type: ss
  server: server
  port: 443
  cipher: chacha20-ietf-poly1305
  password: "password"
  # udp: true  

- name: "ss2"
  type: ss
  server: server
  port: 443
  cipher: AEAD_CHACHA20_POLY1305
  password: "password"
  plugin: obfs
  plugin-opts:
    mode: tls
    host: bing.com
# 节点群组
proxy-groups:
- name: "Proxy"
  type: select
  proxies:
  - "ss1"
  - "ss2"
- name: "Google"
  type: select
  proxies:
  - "ss1"
  - "ss2"
# 规则
rules:
# Google
- DOMAIN-SUFFIX,google.com,Google
- DOMAIN-KEYWORD,google,Google

# Proxy
- MATCH,Proxy
````
</details>

***

### 常用协议的格式

*请根据个人情况更改或删除某些选项*

**基本的说明**

`server`更改为服务器的`ip`或`link`

`ture/false`的选项更具根据个人情况更改

`port`根据服务端开放/设定的端口更改(例如`443`, `65535`之类的)

`"password"`更改为服务端设定的密码(例如`"123456789"`, 在vmess指的是`uuid`, 格式: `DC9D4747-CE52-F759-3A35-1EF62463D3E1`)

如果服务端的配置没有关于相应的选项(例如vmess的`servername: servername`)就可以选择直接删除整行

#### Shadowsocks 配置格式
````
- name: "name"
  type: ss
  server: server
  port: port
  cipher: aes-128-gcm
  password: "password"
  udp: true
````

#### V2ray (vmess) 配置格式
````
- name: "name"
  type: vmess
  server: server
  port: port
  uuid: uuid
  alterId: alterId
  cipher: auto
  udp: true
  tls: false
  skip-cert-verify: true
  servername: servername
````

#### ShadowsocksR 配置格式
````
- name: "name"
  type: ssr
  server: server
  port: port
  cipher: aes-128-gcm
  password: password
  udp: true
  obfs: plain
  protocol: origin
  # obfs-param: domain.tld
  # protocol-param: "#"
````

#### Trojan 配置格式
````
- name: "name"
  type: trojan
  server: server
  port: port
  password: "password"
  udp: true
  sni: example.com
  alpn:
    - h2
  skip-cert-verify: h2
````

#### Http 配置格式
````
- name: "name"
  type: http
  server: server
  port: port
  username: username
  password: "password"
  tls: true
  skip-cert-verify: true
````

***

### 自动选择节点的基本格式
<details><summary>点击以查看模板</summary>

````
port: 7890
socks-port: 7891
redir-port: 7892
allow-lan: false
mode: rule
hosts:
  services.googleapis.cn: 142.250.196.131
  www.google.cn: 142.250.196.131
  time.android.com: 203.107.6.88
  www.msn.cn: 127.0.0.1
log-level: info
clash-for-android:
  append-system-dns: false
external-controller: '127.0.0.1:9090'
secret: ''

proxies:
- name: "SERVER-1"
  type: vmess
  server: 123.123.123.001
  port: 443
  uuid: d364941a-6163-15ed-9b6a-0242ac100001
  cipher: auto
  alterId: 64
  udp: true
  skip-cert-verify: true

- name: "SERVER-2"
  server: 123.123.123.003
  port: 443
  cipher: aes-256-gcm
  type: ss
  password: "123456789"
  udp: true

- name: "SERVER-3"
  server: example.com
  port: 443
  type: trojan
  password: "123456789"
  sni: example.com
  skip-cert-verify: true
  udp: true

proxy-groups:
# groups
- name: "Proxy"
  type: select
  proxies:
  - "AutoChoose"
  - "SERVER-1"
  - "SERVER-2"
  - "SERVER-3"
  - 'DIRECT'
# Auto choose proxy
# 自动选择的配置内容可以存在多个(- name: "xxx" 不能重复)
- name: "AutoChoose"
  type: url-test
# url指的是自动ping的网站(用于确认延迟, 可以随意更改)
  url: http://www.github.com/
# interval指的是自动测试的间隔时间(秒)
  interval: 120
# proxies放入你想用于自动测试延迟的节点
  proxies:
  - "SERVER-1"
  - "SERVER-2"
  - "SERVER-3"
rules:

# Rules example
- DOMAIN-KEYWORD,google,Proxy
- DOMAIN-KEYWORD,googleads,REJECT
- DOMAIN-SUFFIX,doubleclick.net,REJECT
- DOMAIN-SUFFIX,baidu.com,DIRECT

````
</details>
