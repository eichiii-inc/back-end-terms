# Golang コーディング規約

## 変数宣言

変数を宣言する際は、初期値を明示する

```Go
// bad
var message string

// good
message := ""
```

## 構造体

構造体を宣言する際は、フィールドのコメントを記載する

```Go
// bad
type User struct {
 UserID int64
 Name   string
}

// good
type User struct {
 UserID int64  // UserID
 Name   string // 名前
}
```
