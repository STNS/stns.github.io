---
layout: default
permalink: /en/configuration
---

## config

### /etc/stns/stns.conf

```toml
port = 1104
include = "/etc/stns/conf.d/*"

# basic auth
user = "basic_user"
password = "basic_password"

# tls encrypt
# tls_ca   = "keys/ca.pem"      # using only client authentication
tls_cert = "keys/server.crt"
tls_key  = "keys/server.key"

[users.example]
id = 1001
group_id = 1001
directory = "/home/example" # default:/home/:user_name
shell = "/bin/bash" # default:/bin/bash
keys = ["ssh-rsa XXXXX…"]
link_users = ["foo"]

[groups.example]
id = 1001
users = ["example"]

[sudoers.example]
password = "$6$ZbcEUwqLWMcV7fr5$4krw.1ULrmZytoMwuV5.pIqjEo1Ngc9K15zYQ.KGZa.8T4EmCd1RfUM6rfviIpAwncNpnF9Yjyc0.30c2dN1J/"
```

#### General

|Name|Description|
|---|---|
|port|listen port|
|include|include config directory|
|user| basic authentication user|
|password| basic authentication password|
|tls_ca| ca public key(use only client authentication)|
|tls_cert|server certificate|
|tls_key|server private key|

#### Users

|Name|Description|
|---|---|
|id(※)| unique user id|
|group_id(※)|id of the group they belong|
|directory|home directory path|
|shell|default shell path|
|gecos|description|
|keys|public key list|
|link_users|merge public key from the specified user|
|password| password token|

##### link_users

link_users params is merge public key from the specified user

```toml
[users.example1]
keys = ["ssh-rsa aaa"]
link_users = ["example2"] ←

[users.example2]
keys = ["ssh-rsa bbb"]
```
```
$ /user/local/bin/stns-key-wrapper example1
ssh-rsa aaa
ssh-rsa bbb
$ /user/local/bin/stns-key-wrapper example2
ssh-rsa bbb
```

#### Groups

|Name|Description|
|---|---|
|id(※)| unique group id|
|users|user name of the members|
|link_groups|merge from belong to the other group users|

##### link_groups
It can be used to represent the organizational structure

```toml
[groups.department]
users = ["user1"]
link_groups = ["division"]

[groups.division]
users = ["user2"]

```

```sh
$ /user/local/bin/stns-query-wrapper /group/name/department
{
  …
  "users": ["user1", "user2"]
}
$ /user/local/bin/stns-query-wrapper /group/name/division
{
  …
  "users": ["user2"]
}
```

#### Sudoers

|Name|Description|
|---|---|
|password(※)| password token|

※: required parameter


### /etc/stns/libnss-stns.conf

```toml
api_end_point = ["http://api01.example.com","http://api02.example.com"]
request_timeout = 3
retry_request = 1
# basic auth
user = "basic_user"
password = "basic_password"

# tls client authentication
tls_ca   = "keys/ca.pem"
tls_cert = "keys/client.crt"
tls_key  = "keys/client.key"

ssl_verify = true
wrapper_path = "/usr/local/bin/stns-query-wrapper"
chain_ssh_wrapper = "/usr/libexec/openssh/ssh-ldap-wrapper"
http_proxy = "http://proxy.com:8080"

[request_header]
x-api-key = "xxxxxxx"
```

#### General

|Name|Description|
|---|---|
|api_end_point|api endpoints|
|request_timeout|http request timeout in wrapper command|
|retry_request|http request of retries|
|user| basic authentication user|
|password| basic authentication password|
|ssl_verify| verify certs|
|tls_ca| ca public key|
|tls_cert|client certificate|
|tls_key|client private key|
|http_proxy|http proxy server|
|request_hearder| http request header|

