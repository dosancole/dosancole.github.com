---
layout: post
title: "Amazon EC2にAlminiumをインストールする"
description: ""
category: Environment
published: true
tags: [開発環境,EC2,Alminium,redmine]
---

![キャッチ](/images/2013-10-27-alminium-ec2.jpg)

[Alminium](http://alminium.github.io/alminium/)は簡単にredmine+αを構築できるパッケージです。
特にかんばんを利用したい方、gitとの連携を考えている方には、プラグイン含め一式構築できるのでおススメです。

今回、Amazon EC2にインストールする手順を残しておきます。

# 1.EC2のインスタンスをラウンチする

- EC2のインスタンスを作成し、ssh接続。
- 初期設定など

```
sudo su -

yum update -y
yum upgrade -y

cp /usr/share/zoneinfo/Japan /etc/localtime
vi /etc/sysconfig/i18n
// でLANGを"ja_JP.UTF-8に変更

vi /etc/ssh/sshd_config
// PermitRootLogin no に編集。
/etc/init.d/sshd reload
```

# 2.インストール準備

- git等のインストール

```
yum install -y git
yum install -y \ gcc-c++ \ git-core \ libffi-devel \ libicu-devel \ libxml2-devel \ libxslt-devel libyaml-devel \ make \ mysql \ mysql-devel \ patch \ perl-Time-HiRes \ postfix \ readline-devel \ zlib-devel  
yum install -y libxml2 libxml2-devel libxslt libxslt-devel libicu libicu-devel
yum install -y redis --enablerepo=epel
```

- ruby2.0をインストール（2でないと失敗する）

```
yum remove -y ruby
bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
// PATH反映のためログインしなおし。 /etc/profile.d/... の内容
rvm install ruby 2.0.0
```

- これでruby-2.0.0-p247がインストールされた。
- 一応下記も入れておく。

```
gem install bundler --no-ri --no-rdoc
gem install charlock_holmes --version '0.6.9.4'
```

# 3.Alminiumのインストール

- 適切なフォルダにおいて、公開されている EC2 用のスクリプトを実行する（手打ち）
  - https://github.com/angelndxp/AWS-ALMinium/wiki/ALMinium_EC2StandAlone_https
  - メールは Nを押下する必要あった。wikiの方はjenkinsの漏れがあるので注意のこと。

- 最後のhttpd startが失敗するが、 /etc/httpd/conf.d/redmine.conf を以下修正すればOKである。

```
LoadModule passenger_module /usr/local/rvm/gems/ruby-2.0.0-p247/gems/passenger-4.0.21/buildout/apache2/mod_passenger.so
PassengerRoot /usr/local/rvm/gems/ruby-2.0.0-p247/gems/passenger-4.0.21 
PassengerRuby /usr/local/rvm/rubies/ruby-2.0.0-p247/bin/ruby 
```

# 4.Alminiumの動作確認

- https://xx.xx.xx.xx でredmineが。
- https://xx.xx.xx.xx/jenkins で jenkinsが入る
- 管理者は admin/admin

# 5.Alminiumの初期設定

- adminユーザをロックしておく。


# 参考

http://se-suganuma.blogspot.jp/2013/04/amazon-ec2alminiumredmine.html

https://github.com/alminium/alminium
