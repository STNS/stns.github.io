---
layout: default
permalink: /en/configuration
---

## config

### server
- /etc/stns/server/stns.conf

```toml
port = 1104
include = "/etc/stns/conf.d/*"
module_path = "/usr/local/stns/modules.d"
load_module = "mod_stns_etcd.so"
# basic auth
[basic_auth]
user = "basic_user"
password = "basic_password"

# token auth
[token_auth]
tokens = ["xxxxxxx"]

# tls encrypt
[tls]
# ca = "/etc/stns/keys/ca.pem"     # using only client authentication
cert = "/etc/stns/keys/server.crt"
key  = "/etc/stns/keys/server.key"

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

[modules.etcd]
endpoints = ["http://127.0.0.1:2379"]
```

#### General

|Name|Description|Default|
|---|---|---|
|port|listen port| 1104|
|include|include config directory| -|
|module_path|module include path| /usr/local/stns/modules.d|
|load_module|include module name| -|
|basic_auth - user| basic authentication user(env:STNS_BASIC_AUTH_USER)| -|
|basic_auth - password| basic authentication password(env:STNS_BASIC_AUTH_PASSWORD)|-|
|token_auth - tokens| token authentication tokens(env:STNS_AUTH_TOKEN separetor is `,`)|-|
|tls - ca| ca public key(use only client authentication)|-|
|tls - cert| server certificate|-|
|tls - key| server private key|-|
|ldap - base_dn | ldap server base dn|dc=stns,dc=local|
|redis - host | redis host name |-|
|redis - user | redis username |-|
|redis - password| redis password(env:STNS_REDIS_PASSWORD)|-|
|redis - ttl| redis ttl|-|
|redis - db| redis db id|-|

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
$ /usr/lib/stns/stns-key-wrapper example1
ssh-rsa aaa
ssh-rsa bbb
$ /usr/lib/stns/stns-key-wrapper example2
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
$ curl http://stns.example.com/v1/groups?name=department
[{
  …
  "users": ["user1", "user2"]
}]

$ curl http://stns.example.com/v1/groups?name=division
[{
  …
  "users": ["user2"]
}]
```

#### Modules
##### Etcd

|Name|Description|Type|
|---|---|---|
|endpoints| etcd urls| strings|
|user|etcd user| string|
|password|etcd password(env:STNS_ETCD_PASSWORD)| string |
|sync|sync config from toml file(exclude user password)| bool|

##### DynamoDB

|Name|Description||Type|
|---|---||---|
|read_capacity_units| table read capacity units|int|
|write_capacity_units| table write capacity units|int|
|user_table_name| user table name|string|
|group_table_name| group table name|string|
|sync|sync config from toml file(exclude user password)| bool|

### client
- /etc/stns/client/stns.conf

```toml
api_endpoint = "http://api01.example.com/v1"
http_proxy = "http://localhost:8080"
request_timeout = 3
request_retry = 1
request_locktime = 600
ssl_verify = true

# basic auth
user = "basic_user"
password = "basic_password"

# token auth
auth_token = "token"

query_wrapper = "/usr/local/bin/stns-query-wrapper"
chain_ssh_wrapper = "/usr/libexec/openssh/ssh-ldap-wrapper"

cache = true
cache_dir = "/var/cache/stns/"
cache_ttl = 600
negative_cache_ttl = 600

uid_shift = 2000
gid_shift = 2000

# tls client authentication
[tls]
cert = "/etc/stns/keys/client.crt"
key  = "/etc/stns/keys/client.key"
```

#### General

|Name|Description|Default|
|---|---|---|
|api_endpoint|api endpoints|http://localhost:1104/v1|
|request_timeout|http request timeout|10|
|request_retry|http request of retries|3|
|request_locktime|request lock when after request timeout|60|
|http_proxy|use http proxy|-|
|ssl_verify| verify certs| true|
|user| basic authentication user|-|
|password| basic authentication password|-|
|auth_token| token authentication token|-|
|query_wrapper| use it when acquiring information with arbitrary script| -|
|chain_ssh_wrapperr| use to obtain public key from other than stns|-|
|cache| use requst cache|true|
|cache_dir|save cache directory | /var/cache/stns|
|cache_ttl|cache ttl | 600|
|negative_cache_ttl|cache ttl when resource notfound |60|
|uid_shift|user id shift from stns response user id |0|
|gid_shift|group id shift from stns response group id |0|
|tls - cert| client certificate|-|
|tls - key| client private key|-|
