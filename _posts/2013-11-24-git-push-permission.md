---
layout: post
title: "gitでpush可能なユーザを制限する"
keywords: "git,push制限"
category: Environment
published: true
tags: [開発環境,git]
---

![キャッチ](/images/2013-11-24-github.png)

gitを使った開発において、masterやdevelopブランチにはpush禁止とし、開発者は各々トピックブランチを生成してからのpull requestを原則とする運用方式があります。
その場合、 **特定のブランチへのpushを制限する** 必要があります。
例えば gitlab でも大枠の制限は設定で可能ですが、細かい制御についてはフックスクリプトを利用する形になります。

ここでは **push可能なユーザを制限するフックスクリプト** について説明します。

## 1.git接続時の環境変数設定

フックスクリプト上でユーザを扱えるように、git接続時に環境変数を設定します。

- sshの設定でPermitUserEnvironmentを許可してデーモンを再起動します。

```
vi /etc/ssh/sshd_config
PermitUserEnvironment yes
/etc/init.d/sshd reload  
```

- authorized_keysの各々に、環境変数を設定します。ここではREMOTE_USERを使います。

```
vi /home/git/.ssh/authorized_keys
各行のcommandの次の行に
,environment="REMOTE_USER=dosancole",
で環境変数を追加。REMOTE_USERは各行に合わせて変更します。
```
## 2.フックスクリプトの作成

対象のgitリポジトリにフックスクリプトを作成します。
※gitlabではupdateフックスクリプトとしてrubyのものが組み込まれてましたので、今回rubyで作成しました。

- 対象のgitリポジトリの hooks/update を作成（修正）します。（gitlabの場合、既存ファイルの先頭に追記）

```ruby
#!/usr/bin/env ruby

refname = ARGV[0]
remoteUser = ENV['REMOTE_USER']
repo_path = `pwd`

# permit conf
permit_conf = {
  'master' => [],
  'develop\/v1[.]1' => [ 'dosancole', 'hogehoge' ],
  'develop\/v1[.]2' => [ 'dosancole' ]
}

# permission to push
permit_conf.each{ |key,value|
  if refname =~ /#{key}/
    if ! value.include?( remoteUser )
      p "You can't push for #{refname} #{remoteUser}, please contact to administrator."
      exit(1)
    end
  end
}
```

- hooks/update には実行権限を与えてます。

## 3.フックスクリプトの説明

permit_confが、ブランチ名（正規表現）に対してpushを許可するユーザを並べる設定になっています。

```ruby
permit_conf = {
  'master' => [],
  'develop\/v1[.]1' => [ 'dosancole', 'hogehoge' ],
  'develop\/v1[.]2' => [ 'dosancole' ]
}
```

この場合、masterブランチには誰もpushできず、develop/v1.1ブランチにはdosancoleとhogehogeが、develop/v1.2にはdosancoleがpush可能となります。
それ以外のユーザはpull requestを出す運用になります。

## 参考

#### http://kokukuma.blogspot.jp/2012/11/git-masterpush.html
#### http://ngyuki.hatenablog.com/entry/20120905/p1
