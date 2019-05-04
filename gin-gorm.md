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

  CONNECT := USER + ":" + PASS + "@" + PROTOCOL + "/" + DBNAME
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
// から定義した構造体に代入してくれる
db.FirstOrInit(&user1)
// dbにあったら構造体に代入し、なかったら作って代入
db.FirstOrCreate(&user1)


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

参考 : ) [GORM](http://doc.gorm.io/)
