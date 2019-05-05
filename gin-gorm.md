# Gin Web Framework および GORM の基本Tips

## Gin


## GORM

### インポート
```go
import (
  "github.com/jinzhu/gorm"
  _ "github.com/jinzhu/gorm/dialects/mysql"
)
```

### DB接続 基本文
```go
func gormConnect() *gorm.DB {
  DBMS := "mysql"
  USER := "root"
  PASS := ""
  PROTOCOL := "tcp(0.0.0.0:3306)"
  DBNAME := "gin_gorm"
  OPTION := "?parseTime=true"

  CONNECT := USER + ":" + PASS + "@" + PROTOCOL + "/" + DBNAME + OPTION
  db, err := gorm.Open(DBMS, CONNECT)

  if err != nil {
    panic(err.Error())
  }
  return db
}

func main() {
  db := gormConnect()
  defer db.Close()

  // logモード
  db.LogMode(true)

  // DBエンジンを「InnoDB」に設定
  db.Set("gorm:table_options", "ENGINE=InnoDB")
}

```

### テーブル作成及び削除
```go
// User ...
type User struct {
  gorm.Model
  Name     string `gorm:"size:255"`
  Password string `gorm:"size:255"`
  Email    string `gorm:"size:255"`
}


// in func main()
// ...
db.AutoMigrate(&User{}) // テーブル作成
db.DropTable(&User{}) // テーブル削除
```

### ※ gorm.Model の定義
```go
type Model struct {
  ID        uint `gorm:"primary_key"`
  CreatedAt time.Time
  UpdatedAt time.Time
  DeletedAt *time.Time
}
```

### レコード関連メソッド
```go

// 単体 Create
user := User{
  Name:     "Yukihiro Taniguchi",
  Password: "12345678",
  Email:    "test@example.com",
}
db.Create(&user)


// Read
db.First(&user1, 1) // IDで取得、Nameも取れる
db.First(&user1, User{Name: "Yukihiro Taniguchi"}) // ID以外取得

// ない場合に第一引数に第二引数の値を反映
db.First(&user1, User{Name: "Yukihiro Taniguchi"})

// ない場合に第一引数に第二引数の値を反映し、レコードを作成
db.FirstOrCreate(User{Name: "Yukihiro Taniguchi"})


// 単体 Update
user1 := User{}
// Name で取得(該当した最初の一個)
db.First(&user1, User{Name: "Yukihiro Taniguchi"})
// 構造体を用いてUpdate
db.Model(&user1).Updates(User{Name: "Frank Underwood"})
// 直接書いてUpdate
db.Model(&user1).Update("name", "Frank Underwood")


// 複数 Update
users := []User{}
// Name で取得(該当したすべて)
db.Find(&users, User{Name: "Yukihiro Taniguchi"})
// 構造体を用いてUpdate
db.Model(&users).Updates(User{Name: "Frank Underwood"})

// Delete
db.Delete(&user1) // deleted_at カラムに日時が設定される


```


### Relationships
```go
// Belong To

// User ...
type User struct {
  gorm.Model
  Profile   Profile `gorm:"foreignkey:ProfileID"`
  ProfileID uint
}

// Profile ...
type Profile struct {
  gorm.Model
  Name string
}


// main.go
// test用レコードを作成
profile := Profile{Name: "Yukihiro Taniguchi"}
db.Create(&profile)
users := make([]User, 10)
for i := range users {
  users[i].ProfileID = profile.ID
  db.Create(&users[i])
}


// users[3] が参照しているprofileレコードを取得
profile2 := Profile{}
db.Model(&users[3]).Related(&profile2)
fmt.Printf("profile2.Name : %s", profile2.Name)
// profile2.Name : Yukihiro Taniguchi


// profileを参照している全userレコードを取得
users2 := make([]User, 10)
db.Where(&User{ProfileID : profile.ID}).Find(&users2)
for i := range users2 {
  fmt.Printf("users2[%d].ID : %d\n", i, users2[i].ID)
}
/**
users2[0].ID : 1
users2[1].ID : 2
users2[2].ID : 3
users2[3].ID : 4
users2[4].ID : 5
users2[5].ID : 6
users2[6].ID : 7
users2[7].ID : 8
users2[8].ID : 9
users2[9].ID : 10
**/
```

参考 : ) [GORM](http://doc.gorm.io/)  
参考 : ) [GORM の Association 関連で、任意の外部キー名を使用しようとしてハマったのでメモ。](rttp://egawata.hatenablog.com/entry/2017/01/08/073313)

### Docker × GORM

#### docker-compose.yml
```yaml
version: '3'

services:
  # MySQL
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: gin_gorm_docker
      MYSQL_USER: docker
      MYSQL_PASSWORD: docker
      TZ: 'Asia/Tokyo'
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    volumes:
    - ./mysql/data:/var/lib/mysql
    - ./mysql/my.cnf:/etc/mysql/conf.d/my.cnf
    ports:
    - 3306:3306

  go:
    image: golang:1.11
    build: ./db
    container_name: go_host
    environment:
      - GO111MODULE=on
    volumes:
      - ./db/:/db
    ports:
      - 8888:8888

```

#### db/main.go
```go
func gormConnect() *gorm.DB {
  DBMS := "mysql"
  USER := "docker"
  PASS := "docker"
  PROTOCOL := "tcp(db:3306)" // PROTOCOL := "tcp(0.0.0.0:3306)" だとだめ

  DBNAME := "gin_gorm_docker"
  OPTION := "?parseTime=true"

  CONNECT := USER + ":" + PASS + "@" + PROTOCOL + "/" + DBNAME + OPTION
  db, err := gorm.Open(DBMS, CONNECT)

  if err != nil {
    panic(err.Error())
  }
  return db
}
```

#### mysql/my.cnf

```cnf
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
```

#### mysql/data (ディレクトリ)
作成しておく(マウントするといろいろその中に作成される)


参考 : ) [gin gormでデータベース接続](https://qiita.com/ogurasousui/items/c0324dbb8dfdb9e0ac1b)
