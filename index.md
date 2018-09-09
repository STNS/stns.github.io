---
layout: default
---

[STNS](https://github.com/STNS/STNS) allows you to easily manage Linux users with simple TOML-based configuration. It consists of server and client implementation, which requires only a few steps to install. Moreover, you can use it with existing user management systems such as LDAP.

<iframe src="https://ghbtns.com/github-btn.html?user=STNS&amp;repo=STNS&amp;type=watch&amp;count=true&amp;size=large" allowtransparency="true" frameborder="0" scrolling="0" width="170" height="30"></iframe><br/>

## Documents

1. Installation Guide: [English](/en/install) / [日本語](/ja/install)
2. Configuration: [English](/en/configuration)
3. Advanced Guide: [English](/en/advanced) / [日本語](/ja/advanced)
2. Interface Guide: [English](/en/interface)

## Architecture

![STNS Architecture](/images/stns-architecture.png)

**STNS server** is a simple Linux middleware which serves as an HTTP server and returns user information as JSON-formatted text or SSH public key for given user with respect to retrieval requests. You can define user information and even organizational structure in TOML-formatted configuration file.

**STNS client** consists of several programs:

* libnss-stns: an NSS module to resolve user name by STNS

## Comparison to Other Solutions

### LDAP

LDAP is a well-known protocol to solve the same issue which STNS faces and there has been a widely-used implementation.

We, however, think LDAP can be too versatile and complicated for us when we just want to control who to be able to login to servers via SSH. It's obviously overmuch to use LDAP in such a case.

STNS has necessary and sufficient features to meet such a requirement.

## Author

[pyama86](https://github.com/pyama86)

### License

[MIT License](https://github.com/STNS/STNS/blob/master/LICENSE)
