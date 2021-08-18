<!-- omit in toc -->
# Vagrantでの環境構築手順書

## 1. 作成者
BW) 藤井 栄輝（研修生)

## 2. 改訂履歴

- 1.0:
  - 作成日時: 2021-08-18
  - 更新内容: 初版作成

***
## 3. このドキュメントの目次

- [1. 作成者](#1-作成者)
- [2. 改訂履歴](#2-改訂履歴)
- [3. このドキュメントの目次](#3-このドキュメントの目次)
- [4. このドキュメントの目的・概要](#4-このドキュメントの目的概要)
- [5. 前提条件](#5-前提条件)
- [6. 作業完了条件](#6-作業完了条件)
- [7. 作業者](#7-作業者)
- [8. チェッカー](#8-チェッカー)
- [9. 作業場所](#9-作業場所)
- [10. 作業端末](#10-作業端末)
- [11. 設定したバージョンは以下の通り](#11-設定したバージョンは以下の通り)
- [12. 作業手順書](#12-作業手順書)
  - [1. VirtualBoxのインストール](#1-virtualboxのインストール)
  - [2. Vagrantのインストール](#2-vagrantのインストール)
  - [3. CentOS 7 ダウンロード](#3-centos-7-ダウンロード)
    - [Vagrantfileを作成](#vagrantfileを作成)
    - [Vagrantfileの編集](#vagrantfileの編集)
  - [4. PHPのインストール](#4-phpのインストール)
    - [composerのインストール](#composerのインストール)
  - [5. MySQLのインストール](#5-mysqlのインストール)
    - [MySQLの起動](#mysqlの起動)
    - [パスワードの確認](#パスワードの確認)
    - [MySQLへログイン](#mysqlへログイン)
    - [パスワードのリセット](#パスワードのリセット)
    - [データベースを作成](#データベースを作成)
  - [6. Laravelのインストール](#6-laravelのインストール)
    - [/vagrant直下で以下のコマンドを実行](#vagrant直下で以下のコマンドを実行)
    - [laravel_appディレクトリ下の .env ファイルの内容を編集](#laravel_appディレクトリ下の-env-ファイルの内容を編集)
  - [7. Nginxのインストール](#7-nginxのインストール)
    - [Nginxインストール](#nginxインストール)
    - [Nginxの設定ファイルを編集](#nginxの設定ファイルを編集)
    - [`nginx` というユーザーでもログファイルへの書き込みができる権限を付与](#nginx-というユーザーでもログファイルへの書き込みができる権限を付与)
    - [設定を反映するために再起動します。](#設定を反映するために再起動します)
    - [Nginxとfpmを起動してステータスでNginxが起動していることを確認する](#nginxとfpmを起動してステータスでnginxが起動していることを確認する)
  - [8. Laravel6.0 ログイン機能実装](#8-laravel60-ログイン機能実装)
    - [laravel/uiをインストールする](#laraveluiをインストールする)
    - [artisanコマンドを実行する](#artisanコマンドを実行する)
    - [以下のコマンドでDBにテーブルを作成](#以下のコマンドでdbにテーブルを作成)
- [13. どういう流れで環境構築したか](#13-どういう流れで環境構築したか)
- [14. 環境構築の所感](#14-環境構築の所感)
- [15. 参考サイト](#15-参考サイト)
***
## 4. このドキュメントの目的・概要

「自分で仮想環境(サーバ)を構築し、マークダウン形式で構築手順書を作成後プルリクエストを作成し提出する」
ための手順書。  
スタートは vagrant用ディレクトリの作成。
ゴールは インストールしたLaravelプロジェクトにログイン機能を実装して実際にログインする。

なお、本作業では基本的な構成は [GIZ TECH](https://giztech.gizumo-inc.work/)過去セクションで構築した環境と同じになっている。

## 5. 前提条件
```
1. Vagrant を使用すること
2. ipは 192.168.33.19 とすること
3. OSは CentOS7
4. DBは MySQL5.7
5. Webサーバは Nginx
6. PHPのバージョンは 7.3
7. Laravelのバージョンは 6.0
```

  - 不明点があれば適宜、[GIZ TECH カリキュラム](https://giztech.gizumo-inc.work/lesson/18)を参照

- 他の人が手順書を見て全く同じ環境を構築できること。
- 過去のセクションではPHPのバージョン7.1をインストールしましたが、  
  同じリポジトリを使用してバージョン7.3をインストールすることが可能。
-  Laravel ver6.0では `php artisan make:auth` は使用できません。 
[Laravel Readouble](https://readouble.com/laravel/6.x/ja/authentication.html)(Laravelの公式リファレンス)に記載があり。

## 6. 作業完了条件

- 全てのリソースの作成が完了する。
- インストールしたLaravelプロジェクトにログイン機能を実装して実際にログインすること。
***
## 7. 作業者

BW) 藤井 栄輝(研修生)

## 8. チェッカー

GIZ) GIZ TECHメンター

## 9. 作業場所

自宅 or oVice内

## 10. 作業端末

自端末(MacBook Pro 13-inch, 2016)
***
## 11. 設定したバージョンは以下の通り

|         | Ver.     | 
| :------ | :------: | 
| PHP     | 7.3      | 
| Nginx   |  1.21.1  | 
| MySQL   | 5.7      | 
| Laravel | 6.0      | 
| OS      | CentOS 7 | 


## 12. 作業手順書
***
### 1. VirtualBoxのインストール
下記のサイトからdmgファイルをダウンロード後、インストールを進めてください。

[Virtual Box公式](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)

`OS X hosts` を選択しましょう。

以下のコマンドを実行してVirtualBoxのウィンドウが表示されれば正常にインストールされています。
```bash
$ virtualbox
```
コマンド実行後は入力を受け付けない状態となるため、 `Control + c` を押してください。
***
### 2. Vagrantのインストール
```bash
$ brew cask install vagrant
```
- homebrewのバージョンが新しいと、caskコマンドがオプションになっており、上記のコマンドでエラーになる場合があります。その時は下記のコマンドでインストール
```bash
$ brew install --cask vagrant
```
- インストールが終わったら `vagrant` コマンドが使用可能かどうか確認するため以下のコマンドを実行
```bash
$ vagrant -v
```
***
### 3. CentOS 7 ダウンロード
LinuxのCentOSのバージョン7のbox名 `centos/7` を指定して実行
```bash
vagrant box add centos/7
```

下記の選択肢で3を選択
```bash
1) hyperv
2) libvirt
3) virtualbox
4) vmware_desktop

Enter your choice: 3
```

#### Vagrantfileを作成
```bash
$ vagrant init centos/7
```

#### Vagrantfileの編集
- 今回行う編集は、三箇所
```bash
# 変更点①
config.vm.network "forwarded_port", guest: 80, host: 8080

# 変更点②
config.vm.network "private_network", ip: "192.168.33.19"
```
上記二箇所の # が付いているのを外してコメントインで編集

- 以下編集でホストOS の`virtual_env_manual`ディレクトリ内と  
  ゲストOSの `/vagrant` のディレクトリ内をリアルタイムで同期するための設定
```bash
# 変更点③
config.vm.synced_folder "../data", "/vagrant_data"
# ↓ 以下に編集
config.vm.synced_folder "./", "/vagrant", type:"virtualbox"
```
Vagrantfile があるディレクトリにて以下のコマンドを実行
- ゲストOSを起動
```bash
$ vagrant up
```
virtual_env_manual ディレクトリに移動して下記のコマンドを実行
- ゲストOSへログイン
```bash
$ vagrant ssh
```
***
パッケージをインストール
```bash
$ sudo yum -y groupinstall "development tools"
```

Complete!とでればOKです！

### 4. PHPのインストール
```bash
$ sudo yum -y install epel-release wget

$ sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

$ sudo rpm -Uvh remi-release-7.rpm

$ sudo yum -y install --enablerepo=remi-php73 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip

$ php -v
```
PHP7.3 ver確認できたらOKです！
```
PHP 7.3.29 (cli) (built: Jun 29 2021 09:30:31) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.29, Copyright (c) 1998-2018 Zend Technologies
```

#### composerのインストール

```bash
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

$ php composer-setup.php

$ php -r "unlink('composer-setup.php');"

$ sudo mv composer.phar /usr/local/bin/composer

$ composer -V
```

```
Composer version 2.1.5 2021-07-23 10:35:47
```
コンポーザーが確認できたらOKです！
***
### 5. MySQLのインストール

```bash
$ sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm

$ sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm

$ sudo yum install -y mysql-community-server

$ mysql --version
```

```
mysql  Ver 14.14 Distrib 5.7.35, for Linux (x86_64) using  EditLine wrapper
```

インストール確認できたらOKです！

#### MySQLの起動
```bash
$ sudo systemctl start mysqld
```

```bash
$ sudo systemctl status mysqld
```

`Active: active (running)`が確認できたらOKです！

#### パスワードの確認
```bash
$ sudo cat /var/log/mysqld.log | grep 'temporary password'
```

```bash
2017-01-01T00:00:00.000000Z 1 [Note] A temporary password is generated for root@localhost: hogehoge
```

`hogehoge` と記載されている箇所に存在するランダムな文字列がパスワード

#### MySQLへログイン
```bash
mysql -u root -p
```
パスワードを`hogehoge`の部分のパスを入力してログイン

#### パスワードのリセット
```bash
set password = "新たなpassword";
exit
sudo systemctl restart mysqld
```

MySQLのパスワード要件を達成したパスワードを設定してあげましょう。
例（Hoge.1234

再起動が確認できたら前回設定したパスワードで
ログインしましょう。

#### データベースを作成
```bash
create database データベース名;
```
***
### 6. Laravelのインストール
#### /vagrant直下で以下のコマンドを実行

```bash
$ composer create-project --prefer-dist laravel/laravel laravel_app "6.0"
```

#### laravel_appディレクトリ下の .env ファイルの内容を編集
編集前
```text
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```
編集後
```text
DB_DATABASE=laravel_app
DB_USERNAME=root
DB_PASSWORD=Hoge.1234
```
***
### 7. Nginxのインストール

`$ sudo vi /etc/yum.repos.d/nginx.repo`

- 以下ファイル内容を追記

```text
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=0
enabled=1
```

#### Nginxインストール
```bash
$ sudo yum install -y nginx
```
- `$ nginx -v`でインストールされてるか確認
```
nginx version: nginx/1.21.1
```
表示確認できたらOKです。

#### Nginxの設定ファイルを編集

`$ sudo vi /etc/nginx/conf.d/default.conf`

- 以下ファイル内容を編集

```bash
server {
  listen       80;
  server_name  192.168.33.19; # Vagranfileでコメントを外した箇所のipアドレスを記述してください。
  # ApacheのDocumentRootにあたります
  root /vagrant/laravel_app/public; # 追記
  index  index.html index.htm index.php; # 追記

  #charset koi8-r;
  #access_log  /var/log/nginx/host.access.log  main;

  location / {
      #root   /usr/share/nginx/html; # コメントアウト
      #index  index.html index.htm;  # コメントアウト
      try_files $uri $uri/ /index.php$is_args$args;  # 追記
  }

  # 省略

  # 該当箇所のコメントを解除し、必要な箇所には変更を加える
  # 下記は root を除いたlocation { } までのコメントが解除されていることを確認してください。

  location ~ \.php$ {
  #    root           html;
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;  # $fastcgi_script_name以前を /$document_root/に変更
      include        fastcgi_params;
  }

  # 省略
```

`$ sudo vi /etc/php-fpm.d/www.conf`

- 以下ファイル内容を編集

```text
;24行目近辺
user = apache
# ↓ 以下に編集
user = nginx

group = apache
# ↓ 以下に編集
group = nginx
```

#### `nginx` というユーザーでもログファイルへの書き込みができる権限を付与
```bash
$ cd /vagrant/laravel_app
$ sudo chmod -R 777 storage
```
#### 設定を反映するために再起動します。
```bash
$ sudo systemctl restart nginx
$ sudo systemctl restart php-fpm
```

#### Nginxとfpmを起動してステータスでNginxが起動していることを確認する
```bash
$ sudo systemctl start nginx
$ sudo systemctl start php-fpm
$ sudo systemctl status nginx
$ sudo systemctl status php-fpm
```
nginxとphp-fpmのステータスを確認して`Active: active (running)`と出たらOK

***
### 8. Laravel6.0 ログイン機能実装

#### laravel/uiをインストールする
```bash
$ composer require laravel/ui:^1.0 --dev
```

#### artisanコマンドを実行する
```bash
$ php artisan ui bootstrap --auth
```

#### 以下のコマンドでDBにテーブルを作成
```bash
$ php artisan migrate
```

***
## 13. どういう流れで環境構築したか

- 全体の流れを意識して骨組みを組み立てて、各Sectionを振り返りながらMDに記述しつつ実装。
- とりあえず実装が完成しなくともMDを完成させる方向で進めてみて、実装途中で不明確なポイントを洗い出した。

***
## 14. 環境構築の所感

- 環境構築中に気付いたことや学んだことは？
  - 誰が見てもわかり易く、なおかつ見た目を意識しました。
  - その１コード、１コマンドが何をしているのか一目で見て把握できるのか否かも意識して書きました。

***
## 15. 参考サイト

- [自己流の手順書フォーマットを公開してみた](https://dev.classmethod.jp/articles/non-97-operation-manual/)
- [MarkdownでTable(表テーブル)を書く](https://notepm.jp/help/markdown-table)
- [インフラエンジニアの綺麗で優しい手順書の書き方](https://www.slideshare.net/shoheikoyama77/ss-52793124)
- [新人エンジニアに捧ぐ！ Linux 本番環境で作業する前に確認しておきたい3つのポイント](https://www.techscore.com/blog/2016/04/01/%E6%96%B0%E4%BA%BA%E3%82%A8%E3%83%B3%E3%82%B8%E3%83%8B%E3%82%A2%E3%81%AB%E6%8D%A7%E3%81%90%EF%BC%81-linux-%E6%9C%AC%E7%95%AA%E7%92%B0%E5%A2%83%E3%81%A7%E4%BD%9C%E6%A5%AD%E3%81%99%E3%82%8B%E5%89%8D/)
- [きれいな手順書をつくるための7つのポイントきれいな手順書をつくるための7つのポイント](https://nishinatoshiharu.com/tidy-procedure/)
- [Laravel6.x以降でログイン機能をインストールする方法](https://blog.capilano-fw.com/?p=4576)

