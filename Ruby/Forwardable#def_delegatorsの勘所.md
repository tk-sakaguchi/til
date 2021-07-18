### 結論
* `Forwardable#def_delegators`を使用することで、処理の`委譲`を実現できる
* 親クラスのメソッドをすべてサポートする`継承`に対して、サポートしたいメソッドをつまみ食いできるのが`委譲`

### Forwardable#delegatorsの使い方
``` ruby
require 'forwardable'

class Parent
  def morning_greeting
    'Good morning!'
  end

  def night_greeting
    'Good night!'
  end
end

class Child
  extend Forwardable

  def_delegators :@str, :length
  def_delegators :@arr, :first, :last
  def_delegators :@parent, :morning_greeting, :night_greeting

  def initialize(parent)
    @arr = %w[foo bar baz]
    @str = 'child'
    @parent = parent
  end
end

parent = Parent.new
child = Child.new(parent)
puts child.length            # => 5
puts child.first             # => foo
puts child.last              # => baz
puts child.morning_greeting  # => Good morning!
puts child.night_greeting    # => Good night!
```

### ポイント
* `継承`でも同じことはできるが、必要のないメソッドまでサポートしてしまう可能性がある
* `委譲`ではサポートしたいメソッドのつまみ食いができる

>継承を使っていたら、サポートしたくないメソッドは、undef_methodで隠すか、プライベートメソッドにするしかない
>委譲を使えば、サポートしたいメソッドをつまみ食いできる

Petter J.Jones　(2015) 「Effective Ruby」 P82￥

* メソッドチェーンが減る
  * 本来accessorを用意して, `child.str.length`と呼ぶところを`child.length`で呼べる

### ちなみに
```
def_delegators :@arr, :first, :last

def_delegator :@arr, :first
def_delegator :@arr, :last

delegate [:first, :last] => :@arr
```
* 上記の`Forwardable#def_delegators`、`Forwardable#def_delegator`、`Forwardable#delegate`のそれぞれを使用した3通りの書き方は全て同じ動作となる
* 個人的には個数が増減する可能性のあるメソッド群を最後尾に書ける`Forwardable#def_delegators`が好み
