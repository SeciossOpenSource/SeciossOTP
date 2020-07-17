# SeciossOTP
SeciossOTP (Secioss One-Time Password)は、以下の機能を提供するオープンソースのソフトウェアです。
* ユーザーワンタイムパスワードの設定
* ユーザーワンタイムパスワードの認証

## 目標
* エンドユーザーワンタイムパスワードの設定を行うことでGoogle Authenticatorを利用し、ワンタイムパスワードで認証できること

## インストール
### 環境
* OS：CentOS 7
* ミドルウェア：Apache、PHP、MariaDB

### インストール
packageを展開して、インストールスクリプトを実行。
# ./isntall.sh install`

### データベースのインストール
MariaDBをインストールして、以下のテーブルを作成して下さい。
~~~
CREATE TABLE `otp_tbl` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `otp_id` varchar(255) NOT NULL,
  `user_id` varchar(255) DEFAULT NULL,
  `user_agent` varchar(256) DEFAULT NULL,
  `device_name` varchar(255) DEFAULT NULL,
  `additional_data` TEXT NULL DEFAULT NULL,
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `update_time` timestamp NULL DEFAULT NULL,
  `access_time` timestamp NULL DEFAULT NULL,
  `deleted` int(11) NOT NULL DEFAULT '0',
  PRIMARY KEY (`id`),
  KEY `otp_id` (`otp_id`),
  KEY `user_id` (`user_id`,`otp_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;


CREATE TABLE `user_info` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` varchar(255) NOT NULL,
  `password` varchar(255) NOT NULL,
  `email` varchar(255) NULL DEFAULT NULL,
  `seciossotpoption` varchar(255) NULL DEFAULT NULL,
  `seciossotpsecretpin` varchar(255) NULL DEFAULT NULL,
  `seciossotpinitsecret` varchar(255) NULL DEFAULT NULL,
  `seciossdeviceidxfido` text,
  `seciossaccountstatus` varchar(10) DEFAULT 'active',
  `additional_data` TEXT NULL DEFAULT NULL,
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `update_time` timestamp NULL DEFAULT NULL,
  `access_time` timestamp NULL DEFAULT NULL,
  `deleted` int(11) NOT NULL DEFAULT '0',
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`) ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
)
~~~      

## 設定
設定ファイル/var/www/conf/config.iniを環境に合わせて変更して下さい。
```
[password]
storage="DB"
db_host="<データベースホスト名>"
db_user="<データベースユーザー名>"
db_pass="<データベースパスワード>"
db_name="<データベーススキーマ名>"
......
```

## 画面
### Google Authenticator
~~~
https://localhost/user/qrsecret.php?st=ga
~~~
### メールアドレス通知
~~~
https://localhost/user/motpmail.php
~~~

## Google Authenticator認証
### リクエスト
~~~
curl https://localhost/api/otp.php?userid=<ユーザーID> -d password=<ワンタイムパスワード>
~~~
### レスポンス　XML
~~~
<response>
  <code>認証結果コード</code>
  <message>認証結果</message>
  <id>ユーザーID</id>
  <authzto></authzto>
</response>
~~~

## メールアドレス通知
### ワンタイムパスワード送信リクエスト
~~~
curl https://localhost/pub/motp.cgi -d username=<ユーザーID>&sessid=<セッションID>
~~~
### ワンタイムパスワードの検証
~~~
curl http://localhost/pub/checkmotp.cgi -d userid=<ユーザーID>&password=<ワンタイムパスワード>
~~~



