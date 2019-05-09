# homestead 環境設定

## プロジェクトの新規作成

### laravel application の作成
ディレクトリを移動
```shell
$ cd myapp
```
To install Homestead directly into your project, require it using Composer:
```shell
$ composer require laravel/homestead --dev
```
Homestead.yml を作成する
```shell
$ composer install
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 78 installs, 0 updates, 0 removals
~

$ vendor/bin/homestead make
Homestead Installed!
```

### IPの設定
virtual machine で使用されている、ip を確認。
```shell
$ cat /etc/hosts
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
Homestead.yml の ip を他の virtual machine と被らないように変更。
```shell
$ vim Homestead.yml
```

```yaml:Homestead.yml
-ip: 192.168.10.10
+ip: 192.168.10.11
memory: 2048
cpus: 1
provider: virtualbox
authorize: ~/.ssh/id_rsa.pub
keys:
~
```
hosts ファイルに、ホスト名と IP アドレスを追加
```shell
$ sudo vim /etc/host
```
```:/etc/host
+192.168.10.11   laravel-project.test
```
### DB接続 (SQL クライアントで接続)
Over SSH
- Host : 127.0.0.1:3306(Host)
- User : homestead
- Password : secret (デフォルト)
- Server : 192:168:10.11(Guest)
- Port : 22(Guest)
- User : vagrant
- Password : vagrant

### Vagrant 内環境設定

```shell

$ vagrant up

$ vagrant ssh

--- in vagrant ---

$ cd code

$ cp .env.example .env

$ php artisan key:generate
```

.env ファイルに、host 情報を追記
```.env
-APP_URL=http://localhost
+APP_URL=http://project.test
```

### 参考
https://readouble.com/laravel/5.5/ja/homestead.html
https://qiita.com/7968/items/97dd634608f37892b18a
https://qiita.com/7968/items/97dd634608f37892b18a
https://ouchi-it.com/vagrantup-error/

---

## homestead が入ったプロジェクト をclone するとき
### homestead の準備
```shell
$ git clone https://github.com/hoge/huga.git

$ cd huga
```
Homestead.yml を作成する
```shell
$ composer install
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 78 installs, 0 updates, 0 removals
~

$ vendor/bin/homestead make
Homestead Installed!

```
以下「IPの設定」に続く
