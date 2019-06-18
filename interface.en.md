---
layout: default
permalink: /en/interface
---

## Api Version 1

- EndPoint

|path|Description|
|---|---|
|/users | user list|
|/groups |group list|

## 1.user

- response detail

|Name|Required|Description|
|---|:---:|---|
|id|●|uid|
|password||login password by /etc/shadow format|
|directory||home directory|
|shell||default shell|
|gecos||general information about the account|
|keys||public keys|

### 1.1 list request

- request

```
$ curl http://localhost:1104/v1/users
```

- response

```json
[{
  "name": "example1",
    "id": 1001,
    "password": "",
    "group_id": 0,
    "directory": "",
    "shell": "",
    "gecos": "",
    "keys": [
      "ssh-rsa xxxx"
    ],
},
{
  "name": "example2",
  "id": 1002,
  "password": "",
  "group_id": 0,
  "directory": "",
  "shell": "",
  "gecos": "",
  "keys": [
    "ssh-rsa xxxx"
  ]
}]
```


### 1.2 name/id request

- request

```
$ curl http://localhost:1104/v1/users?name=example1
```

- response

```json
[{
  "name": "example1",
    "id": 1001,
    "password": "",
    "group_id": 0,
    "directory": "",
    "shell": "",
    "gecos": "",
    "keys": [
      "ssh-rsa xxxx"
    ],
}]

```

## 2.group

- response detail

|Name|Required|Description|
|---|:---:|---|
|id|●|gid|
|users|●|users who belong to the group|


### 2.1 list request

- request

```
$ curl http://localhost:1104/v1/groups
```

- response

```json

[{
  "name": "example1",
    "id": 1001,
    "users": [
      "example1"
    ],
},
{
  "name": "example2",
  "id": 1002,
  "users": [
    "example2"
  ],
}]

```

### 2.2 name/id request

- request

```
$ curl http://localhost:1104/v1/groups?id=1001
```

- response

```json

[{
  "name": "example1",
    "id": 1001,
    "users": [
      "example1"
    ],
}]

```
