---
layout: default
permalink: /en/advanced
---

## Advanced Guide

### Create home directory for login user

If you want the system to create home directory for login user after login in via SSH, add the configuration below into `/etc/pam.d/sshd`:

```
$ echo 'session    required     pam_mkhomedir.so skel=/etc/skel/ umask=0022' >> /etc/pam.d/sshd
```

### Add deploy users using `link_users`

We often create a special user account whose privilege is restricted for a certain purpose that we deploy application to remote server by the user account. To realize such a way to deploy, we can add real user accounts' SSH public keys to the user account's `~/.ssh/authorized_keys`.

STNS allows us to easily bundle deploy users using the `link_users` feature.

```toml
[users.deploy]
id         = 1
group_id   = 1
link_users = ["example1","example2"]

[users.example1]
id         = 2
group_id   = 1
keys       = ["ssh-rsa aaa"]

[users.example2]
id         = 3
group_id   = 1
keys       = ["ssh-rsa bbb"]
```

With the configuration above, the users shown as `example1` and `example2` can login to a server as `deploy` user.

When the `deploy` user attempts to login to a server via SSH, STNS server returns to STNS client keys for both `example1` (`ssh-rsa aaa`) and `example2` (`ssh-rsa bbb`). That is, the users are linked.

### Express organizational structure using `link_groups`

It's a common requirement to control who can access to a server with respect to organizational structure. Imagine such a case like that we want to give read and write privilege to ones in a division, and, at the same time, we want to add read-only privilege to ones in a department to which the division belong.

STNS meets such a complicated requirement.

```toml
[groups.department]
users       = ["user1"]
link_groups = ["division"]

[groups.division]
users       = ["user2"]
```

In the configuration above, `user1` belongs to `department` group and `user2` belongs to `division` group. `link_group` is set to link `department` group to `division` group.

As a result:

* `department` group includes `division` group
* `user2` belongs to both `department` group and `division` group

You can confirm it by executing `id` command like below:

```
$ id user2
uid=1001(user1) gid=1002(division) groups=1001(department),1002(division)
```
