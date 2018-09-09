---
layout: default
permalink: /ja/install
---

## インストールガイド
rhel,debian系共にリポジトリを提供しています。

### redhat/centos
```
$ curl -fsSL https://repo.stns.jp/scripts/yum-repo.sh | sh
```

### debian/ubuntu
```
$ curl -fsSL https://repo.stns.jp/scripts/apt-repo.sh | sh
```

サーバとクライントをインストールします。サーバは通常は専用のホストを設けて運用すると思いますが、ドキュメントの便宜上同じホストと言う前提で記載しています。
また本手順はrhel系のコマンドを利用しますが、debian系でも同等の作業可能です。

```
$ yum install stns-v2 libnss-stns-v2
```

## 設定ファイル

### サーバ
インストールが完了したらサーバの設定から行います。

* /etc/stns/server/stns.conf

```toml
port = 1104
include = "/etc/stns/conf.d/*"

user = "test_user"
password = "test_password"

[users.example]
id = 1001
group_id = 1001
keys = ["ssh-rsa XXXXX…"]

[groups.example]
id = 1001
users = ["example"]

```

ベーシック認証を利用し、1104ポートで起動、`/etc/stns/conf.d/*`配下の設定ファイルを追加で読み込むように設定する例です。部署やチームごとに設定ファイルを分離し、運用するのが良いでしょう。
例ではユーザーexample、グループexampleを定義しています。
設定を記述したらreloadしてください。

```
$ service stns reload
```

### クライアント

* /etc/stns/client/stns.conf


```toml
api_endpoint = "http://<server-ip>:1104/v1"

user = "test_user"
password = "test_password"

chain_ssh_wrapper = "/usr/libexec/openssh/ssh-ldap-wrapper"

ssl_verify = true

request_timeout = 3
```

設定としてはサーバのエンドポイント、ベーシック認証のID、パスワードを定義しています。`chain_ssh_wrapper`についてはSSHログイン時の公開鍵を取得する際にSTNSに加えて取得先がある場合に取得コマンドを定義します。定義されたコマンドに`ユーザー名`を引数に渡して取得を試みます。
`ssl_verify`についてはSTNSサーバをnginxなどと組み合わせてSSL対応した際に証明証の照合エラーを無視するか否かの設定です。`false`に設定した場合に証明証のエラーを無視します。

* /etc/nsswitch.conf

```
passwd:     files stns
shadow:     files stns
group:      files stns
```

nsswitch.confにstnsを追加し、stns経由での名前解決を有効にします。ldapを利用している場合は`passwd:     files stns ldap`のように記載することで併用可能です。


最後にSSHログインを可能にするため、sshdの設定を行います。

* /etc/sshd/sshd_config

```
PubkeyAuthentication yes
AuthorizedKeysCommand /usr/lib/stns/stns-key-wrapper
AuthorizedKeysCommandUser root
```

公開鍵認証を許可し、公開鍵取得コマンドに`/usr/lib/stns/stns-key-wrapper`を設定してください。設定後sshdを再起動しましょう。

```
service sshd restart
```

以上でSTNSのインストールは完了です。
