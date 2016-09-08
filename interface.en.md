---
layout: default
permalink: /en/interface
---

## Api Version 2

- EndPoint

|path|Description|
|---|---|
|/user/list|user list|
|/user/name/:user_name|find by user name|
|/user/id/:uid|find by user id|
|/group/list|group list|
|/group/name/:group_name|find by group name|
|/group/id/:gid|find by group id|
|/sudo/name/:sudo_user_name|find by sudo user name|
|/healthcheck|health check url|

- Metadata

|path|Description|
|---|---|
|api_version| api version|
|result|success only|
|min_id|please return the minimum Id in the All users, group|


## 1.user

- resposne detail

|Name|Required|Description|
|---|:---:|---|
|id|●|uid|
|password||login password by /etc/shadow format|
|directory||home directory|
|shell||default shell|
|gecos||general information about the account|
|keys||public keys|
|link_users||stns link users|

### 1.1 list request

- request

```
$ curl http://localhost:1104/v2/user/list
or
$ stns-query-wrapper /user/list
```

- response

```json
{
  "metadata": {
    "api_version": 2.1,
    "result": "success",
    "min_id": 1001
  },
  "items": {
    "example1": {
      "id": 1001,
      "password": "",
      "group_id": 0,
      "directory": "",
      "shell": "",
      "gecos": "",
      "keys": [
        "ssh-rsa xxxx"
      ],
      "link_users": null
    },
    "example2": {
      "id": 1002,
      "password": "",
      "group_id": 0,
      "directory": "",
      "shell": "",
      "gecos": "",
      "keys": [
        "ssh-rsa xxxx"
      ],
      "link_users": null
    }
  }
}
```


### 1.2 name/id request

- request

```
$ curl http://localhost:1104/v2/user/name/example1
or
$ stns-query-wrapper /user/name/example1
```

- response

```json
{
  "metadata": {
    "api_version": 2.1,
    "result": "success",
    "min_id": 1001
  },
  "items": {
    "example1": {
      "id": 1001,
      "password": "",
      "group_id": 0,
      "directory": "",
      "shell": "",
      "gecos": "",
      "keys": [
        "ssh-rsa xxxx"
      ],
      "link_users": null
    }
  }
}
```

## 2.group

- resposne detail

|Name|Required|Description|
|---|:---:|---|
|id|●|gid|
|users|●|users who belong to the group|
|link_groups||stns link groups|


### 2.1 list request

- request

```
$ curl http://localhost:1104/v2/group/list
or
$ stns-query-wrapper /group/list
```

- response

```json
{
  "metadata": {
    "api_version": 2.1,
    "result": "success",
    "min_id": 1001
  },
  "items": {
    "example1": {
      "id": 1001,
      "users": [
        "example1"
      ],
      "link_groups": null
    },
    "example2": {
      "id": 1002,
      "users": [
        "example2"
      ],
      "link_groups": null
    }
  }
}
```

### 2.2 name/id request

- request

```
$ curl http://localhost:1104/v2/group/id/1001
or
$ stns-query-wrapper /group/id/1001
```

- response

```json
{
  "metadata": {
    "api_version": 2.1,
    "result": "success",
    "min_id": 1001
  },
  "items": {
    "example1": {
      "id": 1001,
      "users": [
        "example1"
      ],
      "link_groups": null
    }
  }
}
```


## 3.sudoers
- resposne detail

|Name|Required|Description|
|---|:---:|---|
|password|●|sudo password by /etc/shadow format|

### 3.1 name request

sudoers query only name.

- request

```
$ curl http://localhost:1104/v2/sudo/name/example1
or
$ stns-query-wrapper /sudo/name/example1
```

- response

```json
{
  "metadata": {
    "api_version": 2.1,
    "result": "success",
    "min_id": 0
  },
  "items": {
    "example1": {
      "id": 0,
      "password": "$6$ZbcEUwqLWMcV7fr5$4krw.1ULrmZyto...",
      "group_id": 0,
      "directory": "",
      "shell": "",
      "gecos": "",
      "keys": null,
      "link_users": null
    }
  }
}
```
