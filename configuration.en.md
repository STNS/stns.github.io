---
layout: default
permalink: /en/configuration
---

## Usage

```
$ ssh pyama@example.jp
$ id pyama
uid=1001(pyama) gid=1001(pyama) groups=1001(pyama)
```

## config

* /etc/stns/stns.conf

```toml
port = 1104
include = "/etc/stns/conf.d/*"
salt_enable = false
stretching_number = 0
hash_type = "sha256"

# support basic auth
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

[sudoers.example]
password = "9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08"
hash_type = "sha256"
```

### General

|Name|Description|
|---|---|
|port|listen port|
|include|include config directory|
|user| basic authentication user|
|password| basic authentication password|
|salt_enable| To generate a salt of the password from the user name |
|stretching_number|Stretching number of password|
|hash_type| password hash algorithm (sha256,sha512) |

### Users

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
|hash_type| hash algorithm (sha256,sha512) Users > General|

#### link_users

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

### Groups

|Name|Description|
|---|---|
|id(※)| unique group id|
|users|user name of the members|
|link_groups|merge from belong to the other group users|

#### link_groups
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

### Sudoers

|Name|Description|
|---|---|
|password(※)| password token|
|hash_type| hash algorithm default sha256(sha256,sha512) |

※: required parameter

