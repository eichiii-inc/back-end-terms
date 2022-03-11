# SQL コーディング規約

## エイリアス

小文字で記載する。
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

## 記述

* 予約語
大文字で記述する

* テーブル名、カラム名
小文字で記載する

* インデント
ハードタブ（タブ文字）を使用し、ソフトタブ（半角スペース）を使用しない。

```sql
// bad
select
    *
from
    user_db.users as uu

// good
SELECT
	*
FROM
	user_db.users AS uu
```

### GROUP BY
* 集約関数を使う場合は順番を揃えてSELECT句の最後に集約関数を使う
```sql
// bad
SELECT
    user_id,
    COUNT(*) AS cnt,
    age
FROM
    user_db.users AS uu
GROUP BY
    user_id,
    age


// good
SELECT
    user_id,
    age,
    COUNT(*) AS cnt
FROM
    user_db.users AS uu
GROUP BY
    user_id,
    age
```
