# renumnum

**IPアドレスのv4形式** （例えば 128.0.0.1）や、**セマンティック・バージョニング** （例えば 1.0.0）といったピリオド区切りの数字（ベクターのようなもの）は便利ですが、このピリオド区切りの数字をもう少し拡張してみましょう。  
例えば、ピリオドを英字小文字の `o` に変えたり（128o0o0o1 とか 1o0o0）、その他いくつかのルールを加えると、さらに変わった性質を得ることができます。  

まず最初に、コンピューターを利用している場面で、スカラー数（ピリオドで区切っていない私たちがよく知っている 1 とか 2 といった数）を使っているときに**リナンバリングすると起こりやすい問題**を説明します。  
その次に、その問題をいくつか解決する性質をもったもの（名前は**リナンバリンギスト番号**、**renumnum**とでもしましょう）を説明します。  
最後に、 Python を使ってリナンバリンギスト番号を生成したり、パースしたりするスクリプトの書き方を解説します  

# 例：私たちのよく知っている数字を使った出席番号

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

👆　もともと２番以降の人たちを**リナンバリング**（＝番号を昇順に振り直すこと）する手間が発生します  

リナンバリングしても、番号を振り直す手間が発生しないような便利な数字があったらいいな、というような過激な思想をもった人たちを**リナンバリンギスト**（Renumberingist）とでも呼ぶとしましょう。  
そんなリナンバリンギストのための数字が**リナンバリンギスト番号**（Renumberingist Number）、**renumnum**です  

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

👆　"1.5" の感覚で、既存の人の出席番号を変えず、ボブだけを順番の中に組み込めました  

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

👆　いくつかの挿入方法がありますが、アンを６人追加できました。 `_9` というのは、私たちがよく知っている数では -1 であることに注意してください。  
覚え方としては、 0 から 1 を引くと 9 になって１桁下がる、という感じです。  
-9 なら `_1`、 -10 なら `__9` になります  

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

👆　問題なく挟めました。私たちがよく知っている数字でいうと "1.0.5" といったような感覚です  

👇　出席番号が２桁になったときは、以下のような手間が発生するので注意してください  

```plaintext
o1o0
o2o0
o3o0
oa10o0
```

👆　数字が１桁増えるたびに頭に `a` が付きます。 100 なら `oaa100o0` です。そう、桁数の多い数字は私たちが使い慣れている数字よりおよそ２倍の文字数になります。これは辞書順を従えさせるためのコストです  

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

# リナンバリンギスト番号の変数名は何でも構いませんがとりあえず oo としてみましょう
print("整数：1")
oo = rn.num(1)
print(str(oo))          # O1o0
print(oo.to_tuple())    # (1,)
print(oo.to_list())     # [1]

print("コンマ区切りの整数：1, 2")
oo = rn.num(1, 2)
print(str(oo))          # O1o2o0
print(oo.to_tuple())    # (1, 2)
print(oo.to_list())     # [1, 2]

print("タプル：(1, 2)")
oo = rn.num((1, 2))
print(str(oo))          # O1o2o0
print(oo.to_tuple())    # (1, 2)
print(oo.to_list())     # [1, 2]

print("リスト：[1, 2]")
oo = rn.num([1, 2])
print(str(oo))          # O1o2o0
print(oo.to_tuple())    # (1, 2)
print(oo.to_list())     # [1, 2]

print("文字列：'1'")
oo = rn.num('1')
print(str(oo))          # O1o0
print(oo.to_tuple())    # (1,)
print(oo.to_list())     # [1]

#oo = rn.num('1, 2')   # error

# 末尾に o0 が付いていなくても、辞書順番号として有効です
print("辞書順番号（Dictionary order number）：'_9'")
oo = rn.num('_9')
print(str(oo))          # O_9o0
print(oo.to_tuple())    # (-1,)
print(oo.to_list())     # [-1]

print("同：'A10'")
oo = rn.num('A10')
print(str(oo))          # OA10o0
print(oo.to_tuple())    # (10,)
print(oo.to_list())     # [10]

# 末尾に o0 が付いていなくても、数珠玉記数法として有効です
print("数珠玉記数法（Beads notation）：'O0'")
oo = rn.num('O0')
print(str(oo))          # O0o0
print(oo.to_tuple())    # (0,)
print(oo.to_list())     # [0]

print("同：'O1'")
oo = rn.num('O1')
print(str(oo))          # O1o0
print(oo.to_tuple())    # (1,)
print(oo.to_list())     # [1]

# リナンバリンギスト番号
print("リナンバリンギスト番号（Renumberingist number）：'O0o0'")
oo = rn.num('O0o0')
print(str(oo))          # O0o0
print(oo.to_tuple())    # (0,)
print(oo.to_list())     # [0]

print("同：'O1o0'")
oo = rn.num('O1o0')
print(str(oo))          # O1o0
print(oo.to_tuple())    # (1,)
print(oo.to_list())     # [1]
```
