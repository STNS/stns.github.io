---
layout: default
permalink: /en/install
---

## Installation Guide

We provides both YUM and APT repositories.

You can install YUM/APT repository by executing the command below. Although you must want to install STNS server and client into separate hosts, we here install them into the same host for the sake of simplicity of explanation.

### Install YUM Repository

```
$ curl -fsSL https://repo.stns.jp/scripts/yum-repo.sh | sh
```

### Install APT Repository

```
$ curl -fsSL https://repo.stns.jp/scripts/apt-repo.sh | sh
```

### Install STNS Server and Client

Although we use commands for RHEL in what follows, the equivalent commands will work also for Debian family.

You can install STNS server by installing the `stns-v2` package. The STNS client consists of packages: `libnss-stns-v2`.

```
$ yum install stns-v2 libnss-stns-v2 cache-stnsd
```

## Configuration

### STNS Server

After successfully installing packages, let's configure STNS server.

`/etc/stns/server/stns.conf`:

```toml
port     = 1104
include  = "/etc/stns/conf.d/*"

[users.example]
id       = 1001
group_id = 1001
keys     = ["ssh-rsa XXXXX…"]

[groups.example]
id       = 1001
users    = ["example"]
```

This configuration means that the STNS server:

* Listens to 1104 port.
* Includes additional configurations placed under `/etc/stns/conf.d`.
* Defines example user and group.

We encourage you to set configurations for each teams into separate files.

Reload the server right after modifying the file to activate the new configuration.

```
$ service stns restart
```

### STNS Client
**Firstly**, configure STNS client.

`/etc/stns/client/stns.conf`:

```toml
api_endpoint     = "http://<server-ip>:1104/v1"
use_cached = true
```

If you append `use_cached = true` and to delegate cache function to cache-stnsd, should restart cache-stnsd.


> Notice: `use_cached = true` is default value when libnss-stns-v3.

```
$ service cache-stnsd restart
```

**Secondly**, configure the name resolution order like below.

`/etc/nsswitch.conf`:

```
passwd:     files stns
shadow:     files stns
group:      files stns
```

Add stns into `nsswitch.conf` to enable name resolution using STNS. To use LDAP concurrently, you can configure like: `passwd: files stns ldap`.


**Lastly**, configure sshd to enable SSH login using STNS.

`/etc/sshd/sshd_config`:

```
PubkeyAuthentication yes
AuthorizedKeysCommand /usr/lib/stns/stns-key-wrapper
AuthorizedKeysCommandUser root
```

This configuration means that tha SSH server:

* Activates SSH Public key authentication.
* Use `/usr/lib/stns/stns-key-wrapper` to retrieve the public key for the login user.

Reload sshd right after the modifying the configuration file.

```
service sshd restart
```

Installation of STNS has been finally completed!
In addition, since SELinux is not supported at present, if you do not operate properly please disable SELinux and try.
