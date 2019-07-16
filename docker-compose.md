# Docker Compose でサーバーサイド、フロントエンド、データベースの処理を切り分けるTips

## Docker Compose

- server side
- front end
- database
- docker-compose

### server side

### front end

### database

### docker-compose

docker-compose.yml をapplicationのルートディレクトリに配置

```yaml
version: '3'

services:
  client: # 名前は勝手につけられる
    build: ./client # build するディレクトリ (.はルート)
    ports:
      - 8080:8080 # host:guest
    volumes: # ディレクトリのマウント
      - ./client:/app  # host:guest
    command: yarn serve ## guestで実行したいコマンド(サーバーをたちあげる)

  server:
    build: ./server
    ports:
      - 8888:8888
    volumes:
      - ./server:/api
```
