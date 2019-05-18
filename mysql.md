# MySQL の基本Tips

起動
```shell
mysql.server start
```

停止
```shell
mysql.server stop
```

ログイン
```shell
mysql -u root
```

データベース一覧
```sql
mysql> show databases;
```

データベースの選択(なかったら作る)
```sql
mysql> use mysql;
Database changed
```

テーブルからカラムを選択し表示
```sql
mysql> select Host, User from user;
+-----------+-----------+
| Host      | User      |
+-----------+-----------+
| localhost | mysql.sys |
| localhost | root      |
+-----------+-----------+
2 rows in set (0.00 sec)
```

テーブルからカラムをすべて選択し表示
```sql
select * from user;
```
こうすると見やすい
```sql
select * from user\G
```

ログインせずにコマンドを実行
```shell
mysql -u root mysql -e 'show tables;'
```

ファイルからコマンドを実行
```shell
mysql -u root mysql < hoge.sql
```

データベース作成
```sql
mysql> create database hogedatabase;
```

ログインし、任意のデータベースに対話モード
```sql
mysql -u root hogedatabase;
```



参考 : ) [MySQL 入門](https://qiita.com/okamuuu/items/c4efb7dc606d9efe4282)

参考 : ) [MySQL 5.6 リファレンスマニュアル](https://dev.mysql.com/doc/refman/5.6/ja/)
