---
title: isset と empty について
head:
  - - meta
    - name: og:title
      content: isset と empty について
  - - meta
    - name: twitter:title
      content: isset と empty について
  - - meta
    - name: og:description
      content: PHP の isset と empty についての違いは何か
  - - meta
    - name: twitter:description
      content: PHP の isset と empty についての違いは何か
  - - meta
    - name: og:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:isset と empty について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
  - - meta
    - name: twitter:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:isset と empty について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
---

# {{ $frontmatter.title }}

PHP にはクソみたいな混同しがちなクソみたいな組み込み関数がたくさんあります。isset と empty も例外ではありません。  
こいつらの違いや挙動、使い分け、また周辺の関数への理解が PHP 歴1ヶ月の僕には難しかったのでとりあえず書いて覚えるみたいなことをします。  

## isset
まず [isset](https://www.php.net/manual/ja/function.isset.php) について。  

> 変数がセットされているかを調べます。 これは変数が宣言されており、 null とは異なる値だということです。

らしい、とても良いと思います。  
PHP はエラーレベルを指定できるため、undefined でもエラーを吐かないことも可能なのですがどうせテストで引っかかるのでここで変数チェックをすることでそのエラーを避けることができます。  

```php
$name = 'takurinton';
if (isset($name)) {
    echo 'hi I am ' . $name;
}
```

こんな感じで使えます。`$name` が undefined だったら何も出力されません。

## empty 
[empty](https://www.php.net/manual/ja/function.empty.php) についてです。   

> 変数が空であるかどうかを検査します

らしい。つまり定義だけして代入してなかったら `false` が返ってくるみたいな感じです。

てことは、クラスの中でフィールドに初期値を定義してそれをコンストラクタで初期値代入できているかどうかのチェックをするときとかに使えそう？

```php 
class SimpleClass
{
    private $var;
    
    function __construct(string $name) {
        $this->var = $name;
    }

    public function myFunc() {
        if (empty($this->var)) {
            echo 'variable is empty';
        }
        echo $this->var;
    }
}
```

まあこんな書き方しないけどパッと思いつくのがこれしかなかった。  

## これの何がクソなのか

これ適切に管理しないとクソコードが完成するので非常に体験が悪くなります。コード規約とか作った方がいい。  
結局は PHP がクソってよりは自分がクソって話なのですが、例えば自分が見たコードではこんな感じのがありました。  
(正直観測したコードがクソなだけってのはあるので突っ込まないで泣いちゃう)

```php
$hoge = env('HOGE');
if (!isset($hoge)) {
    処理
}

$fuga = env('FUGA')
if (empty($fuga)) {
    処理
}
```

みたいな感じです。やってることはほぼ同じなのですが、どちらかに統一した方が絶対良いし、それぞれ意味が逆なので `!` が発生してあまり嬉しくないです。  
中身のコードを読む感じでもこの2つを使い分ける必要はなさそうで、どっちも `isset` にした方が良いのではないかなあとか思って見てました。

## 他にもあるぞこの類の関数
PHP ってこういう関数がゴロゴロあるんですよね。良くも悪くもな気がしてます。   
多くのサポートが受けられるのはいいことだけどそれによって管理がしにくくなっては意味がないので。  

| 式 | gettype() | empty() | is_null() | isset() | bool: if($x) |
| :--------- | :--------- | :--------- | :--------- | :--------- | :--------- |  
$x = ""; | string | true | false | true | false
$x = null; | NULL | true | true | false | false
var $x; | NULL | true | true | false | false
$x が未定義 | NULL | true | true | false | false
$x = array(); | array | true | false | true | false
$x = array('a', 'b'); | array | false | false | true | true
$x = false; | bool | true | false | true | false
$x = true; | bool | false | false | true | true
$x = 1; | int | false | false | true | true
$x = 42; | int | false | false | true | true
$x = 0; | int | true | false | true | false
$x = -1; | int | false | false | true | true
$x = "1"; | string | false | false | true | true
$x = "0"; | string | true | false | true | false
$x = "-1"; | string | false | false | true | true
$x = "php"; | string | false | false | true | true
$x = "true"; | string | false | false | true | true
$x = "false"; | string | false | false | true | true

上で紹介したやつ以外に `is_null` という関数や `gettype` という関数があります。  
それぞれその名の通りだなという感じなので省略。ここら辺を適切に使用していけるようにしていきたいです。

## まとめ
別に PHP を否定しているわけではなくて、管理を間違うと死ぬぞみたいな話。  
令和時代のレガシーなコードは PHP で書かれていることが多く、保守運用やリファクタ、場合によってはリプレイスが必要になりますがそのような時に適切に処理を追うことができるようにそれぞれの関数の意味を適切に理解していきたいです。  
また、これから新しく書くときも混同しづらい表現を使ったり必要ならコメントを書いたりして対処していきたいです。  
