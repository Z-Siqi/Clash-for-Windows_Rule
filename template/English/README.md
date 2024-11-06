### The basic template
<details><summary>Click to view the template</summary>

````
# The # symbol in the configuration is a comment symbol,
# You can arbitrarily modify/delete the line with the # sign.

port: 7890
socks-port: 7891
allow-lan: false
mode: Rule
log-level: info
external-controller: 127.0.0.1:9090

# Proxy info
proxies:
- name: "ss1"
  type: ss
  server: example.server.lgbt
  port: 443
  cipher: chacha20-ietf-poly1305
  password: "password"
  # udp: true  

- name: "ss2"
  type: ss
  server: server.example
  port: 443
  cipher: AEAD_CHACHA20_POLY1305
  password: "password"
  plugin: obfs
  plugin-opts:
    mode: tls
    host: bing.com
# The group of proxy
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
# The proxy rules (Based on the group)
rules:
# Google
- DOMAIN-SUFFIX,google.com,Google
- DOMAIN-KEYWORD,google,Google

# Proxy
- MATCH,Proxy
````
</details>

***

### Common protocol formats

*Please change or delete some options according to your personal situation if needed*

**The Basic Instructions of The Proxy**

Edit `server: server` to your server's `ip` or `link` (eg. `server: 192.168.1.1` or `server: example.com`)

Anything about `ture`/`false`, please change it if you needed.

The `port` must be changed to the port opened on the server side (eg. `443`, `65535` etc.)

The `"password"` need change to the password which set on the server side (eg. `"123456789"`, If it's based on vmess, this will be `uuid`, format: `DC9D4747-CE52-F759-3A35-1EF62463D3E1`)

Please delete the line when the proxy not work if the sever side NOT config anything that in the proxy format (Such as `servername: servername` which from the vmess)

Important note: The rule of Clash is applied in order of precedence (top to bottom)

#### Shadowsocks config format
````
- name: "name"
  type: ss
  server: server
  port: port
  cipher: aes-128-gcm
  password: "password"
  udp: true
````

#### V2ray (vmess) config format
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

#### ShadowsocksR config format
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

#### Trojan config format
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

#### Http config format
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

### Automatic proxies selection basic format
<details><summary>Click to view the template</summary>

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
# Auto choose proxy (- name: "xxx" cannot repeatedly)
- name: "AutoChoose"
  type: url-test
# url means the web that used to ping (For check the delay, edit if you want)
  url: http://www.github.com/
# interval refers to the interval time of automatic test (seconds)
  interval: 120
# Put proxies to here when you want automatic test the proxy
  proxies:
  - "SERVER-1"
  - "SERVER-2"
  - "SERVER-3"
rules:

# Rules example
- DOMAIN-KEYWORD,google,Proxy
- DOMAIN-KEYWORD,microsoft,DIRECT
- DOMAIN-SUFFIX,doubleclick.net,REJECT
- DOMAIN-SUFFIX,baidu.com,REJECT
- MATCH,Proxy
````
</details>

### The basic format of load balancing
> Same `eTLD+1` request will calling to same proxy.
<details><summary>Click to view the template</summary>

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
  - "LoadBalance"
  - "SERVER-1"
  - "SERVER-2"
  - "SERVER-3"
  - 'DIRECT'

# LoadBalance
# load balancing configs info can be multiple (But this cannot be multiple: - name: "xxx" )
- name: "LoadBalance"
  type: load-balance
# The url means the web that used to ping (For check the delay, edit or change if you want)
  url: http://www.gstatic.com/generate_204
# interval refers to the interval time of automatic test (seconds)
  interval: 120
# Put proxies to here when you want the proxy load balancing
  proxies:
  - "SERVER-1"
  - "SERVER-2"
  - "SERVER-3"
rules:

# Rules example
- DOMAIN-SUFFIX,minecraft.net,LoadBalance
- DOMAIN-KEYWORD,google,Proxy
- DOMAIN-KEYWORD,microsoft,DIRECT
- DOMAIN-SUFFIX,doubleclick.net,REJECT
- DOMAIN-SUFFIX,baidu.com,REJECT
- MATCH,Proxy
````
</details>

****

### Reference

~https://dreamacro.github.io/clash/configuration/introduction.html~ 

The source link is 404，restore to the last archived by Internet Archive:
[https://web.archive.org/web/20230523202834/https://dreamacro.github.io/clash/configuration/introduction.html](https://web.archive.org/web/20230523202834/https://dreamacro.github.io/clash/configuration/introduction.html)
