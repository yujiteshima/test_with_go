# Gopher道場申し込み時課題
1. Gopher道場に応募した理由とGopher道場で学びたいことについて教えて下さい。
2. プログラム問題
3. プログラム問題を解く上で工夫した点や難しいと感じた点

## プログラム問題の内容

以下のように、英語の文章を標準入力から受け取って単語の数を数え、多い順でTOP3を出力するプログラムを作成せよ。

```bash
$ echo "dog dog dog. cat. fish fish. go go go go." | wordcount
go
dog
fish
```

単語の区切りはスペースまたは改行(\n)とする。なお、連続するスペースや改行、文末のドット(.)やカンマ(,)は無視し、その他の記号や英語のアルファベット以外の文字は含まれないものとする。
与えられる英語の文章の長さは決まっておらず、任意の長さの文章を処理できるようにすること。また同じ数の英単語がTOP3に入っている場合は、アルファベット順に出力すること。

問題から分かること、考えなくてはならないこと。

- 単語の区切りはスペースまたは改行。
スペースか改行までを一区切りに、単語の情報を記録しておかなくてはならない。
単語の情報とは、文字列と今までの出現回数か？
次の単語がきたら、以前登録しておいた、単語の中に同じものがないか確認、同じものがあれば出現回数をインクリメントする。
同じものが無ければ新規登録する。最後にベスト３を出力する。同じ順位の英単語がトップ３に入っている場合は、アルファベット順に出力する。

- 連続するスペースや改行、文末のドット、カンマは無視し、その他の記号や英語のアルファベット以外の文字は含まれないものとする。
スペースで次の英単語を確認した際に、ドットとカンマはトリムする。次の英単語がくるまで、スペースや改行は処理をパスしループさせる。

- 与えられる英語の文章の長さは決まっておらず、任意の長さの文章を処理できるようにすること。
文の終わりをどのように認識するかを調べなくてはならない。
処理の方針として、すべての文章を一度読み込んで、スペース、改行で単語を分け、リストに格納し、その後カウントするのか、
一つずつ処理しながら、リストにつめていくのかどの方法が良いのか考えなくてはならない。
並行処理を使えるのか。様々なパターンで実装し、パフォーマンス計測して、工夫した点で書く。

タスク
1. 処理のフロー図を書く(複数)
2. "文字数カウント　並行処理"等でググって情報収集
3. パフォーマンス計測方法を確認する。
4. テストを書くために調べる。
5. 文末の終わりの認識方法を調べる。

https://qiita.com/riotam/items/63b3675886e510ee73c9
上記記事で文字数カウントについて書かれている。

A Tour of Go/Exercise: Maps
で同じような課題が出されているもよう。これをググればさらに、情報が得られそう。

複数デリミタで、分割する方法については、下記のようにFieldsFunc（input， Split）を使う例が
書かれていた。これがピッタリだと思える。しかし、連続でスペースが入るケースや、連続で改行が入るケースでは、
どのような動きになるかためしてみなくてはならない。

```go
package main 

import (
    "fmt" 
    "strings" 
) 

func main() { 
    input := `xxxxx:yyyyy:zzz.aaa.bbb.cc:dd:ee:ff` 
    a := strings.FieldsFunc(input, Split) 
    t := Target{a[0], a[1], a[2], a[3], a[4], a[5], a[6]} 
    fmt.Println(t) // {xxxxx yyyyy zzz aaa bbb cc dd} 
} 
func Split(r rune) bool { 
    return r == ':' || r == '.' 
} 

type Target struct { 
    Service string 
    Type string 
    Domain string 
    Plan string 
    Host string 
    Region string 
    Other string 
} 
```

qiitaの記事のサンプルコード

```go
package main
import (
    "fmt"
    "strings"
)

func WordCount(s string) map[string]int {

    words := strings.Fields(s)
    m := map[string]int{}
    count := 0

    for _, v1 := range words {

        for _, v2 := range words {

            if v1 == v2 {
                count++
            }

        }
        m[v1] = count
        count = 0

    }
    return m
}

func main(){
    fmt.Println(WordCount("I have a pen"))
}
```

```console
>> map[I:1 a:1 have:1 pen:1]
```

mapの使い方を理解していく中で、ソートを考える。