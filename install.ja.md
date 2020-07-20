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
$ yum install stns-v2 libnss-stns-v2 cache-stnsd
```

## 設定ファイル

### サーバ
インストールが完了したらサーバの設定から行います。

* /etc/stns/server/stns.conf

```toml
port = 1104
include = "/etc/stns/conf.d/*"

[users.example]
id = 1001
group_id = 1001
keys = ["ssh-rsa XXXXX…"]

[groups.example]
id = 1001
users = ["example"]

```

1104ポートで起動、`/etc/stns/conf.d/*`配下の設定ファイルを追加で読み込むように設定する例です。部署やチームごとに設定ファイルを分離し、運用するのが良いでしょう。
例ではユーザーexample、グループexampleを定義しています。
設定を記述したらrestartしてください。

```
$ service stns restart
```

### クライアント

* /etc/stns/client/stns.conf


```toml
api_endpoint = "http://<server-ip>:1104/v1"
use_cached = true
```

設定としてはサーバのエンドポイントを定義しています。
`use_cached = true` を追記し、キャッシュ処理をcache-stnsdに移譲する場合(推奨)、cacheプロセスを再起動してください。

> 注意: libnss-stns-v3において `use_cached = true` がデフォルト値となります。

```
$ service cache-stnsd restart
```

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

以上でSTNSのインストールは完了です。また現状はSELinuxに対応していないため、正常に動かない場合はSELinuxを無効にしてお試しください。
