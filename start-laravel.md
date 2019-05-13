# laravel-homestead 環境設定

## プロジェクトの新規作成

### laravel application の作成
ディレクトリを移動
```shell
~$ cd myapp
```
homestead のインストール
```shell
~/myapp$ composer require laravel/homestead --dev
```
Homestead.yaml を作成する
```shell
~/myapp$ composer install
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 78 installs, 0 updates, 0 removals
~

~/myapp$ vendor/bin/homestead make
Homestead Installed!
```

### IPの設定
VM で使用されている、IP を確認
```shell
~/myapp$ cat /etc/hosts
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
192.168.10.10  hoge.test
~
```
Homestead.yaml の IP を他の VM と被らないように変更
```shell
~/myapp$ vim Homestead.yaml
```

```diff:Homestead.yaml
-ip: 192.168.10.10
+ip: 192.168.10.11
memory: 2048
cpus: 1
provider: virtualbox
authorize: ~/.ssh/id_rsa.pub
keys:
~
```
hosts ファイルに、ホスト名と IP を追加
```shell
~/myapp$ sudo vim /etc/hosts
```
```diff:/etc/host
+192.168.10.11 myapp.test
```

### Guest 内へ

```shell
~/myapp$ vagrant up

~/myapp$ vagrant ssh
```
Guest 内
```shell
Thanks for using
 _                               _                 _
| |                             | |               | |
| |__   ___  _ __ ___   ___  ___| |_ ___  __ _  __| |
| '_ \ / _ \| '_ ` _ \ / _ \/ __| __/ _ \/ _` |/ _` |
| | | | (_) | | | | | |  __/\__ \ ||  __/ (_| | (_| |
|_| |_|\___/|_| |_| |_|\___||___/\__\___|\__,_|\__,_|

~$ cd code

~/code$ cp .env.example .env

~/code$ php artisan key:generate
```
### Hostへ
```
.env ファイルに、host 情報を追記
```shell
~/myapp$ vim .env
```
```diff:.env
-APP_URL=http://localhost
+APP_URL=http://project.test
```

### DB設定 (SQL クライアントで接続)
Over SSH
- Host : 127.0.0.1:3306(Host)
- User : homestead
- Password : secret (デフォルト)
- Server : 192:168:10.11(Guest)
- Port : 22(Guest)
- User : vagrant
- Password : vagrant (デフォルト)

---

## homestead が入ったプロジェクト をclone するとき
### laravel application のclone
```shell
~$ git clone https://github.com/hoge/huga.git

~$ cd huga
```
Homestead.yaml を作成する
```shell
~/huga$ composer install
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 78 installs, 0 updates, 0 removals
~

~/huga$ vendor/bin/homestead make
Homestead Installed!

```
以下「IPの設定」に続く
