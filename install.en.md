---
layout: default
permalink: /en/install
---

## Installation Guide

We provides both YUM and APT repositories.

You can install YUM/APT repository by executing the command below. Although you must want to install SNTS server and client into separate hosts, we here install them into the same host for the sake of simplicity of explanation.

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
$ yum install stns-v2 libnss-stns-v2
```

## Configuration

### STNS Server

After successfully installing packages, let's configure STNS server.

`/etc/stns/server/stns.conf`:

```toml
port     = 1104
include  = "/etc/stns/conf.d/*"

user     = "test_user"
password = "test_password"

[users.example]
id       = 1001
group_id = 1001
keys     = ["ssh-rsa XXXXX…"]

[groups.example]
id       = 1001
users    = ["example"]
```

This configuration means that the STNS server:

* Utilizes basic authentication to restrict requests.
* Listens to 1104 port.
* Includes additional configurations placed under `/etc/stns/conf.d`.
* Defines example user and group.

We encourage you to set configurations for each teams into separate files.

Reload the server right after modifying the file to activate the new configuration.

```
$ service stns reload
```

### STNS Client
**Firstly**, configure STNS client.

`/etc/stns/client/stns.conf`:

```toml
api_endpoint     = "http://<server-ip>:1104/v1"

user              = "test_user"
password          = "test_password"

chain_ssh_wrapper = "/usr/libexec/openssh/ssh-ldap-wrapper"

ssl_verify        = true

request_timeout = 3

```

This file configures the location of SNTS server, and the combination of id and password for basic authentication.

You can set a script path by `chain_ssh_wrapper` to retrieve SSH public key from other place except for STNS server. STNS client executes the script with a user name as an argument.

`ssl_verify` tells if the client must verify or not the TLS certificate in the negotiation process with STNS server. If `false` is set, the client ignores the verification error of TLS certificate.

**Secondly**, configure the name resolution order like below.

`/etc/nsswitch.conf`:

```
passwd:     files stns
shadow:     files stns
group:      files stns
```

Add snts into `nsswitch.conf` to enable name resolution using STNS. To use LDAP concurrently, you can configure like: `passwd: files stns ldap`.

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

