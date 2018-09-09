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

# basic auth
[basic_auth]
user = "basic_user"
password = "basic_password"

# token auth
[token_auth]
tokens = ["xxxxxxx"]

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
```

#### General

|Name|Description|Default|
|---|---|---|
|port|listen port| 1104|
|include|include config directory| -|
|basic_auth - user| basic authentication user| -|
|basic_auth - password| basic authentication password|-|
|token_auth - tokens| token authentication tokens|-|

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


### client
- /etc/stns/client/stns.conf

```toml
api_endpoint = "http://api01.example.com/v1"
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
```

#### General

|Name|Description|Default|
|---|---|---|
|api_endpoint|api endpoints|http://localhost:1104/v1|
|request_timeout|http request timeout in wrapper command|10|
|request_retry|http request of retries|3|
|request_locktime|request lock when after request timeout|60|
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
