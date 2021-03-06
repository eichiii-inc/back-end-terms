# Golang コーディング規約

## ディレクトリ

各パッケージ

```
src/
    conf/ -- 各パッケージのconf処理用
    dao/ -- DBや外部へ接続する（Data Access Object）
    entity/ -- パッケージのみ利用するモデルの宣言
    handler/ -- routeから呼び出される。主にバリデーションを行い、serviceを呼び出す
    route/ -- 受け付けるパスと実行handlerの接続
    service/ -- ビジネスロジックの記述、DAOの利用
```

api-common -- 共通処理

```
src/
    conf/ -- 全体で利用する固定値などの宣言ファイル
    config/ -- dev/stg/prodでの固定値の分離設定ファイル
    dao/ -- DBや外部へ接続する（Data Access Object）
    entity/ -- 全体で利用するモデルの宣言
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

## 制御文

論理値同士の比較はしない

```Go
// bad
if isEnable == true {
  // isEnableがtrueの場合の処理
}

// good
if isEnable {
  // isEnableがtrueの場合の処理
}

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

## ログレベル

エラーの原因や程度に応じて、ログレベルを使い分ける

| レベル | 使用方法 |
------|------
| FATAL |  （使用しない）アプリケーションの継続が不可能になる深刻な問題が発生した場合、アプリケイーションが停止するため使用しない |
| ERROR | SQLエラーやプログラムエラー等、アプリケーション内部で問題が発生し、処理を継続できない場合 |
| WARN | 処理を継続可能な問題が発生した場合、問題の情報と継続の情報<br/>また、API実行時の権限不足や、APIの不適切な使用などリクエストに問題がある場合 |
| INFO | 実行開始時のメソッド名と引数の情報 |
| DEBUG | システムの動作検証に必要な場合、ステップや条件確認 |
| TRACE | （使用しない）デバッグ情報よりも、さらに詳細な情報、開発時のみ利用できるとする。同行を削除するか、プロダクション実行では絶対に実行されないようにすること |

## 固定値確認での複数条件分岐

タイプなどの、固定値を確認する場合は、if-elseでネストするのではなくswitchを使用する  
switchは、１行以上の処理を記述する場合は、breakを記述しない、defaultの記述を行う  
チェック外の場合は、処理続行可能ならwarningを記述、続行不可ならerrorを出力する

```go
const (
// 施設ステータス
StatusDraft   = 1 // 下書き
StatusEnable  = 2 // 利用中
StatusDisable = 3 // 利用不可
)
status := 1

// bad
if status == StatusDraft {
    // 下書きの場合の処理
} else if status == StatusEnable {
    // 利用中の場合の処理
} else if status == StatusDisable {
    // 利用不可の場合の処理
} else {
    // 当てはまらない処理
}

// good
switch status {
case StatusDraft:
    // 下書きの場合の処理
case StatusEnable:
    // 利用中の場合の処理
case StatusDisable:
    // 利用不可の場合の処理
default:
    // 当てはまらない処理
}

```

## メソッドの設計

条件分岐後の処理が、個別に実行可能な場合や、if内の処理が大きくなる場合、別のメソッドに処理を分離する

```go
const (
	Detail = 1
	Place = 2
)
func UpdatePicture(typeID int64, pic []Picture) (err error) {
    switch typeID {
    case Detail:
        return UpdatePictureForDetail(pic)
    case Place:
        return UpdatePictureForPlace(pic)
    default:
        return errors.New(fmt.Sprintf("No Type ID %d", typeID))
    }
}
func UpdatePictureForDetail(pic []Picture) (err error) {
    // 処理
}
func UpdatePictureForPlace(pic []Picture) (err error) {
    // 処理
}
```
