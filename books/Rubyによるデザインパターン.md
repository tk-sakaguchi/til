# 第1部 パターンとRuby

## 第1章 よいプログラムとパターン

#### ギャング・オブ・フォー
* 『オブジェクト指向における再利用のためのデザインパターン』を出版した4人
* デザインパターンを一般的な設計の問題に対する解決策としてまとめた
* 23のパターンを特定し、名前をつけ、説明した

#### パターンのためのパターン
* 変わるものを変わらないものから分離する
* インターフェースに対してプログラムし、実装に対して行わない
  * もしも自動車を乗り物と呼べるならば、自動車を自動車と呼ばない
* 継承よりも集約
  * オブジェクトが何かの一種である(is-a-kind-of)関係は避けて、何かを持っている(has-a)関係にする
* 委譲、委譲、委譲
* 必要になるまで作るな
  * You Ain't Gonna Need It (YAGNI)

## 第2章 Rubyを始めよう

#### 気になった点だけメモ
* `''`で文字列を囲んだ際は`/n`などはそのまま文字列として扱う
* `""`で文字列を囲んだ際は`/n`などは変換する(`/n`であれば改行文字に変換する)
* `require`はファイルをすでにロードしているかを判定しており、同じファイルを2回ロードすることはない

# 第2部 Rubyにおけるパターン

## 第3章 アルゴリズムを変更する：Template Method

#### フックメソッド
* `Template Method`の具象クラスによってオーバーライドできる非抽象メソッド
* 以下を選択することができる
  * 基底実装をオーバーライドすることで別の処理を実行させる
  * 標準実装をそのまま使う

#### まとめ
* 継承ベースのパターン
* 基底クラスに不変の部分を記述し、変わる部分はサブクラスに定義するメソッドにカプセル化する
* 基底クラスはメソッドを未定義にしておくことができる
  * その場合はサブクラスでそのメソッドを提供しなければならない
* 未定義にする代わりに、基底クラスで標準実装を提供し、必要であればサブクラスでオーバーライドすることもできる

## 第4章 アルゴリズムを変換する：Strategy

#### 委譲、委譲、さらに委譲
* `Strategy`パターンの核となるアイデアは、同じ目的を持った一群のオブジェクト、`Strategy`を定義すること
* `Strategy`はすべて正確に同じインターフェースを提供するため、その利用者(`Context`)はそれらを取り換え可能なパーツとして扱うことができる

#### `Context`と`Strategy`の間でデータを共有する
* データを共有するには2つの選択肢がある
  * `Context`が`Strategy`を呼び出す際に必要なものをすべて引数として渡す → 引数が大量になった際に使われるかわからないものも引数として渡す必要がある
  * `Strategy`へと渡された`Context`自身の参照を使って、`Strategy`が`Context`から必要なものを引き出す → 結合度が上がる

#### まとめ
* `Template Method`パターンと同様の問題に対する委譲ベースのアプローチ
* アルゴリズムのパターンごとにばらばらのオブジェクトとしてシンプルに実装する
* `Strategy`パターンの背後にある動機は、アルゴリズムのバリエーションをどうやって演じたらよいかを知っているオブジェクトを提供すること

## 第5章 変更に追従する：Observer

#### 通知を受ける
* `Observer`パターンのアイデアは、「何らかのオブジェクトが変化した」というニュースの発信者と消費者の間にきれいなインターフェースをつくること
* サブジェクト(`Subject`)：ニュースを持っているクラス
* オブザーバ(`Observer`)：ニュースを得ることに関心のあるオブジェクト

#### さまざまな`Observer`パターン
* pull型：`Observer`にただ一つのメソッドがあり、唯一の引数が`Subject`
* push型：変更についての多くの詳細データを`Subject`が`Observer`に対して送信させる

#### まとめ
* `Observer`パターンは`Strategy`パターンと少し似ているが、目的が異なる
  * `Observer`パターンでは、`Observable`オブジェクトで発生しているイベントをほかのオブジェクトに伝える
  * `Strategy`パターンでは、何かの処理を行うために`Strategy`オブジェクトを取得する

## 第6章 部分から全体を組み立てる：Composite

#### `Composite`の作成
* `Composite`パターンのアイデアは、「全体が部分のように振る舞う」という状況を表すこと
  * `Composite`パターンは、階層構造やツリー構造のオブジェクトを作りたいとき、そしてそのツリーが利用するコードが1つの単純なオブジェクトを扱っているのか、それともごちゃごちゃした枝全体を扱っているのかを考えさせたくないときに利用できる
* `Composite`パターンを作るには3つの部品を使う
  * `Component`：すべてのオブジェクトの共通インターフェース、または、基底クラス
  * `Leaf`：プロセスの単純な構成要素で、1つ以上必要
  * `Composite`：`Composite`も`Component`だが、サブコンポーネントから作られる、より上位のオブジェクト

#### `Composite`パターンの使用上の注意
* よくある間違いは、ツリーの深さが1段しかないことを想定してしまっているもの
  * 覚えておきたいのは、`Composite`パターンは任意の深さのツリーを作れる力を持っていること

#### まとめ
* `Composite`パターンは基本的でありふれたものであるため、時々他のパターンに紛れて登場する
* `Interpreter`パターンは`Composite`パターンを特化したもの
* `Composite`パターンは`Iterator`パターンなしに想像するのは難しい
