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
user = "basic_user"
password = "basic_password"

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

|Name|Description|
|---|---|
|port|listen port|
|include|include config directory|
|user| basic authentication user|
|password| basic authentication password|

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
retry_request = 1
# basic auth
user = "basic_user"
password = "basic_password"

ssl_verify = true
query_wrapper = "/usr/local/bin/stns-query-wrapper"
chain_ssh_wrapper = "/usr/libexec/openssh/ssh-ldap-wrapper"

```

#### General

|Name|Description|
|---|---|
|api_endpoint|api endpoints|
|request_timeout|http request timeout in wrapper command|
|retry_request|http request of retries|
|user| basic authentication user|
|password| basic authentication password|
|ssl_verify| verify certs|
|query_wrapper| Please use it when acquiring information with arbitrary script|
|chain_ssh_wrapperr| Please use to obtain public key from other than stns|
