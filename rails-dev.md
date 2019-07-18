# rails dev

## aws の設定

### パーミッションの設定

```shell
cp rails_app.pem ~/.ssh/
chmod 400 ~/.ssh/rails_app.pem
```

### aws インスタンスへの接続

```shell
ssh -i ~/.ssh/rails_app.pem ec2-user@ec2-XX-XX-XX-XX.ap-northeast-1.compute.amazonaws.com
```

## docker

