# SQL コーディング規約

## エイリアス

テーブルのエイリアスを指定する際は、テーブル名を構成する各単語の頭文字を繋げて記載する。
ただし、テーブル名が1つの単語のみで構成される場合は、データベース名の頭文字を利用し2文字以上とすること。

``` sql
// bad
customer_db.customer_users AS t1
user_db.users AS u

// good
customer_db.customer_users AS cu
user_db.users AS uu
```
