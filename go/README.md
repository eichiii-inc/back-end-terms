# Golang コーディング規約

## ディレクトリ
```
src/
    conf/ -- 全体で利用する固定値などの宣言ファイル
    dao/ -- DBや外部へ接続する（Data Access Object）
    entity/ -- 全体で利用するモデルの宣言
    handler/ -- routeから呼び出される。主にバリデーションを行い、serviceを呼び出す
    route/ -- 受け付けるパスと実行handlerの接続
    service/ -- ビジネスロジックの記述、DAOの利用
    config/ -- dev/stg/prodでの固定値の分離設定ファイル
    log/ -- ログ出力用ラッパー
    middleware/ -- Authなどの中間処理
    util/ -- 便利処理や共通処理
    main.go -- ここから開始されます。
```

## ファイル名
スネークケースの命名
```
// bad
BookSearch.go

// good
book_search.go
```

## 記述
* インデント  
ハードタブ（タブ文字）を使用し、ソフトタブ（半角スペース）を使用しない。
* １行あたりの文字数  
制限はない。長すぎる場合は改行してもよい。
* コメント // の後には１スペースを入れる
```go
// bad
name := "" //名前

// good
name := "" // 名前
```

## コードを自動整形する
1. コーディング規約に従ってコードを自動整形するためのツール (go fmt) を使用する
2. golang.org/x/tools/cmd/goimports (go goimports)を使用する。  
パラメータは、（-w -local "github.com/eichiii-inc" $FilePath$）

## 命名規則
公開メソッド、フィールド  
大文字（アッパーキャメルケース）で始める
```Go
// good
MexedCaps := ""
```

非公開メソッド、フィールド 
小文字（キャメルケース）で始める
```Go
// good
mixedCaps := ""
```

コンストラクタ
New + 生成対象の構造体名
```Go
type Book struct {
	Name string
}

func NewBook(name string) *Book {
    return &Book{
		Name: name,
    }
}
```

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
tag名json情報を記載する  
連続した変数定義やコメントは縦に揃える

```Go
// bad
type User struct {
 UserID int64
 Name   string
}

// good
type User struct {
 UserID int64  `json:"user_id"` // UserID
 Name   string `json:"name"`    // 名前
}
```

## defer（遅延関数）
関数から戻る直前に実行される  
ファイルの閉じ忘れなどを防ぐ。Open後、必ずCloseが必要な処理には記述する
```go
// Contents returns the file's contents as a string.
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close will run when we're finished.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // append is discussed later.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // f will be closed if we return here.
        }
    }
    return string(result), nil // f will be closed if we return here.
}
```