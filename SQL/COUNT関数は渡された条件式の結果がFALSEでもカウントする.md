## 環境
- PostgreSQL 11.10

## やりたいこと
usersテーブル(男性: sex = 1 女性: sex = 2)
| id | name | sex |
| -- | -- | -- |
| 1  | 太郎 | 1 |
| 2  | 健太 | 1 |
| 3 | 花子 | 2 |
| 4 | 武 | 1 |
| 5 | 遥 | 2 |

- 上記のusersテーブルから男女それぞれの人数を集計して以下のように取得したい

| male_count | female_count |
| --  | -- |
| 3 | 2 |

## ダメなクエリ
```SELECT COUNT(sex = 1) AS male_count, COUNT(sex = 2) AS female_count FROM users;```
- 上記のクエリで良さそうな気がするが結果はこうなる

  | male_count | female_count |
  | --  | -- |
  | 5 | 5 |

## なぜダメか
- COUNT関数は条件式が非NULL値を持つ入力行の個数をカウントしている [参考](https://www.postgresql.jp/document/11/html/functions-aggregate.html)
=> COUNT関数はFALSEをカウントする
    - 検証
     -> ```SELECT COUNT(TRUE) AS true, COUNT(FALSE) AS false FROM users;```
 
      | true | false |
      |   --  | -- |
      |  5 | 5 |

## うまくいくクエリ
```SELECT COUNT(sex = 1 OR NULL) AS male_count, COUNT(sex = 2 OR NULL) AS female_count FROM users;```
- 条件式に`OR NULL`を加えれば期待通りになる

  | male_count | female_count |
  | --  | -- |
  | 3 | 2 |

## なぜうまくいくか
- COUNT関数は条件式が非NULL値を持つ入力行の個数をカウントしている [参考](https://www.postgresql.jp/document/11/html/functions-aggregate.html)
=> COUNT関数はNULLをカウントしない
  - 検証
  -> ```SELECT COUNT(TRUE) AS true, COUNT(FALSE) AS false, COUNT(NULL) AS null FROM users;```

      | true | false | null |
      | --  | -- | -- |
      | 5 | 5 | 0 |

- `TRUE OR NULL`は短絡評価で`TRUE`と評価される
- `FALSE OR NULL`は`FALSE`と`NULL`の和集合で`NULL`と評価される [参考](https://www.postgresql.jp/document/11/html/functions-logical.html)
    - 検証
    -> ```SELECT (NULL IS NULL) OR NULL as a, (1 IS NULL) OR NULL as b```

      | a | b |
      | --  | -- |
      | TRUE | NULL |

## まとめ
- COUNT関数に条件式を渡した場合は条件式が非NULL値を持つ入力行の個数
をカウントしている [参考](https://www.postgresql.jp/document/11/html/functions-aggregate.html)
    - つまり条件式の結果がFALSEの場合でもカウントされる

## ちなみに
- MySQLでも大体同じっぽい
  - [or演算子](https://dev.mysql.com/doc/refman/8.0/ja/logical-operators.html#operator_or)
  - [count関数](https://dev.mysql.com/doc/refman/8.0/ja/aggregate-functions.html#function_count)
