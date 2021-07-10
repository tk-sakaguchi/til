## DBごとにデフォルトの文字ソート方法が異なるよ
- 各DBのアルファベットソート時のデフォルトの挙動は以下の通り(ほかのDBは未調査)
  - PostgreSQL: 大文字小文字の区別なしで実行される([サンプル](http://sqlfiddle.com/#!17/53802/4))
  - MySQL: 大文字小文字の区別なしで実行される([サンプル](http://sqlfiddle.com/#!9/53802d2/2))
  - Oracle: 文字コード順にソートする([サンプル](http://sqlfiddle.com/#!4/ea044/1))
      - これをバイナリ・ソートと言うらしい([参考](https://docs.oracle.com/cd/E16338_01/server.112/b56307/ch5lingsort.htm))

## PostgreSQLやMySQLで文字コード順でソートしたいとき
-  PostgreSQL: COLLATE句を使用する
    - [サンプル](http://sqlfiddle.com/#!17/53802/3)
    - [参考](https://www.postgresql.jp/document/12/html/collation.html)
        - COLLATEの後に`C`か`POSIX`を指定するとASCII文字コードのバイト値でソートする
        - DB作成時に照合順序を定義することもできそう
- MySQL: `BINARY` を使用して`ORDER BY BINARY col_name` のように指定する
    - [サンプル](http://sqlfiddle.com/#!9/53802d2/4)
    - [参考](https://dev.mysql.com/doc/refman/5.6/ja/sorting-rows.html)

## ちなみに
- Rubyで文字列の大小比較を行う際は文字コードをもとに比較が行われる
``` ruby
irb(main):001:0> 'a' < 'b'
=> true
irb(main):002:0> 'a' < 'B'
=> false
irb(main):003:0> 'a'.ord
=> 97
irb(main):004:0> 'b'.ord
=> 98
irb(main):005:0> 'A'.ord
=> 65
irb(main):006:0> 'B'.ord
=> 66
```
