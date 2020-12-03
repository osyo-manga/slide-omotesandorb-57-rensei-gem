#### Omotesando.rb #56
- - -

### AST から Ruby のコードに復元する
### プラグインをつくった

---

#### 自己紹介
- - -

* 名前：osyo
* Twitter : [@pink_bangbi](https://twitter.com/pink_bangbi)
* github  : [osyo-manga](https://github.com/osyo-manga)
* ブログ  : [Secret Garden(Instrumental)](http://secret-garden.hatenablog.com)
* 趣味で Ruby にパッチを投げたり bugs.ruby で気になったチケットをブログにまとめたりしてる
* Ruby で一番好きな機能は Refinements
* オンラインなのであちこちの地域.rb に参加してる
<br>
   * [Fukuoka.rb](https://fukuokarb.connpass.com/)
[Hamada.rb](https://hamadarb.connpass.com/)
[kawasaki.rb](https://kawasakirb.connpass.com/)
[nikotama.rb](https://nikotamarb.connpass.com/)
[Sendai.rb](https://sendairb.connpass.com/)
[Shibuya.rb](https://shibuyarb.connpass.com/)
[Shinjuku.rb](https://shinjukurb.connpass.com/)
[Tama.rb](https://tamarb.connpass.com/)
[Toyama.rb](https://toyamarb.connpass.com/)
[Gotanda.rb](https://gotanda-rb.connpass.com/)
[Entaku.rb](https://entakurb.doorkeeper.jp/)
[Grow.rb](https://growrb.doorkeeper.jp/)
[Hamamatsu.rb](https://hmrb.doorkeeper.jp/)
[kanazawa.rb](https://kzrb.doorkeeper.jp/)
[Kobe.rb](https://koberb.doorkeeper.jp/)
[Machida.rb](https://machidarb.doorkeeper.jp/)
[mitaka.rb](https://mitakarb.doorkeeper.jp/)
[Ruby Tuesday](https://ruby-tuesday.doorkeeper.jp/)
[toruby](https://toruby.doorkeeper.jp/)
[西日暮里.rb](https://nishinipporirb.doorkeeper.jp/)
<br>

---

### AST から Ruby のコードに
### 復元するプラグインをつくった

---

### Ruby のコードから AST 情報を取得する

---

#### RubyVM::AbstractSyntaxTree を使う
- - -

* RubyVM::AbstractSyntaxTree.parse(code) で code が構文解析される

```ruby
code = "1 + 2"
ast = RubyVM::AbstractSyntaxTree.parse(code)
pp ast.class
# => RubyVM::AbstractSyntaxTree::Node
pp ast
# => (SCOPE@1:0-1:5
#     tbl: []
#     args: nil
#     body: (OPCALL@1:0-1:5 (LIT@1:0-1:1 1) :+ (LIST@1:4-1:5 (LIT@1:4-1:5 2) nil)))
```

```ruby
# proc のブロックの中身をパースする
ast = RubyVM::AbstractSyntaxTree.of(proc{ 1 + 2 })
pp ast
# => (SCOPE@2:40-2:49
#     tbl: []
#     args: nil
#     body:
#       (OPCALL@2:42-2:47 (LIT@2:42-2:43 1) :+
#          (LIST@2:46-2:47 (LIT@2:46-2:47 2) nil)))
```
<!-- .element: class="fragment" -->

---

### AST から Ruby のコードに復元する

---

#### rensei-gem
- - -

* rensei-gem という gem をつくった
* これは AST から Ruby のコードに復元する gem
* まだ未完成なので注意してね！

```
$ gem install rensei
```

```ruby
require "rensei"

code = "1 + 2"

# parse 結果を
ast = RubyVM::AbstractSyntaxTree.parse(code)

# Ruby のコードに変換する
ruby = Rensei.unparse(ast)

pp ruby
# => "(1 + 2)"
```

---

#### 注意点
- - -

* 構文解析前のコードと復元する Ruby のコードは同じテキストにならない
* これは AST がそもそも余計な情報を切り落として生成されるため
    * 例えばコメントなどは AST 情報としては残らない
* rensei-gem では元の AST と復元した AST が全く同じになることだけを保証している

```ruby
require "rensei"

ast = RubyVM::AbstractSyntaxTree.of proc {
  # MEMO: 優先順位を変えるため and を使う
  hoge.bar if (a || b and c + d)
}
# 復元するコードは異なるが AST としては同じになる
puts Rensei.unparse(ast)
# =>
# if ((a || b) && (c + d))
#   hoge.bar()
# end
```


---

## [デモ](https://github.com/osyo-manga/gem-rensei/blob/cdc61c89616aae1616101cb8481833d96f27ca2e/sample/bocchi.rb)

---

#### まとめ
- - -

* Ruby は標準ライブラリで AST 情報を取得できる
* RubyVM::AbstractSyntaxTree はブロック内から AST 情報を取得できる
* rensei-gem で AST から Ruby のコードを復元する事ができる
    * これでブロック内のコードをいい感じに書き換える事ができる
* 詳しくはアドベントカレンダーの記事を読んでくれよな！！
    * [Ruby の AST から Ruby のソースコードを復元しよう](https://secret-garden.hatenablog.com/entry/2020/12/01/093316)


---

### ご清聴
### ありがとうございました

