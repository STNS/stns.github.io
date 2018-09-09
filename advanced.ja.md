---
layout: default
permalink: /ja/advanced
---

## アドバンスドガイド

### SSHログイン時にホームディレクトリを作成する
sshログインした際に、ユーザーのホームディレクトリは作成されません。`/etc/pam.d/sshd`に設定を行うことでホームディレクトリを自動で作成することが出来ます。

```
$ echo 'session    required     pam_mkhomedir.so skel=/etc/skel/ umask=0022' >> /etc/pam.d/sshd
```

### link_users機能を利用し、デプロイユーザーを追加する
Webサービスにおいて、アプリケーションをデプロイする際に、デプロイユーザーを作成し、デプロイする運用が多くあると思います。大体のケースにおいて、デプロイユーザーの`~/.ssh/authorized_keys`にデプロイするユーザーの公開鍵を羅列して運用することが多いでしょう。STNSの`link_users`機能を利用すると同様のことがシンプルに実現できます。

```toml
[users.deploy]
id = 1
group_id = 1
link_users = ["example1","example2"]

[users.example1]
id = 2
group_id = 1
keys = ["ssh-rsa aaa"]

[users.example2]
id = 3
group_id = 1
keys = ["ssh-rsa bbb"]
```

上記の定義を行うと、`deploy`というユーザー名で`exampl1`と`example2`がログイン可能となります。裏の動きとしては`deploy`というユーザー名でSSHログインを行う際に、example1とexample2の公開鍵である`ssh-rsa aaa`および`ssh-rsa bbb`を返却しています。

### link_groups機能を利用し、組織の階層構造を表現する
サーバのパーミッション制御を行う際に、部署単位で行いたい場合があります。例えばA課で読み取り書き込み権限を与え、A課が所属するB部では読み取り権限のみ与えると行ったケースです。STNSではこういったケースにも対応する事ができます。

```toml
[groups.department]
users = ["user1"]
link_groups = ["division"]

[groups.division]
users = ["user2"]

```

この例では`department`グループに`user1`が所属しており、`division`グループには`user2`が所属しています。また`department`には`link_groups`に`division`を定義しております。
これによって、departmentにはdivisionも所属しているという状態になり、`id`コマンドを発行すると下記のようになります。

```
$ id user2
uid=1001(user1) gid=1002(division) groups=1001(department),1002(division)
```

これによりuser2はdepartmentに所属するdivisionのユーザーということを表現できます。
