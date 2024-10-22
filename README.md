# renumnum

リナンバリングしやすく、かつ、辞書順に並ぶ数字のようなもの  

まず最初に、私たちがよく知っている数字を使ったときに起きやすい問題を説明します。  
その次に、問題を解決した数字を説明します。  
最後に、 Python を使ったスクリプトの書き方を説明します  

# 例：出席番号

```plaintext
1 アリス
2 チャーリー
3 デーブ
4 エミリー
```

👆　この出席番号の１番目と２番目の間にボブを加えたいとき  

```plaintext
1 アリス      -->  1 アリス
              -->  2 ボブ
2 チャーリー   -->  3 チャーリー
3 デーブ       -->  4 デーブ
4 エミリー     -->  5 エミリー
```

👆　もともと２番以降の人たちに**リナンバリング**（＝番号を昇順に振り直すこと）が発生します

リナンバリングしても、番号を振り直す手間が発生しないような便利な数字があったらいいな、と思っている人たちを**リナンバリンギスト**（Renumberingist）とでも呼ぶとしましょう。  
そんなリナンバリンギストのための数字が**リナンバリンギスト番号**（Renumberingist Number）です  

# 例：リナンバリンギスト番号を使った出席番号

リナンバリンギスト番号を説明する前に、まずは使ってみましょう  

👇　以下が、リナンバリンギスト番号を使った出席番号です  

```plaintext
o1o0 アリス
o2o0 チャーリー
o3o0 デーブ
o4o0 エミリー
```

👆　この出席番号の１番目と２番目の間にボブを加えたいとき  

```plaintext
o1o0 アリス       -->  o1o0 アリス
                  -->  o1o5o0 ボブ
o2o0 チャーリー   -->  o2o0 チャーリー
o3o0 デーブ       -->  o3o0 デーブ
o4o0 エミリー     -->  o4o0 エミリー
```

👆　1.5 の感覚で、既存の人の出席番号を変えず、ボブだけを順番の中に組み込めました  

👇　では次に、アリス（Alice）とボブ（Bob）の間にアン（Ann）が６人転入してきたケースも考えてみましょう  

```plaintext
o1o0 アリス       -->  o1o0 アリス
                  -->  o1o_9o0 アン
                  -->  o1o0o0 アン
                  -->  o1o1o0 アン
                  -->  o1o2o0 アン
                  -->  o1o3o0 アン
                  -->  o1o4o0 アン
o1o5o0 ボブ       -->  o1o5o0 ボブ
o2o0 チャーリー   -->  o2o0 チャーリー
o3o0 デーブ       -->  o3o0 デーブ
o4o0 エミリー     -->  o4o0 エミリー
```

👆　いくつかの挿入方法がありますが、アンを６人追加できました。 `_9` というのは、私たちが使いなじんだ数では `-1` であることに注意してください  

また、 `o1o0 アリス` と `o1o0o0 アン` の出席番号をご覧ください。末尾に `o0` が常に付くというルールがあることにより、０番を後から追加したいが既存の 0番 と被ってしまう、という問題が回避されています  

👇　では、２人目のアンと３人目のアンの間に、またアンを挟んでみましょう

```plaintext
o1o0 アリス       -->  o1o0 アリス
o1o_9o0 アン      -->  o1o_9o0 アン
o1o0o0 アン       -->  o1o0o0 アン
                  -->  o1o0o5o0 アン
o1o1o0 アン       -->  o1o1o0 アン
o1o2o0 アン       -->  o1o2o0 アン
o1o3o0 アン       -->  o1o3o0 アン
o1o4o0 アン       -->  o1o4o0 アン
o1o5o0 ボブ       -->  o1o5o0 ボブ
o2o0 チャーリー   -->  o2o0 チャーリー
o3o0 デーブ       -->  o3o0 デーブ
o4o0 エミリー     -->  o4o0 エミリー
```

👆　問題なく挟めました。私たちが慣れ親しんだ数字でいうと 1.0.5 といったような感覚です  

👇　出席番号が２桁になったときは、以下のような手間が発生するので注意してください  

```plaintext
o1o0
o2o0
o3o0
oa10o0
```

👆　数字が１桁増えるたびに頭に `a` が付きます。 100 なら `oaa100o0` です。そう、数字の桁数は私たちが使い慣れている数字よりおよそ２倍の文字数になりがちです。これは辞書順を従えさせるためのコストです  

```plaintext
o1o0
O1o0
O1O0
```

👆　lowercase、PascalCase、UPPERCASE のいずれでも構いません  

```plaintext
MAX_NUMBER_O1O0 = 99

class HelloWorldO1o0():
    def print_o1o0(self):
        pass
```

👆　リナンバリンギスト番号は、プログラム中でもなるべく記入できることを念頭に置いています  

# 解説

じゃあ出席番号に 1.0.5 や 1.5 を使えるようにすればいいだけではないのか？　と思われるかもしれません  

```plaintext
1 アリス
1.0.5 アン
1.5 ボブ
2 チャーリー
```

👆　最近はソフトウェアも賢くなって、Visual Studio Code のエクスプローラーなどでは数字順に並んでくれます  

```plaintext
1.0.5 アン
1.5 ボブ
1 アリス
2 チャーリー
```

👆　そうでないケースでは、ASCII コード順に並んでしまうことがあります  

```plaintext
o1o0 アリス
o1o0o5o0 アン
o1o5o0 ボブ
o2o0 チャーリー
```

👆　リナンバリンギスト番号は、賢くなく ASCII コード順に並ぶソフトウェアでも順番通りに並んでくれます  

同様に、次の問題もリナンバリンギスト番号は解決してくれます  

```plaintext
10
1
2
3
```

👆　賢くないソフトウェアでは、 1 より前に 10 が並ぶケースはあるでしょう  

```plaintext
o1o0
o2o0
o3o0
oa10o0
```

👆　リナンバリンギスト番号は、賢くなく ASCII コード順に並ぶソフトウェアでも順番通りに並んでくれます  

負数では `_9` のようにアンダースコアを使いますが、コーディング規約などでアンダースコアを使ってはいけないケースもあるかもしれません。その際は、並び順が崩れてしまいますがアンダースコアの代わりに前ゼロを使って `09` で我慢しましょう  

# Python スクリプトの書き方

```py
import renumnum as rn

vec = rn.vec(1)
print(str(vec))     # O1o0

vec = rn.vec(1, 2)
print(str(vec))     # O1o2o0

vec = rn.vec('1')
print(str(vec))     # O1o0

#vec = rn.vec('1, 2')   # error

# 辞書順番号（Dictionary order number）。末尾に o0 が付いていなくても、辞書順番号として有効です
vec = rn.vec('_9')
print(str(vec))     # O_9o0

vec = rn.vec('A10')
print(str(vec))     # OA10o0

# 数珠玉記数法（Beads notation）。末尾に o0 が付いていなくても、数珠玉記数法として有効です
vec = rn.vec('O0')
print(str(vec))     # O0o0

vec = rn.vec('O1')
print(str(vec))     # O1o0

# リナンバリンギスト番号
vec = rn.vec('O0o0')
print(str(vec))     # O0o0

vec = rn.vec('O1o0')
print(str(vec))     # O1o0
```
