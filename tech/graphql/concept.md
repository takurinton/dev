---
title: GraphQL に入門した（概念）
head:
  - - meta
    - name: og:title
      content: GraphQL に入門した（概念）
  - - meta
    - name: twitter:title
      content: GraphQL に入門した（概念）
  - - meta
    - name: og:description
      content: GraphQL に入門しました。考え方や基本のお作法についてまとめています。
  - - meta
    - name: twitter:description
      content: GraphQL に入門しました。考え方や基本のお作法についてまとめています。
  - - meta
    - name: og:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:GraphQL に入門した（概念）/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
  - - meta
    - name: twitter:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:GraphQL に入門した（概念）/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
---

# {{ $frontmatter.title }}

## はじめに
GraphQL に入門したのでまとめます。  
ここでは Query と Mutation については触れますが Subscription については触れません。（これで入門したとか言うな）  
一応 Subscription については後日追記予定です。  
手を動かした時間で言うと10時間もないのでそこまで深くやっているわけではありません。  
一旦ここにまとめてから深めて行きたいなと思っています。


## 勉強するときに参考にしたサイトなど

概念のお勉強は書籍を使った方がいいと思ってる人間なのですが、今回はネット上のものだけを拾いながら勉強しました（辛い）  
参考にしたサイトはいかです。あとは手を動かして頑張りました。  

- [GraphQLに入門する - Qiita](https://qiita.com/jintz/items/c9105dca1725224d36a8)
- [GraphQL入門 - Zenn](https://zenn.dev/yoshii0110/articles/2233e32d276551)
- [graphql-go/graphql](https://github.com/graphql-go/graphql)
    - [expamles](https://github.com/graphql-go/graphql/tree/master/examples)
  
ここら辺を参考にしました。  
最初は何言ってるかわからなかったのでちょこちょこ行ったり来たりしながらやりました。辛かった〜〜。  


## 基本的なところ
まず導入としてここがよくわかりませんでした。  
GraphQL にはどうやら `Query` と `Mutation` というものがあるということで何してるかわからなかったです。ここに一番時間使った気がします。  

- 共通の考え方
    - 基本的には POST リクエストの body に情報を持たせることが多いらしい
    - GET のクエリパラメータとして情報を持たせることもできる
- Query
    - 情報を取得する際に使用する
- Mutation
    - 情報を送る際に取得する

こんな感じです。  
基本的にはこれだけでいいと思います。あとはやって覚えたほうが早い気がする。  
  
## リクエストを送る形式について

次にリクエストを送る形式についてを説明します。  
GraphQL は REST とはだいぶ異なる形でリクエストを送ります。  
REST では完全に JSON という形でしたが GraphQL ではそうではありません。
  
  
まず Query と Mutation の場合で書き方が違います（ほぼ同じだけど）  
ここではわかりやすいように以下のようなシンプルな JSON を使用して説明をしたいと思います。

```json
[
    {
        "id": 1,
        "name": "takurinton"
    }, 
    {
        "id": 2,
        "name": "hoge"
    }, 
    {
        "id": 3,
        "name": "fuga"
    }
]
```

#### 共通のこと

上で GraphQL は GET でも POST でもリクエストを送信できるという話をしました。  
イメージとしては、GET リクエストの場合はクエリパラメータとして、POST リクエストの場合は body として持たせてあげる感じです。  
REST の思想が強めの人はここで GET は取得、POST は作成という概念を消してください。（もっと言うと PUT, PATCH, DELETE のことも忘れてください）  
また、基本的にはエンドポイントは1つです、それも頭に入れておくと良きかもしれません。  
  

以下でちょこちょこ出てくる `なんとかUser` というのはアプリケーション側で任意の名前をつけることができるものです。  

　
#### Query の場合

Query の場合は以下のような書き方をします。  
まずは先ほどの JSON の内容を全て取得したいと思います。
  
GET リクエストの場合は以下のような形で投げることができます。

```bash
curl -g http://localhost:8888/graphql?query={getUsers{id,name}}
```

次に POST リクエストの場合は以下のような形で投げることができます。  

```bash
curl -X POST -H "Content-Type: application/json" --data '{ query { getUsers { id name } } }' http://localhost:8888/graphql
```

GET でも POST でもやってることは同じで、レスポンスは上の JSON が返ってきます。  
  
  
また、id が1のユーザーを取得するなどといった、可変の値を渡すこともできます。以下の POST リクエストのような形で渡してあげることができます。  

```bash
curl -X POST -H "Content-Type: application/json" --data '{ "query": " getUser(id:\"1\") { id name } " }' http://localhost:8888/graphql
```

これはアプリケーション側で `getUser` は引数を取るよということを明示的に定める必要があります。  
このような形で値を渡すこともできます。

  
#### Mutation の場合

Mutation は更新系の処理をしたい場合に使用します。  
REST でいう GET 以外の部分がここに含まれると思ってください。  
  
POST でリクエストを送ってみたいと思います。  
以下の例は先ほどの JSON に値を追加したい時に送る Mutation です。

```bash
curl -X POST -H "Content-Type: application/json" --data '{ "query": "mutation { createUser(id:4, name:\"hogehoge\") { id name } }" }' http://localhost:8888/graphql
```

登録したい内容を送信しないといけないので先ほど同様、可変の値を投げる必要があります。もしこれが DB アクセスをするような場合だと `id` はオートインクリメントになりそうだからいらないなあとか考えますね。  
GraphQL の基本的な形式はこのような形です。  
  
コードがないとわからないよという人は [GraphQL に入門した（Go）](/tech/graphql/base_server_go) を見てください。  


## レスポンスの形式

レスポンスの形式は返ってくる値は JSON ですが REST とは若干異なります。  
GraphQL のレスポンスの形式は以下のようになります。  

```json
{
    "data":{"任意の名前":{"key":"value",...},
    "errors": [ ... ]}
```

`data` はうまく行った時のレスポンスの値、`errors` は何かしらのミスがあったときにエラーを格納します。

例として、上の全件取得（Query）の場合のレスポンスは以下のようになります。  

```json
{"data":{"getUsers":[{"id":1, "name": "takurinton"},{"id":2, "name": "hoge"},{"id":3, "name": "fuga"}]}
```

一番外側に `data` がついていて、任意の名前でラッピングされてるだけと思ったらそこまで難しくなく、普通の JSON じゃんと思うかもしれません。そうです。返ってくるのは JSON です。　　
あまり難しいことはしていないことがわかります。  
  
エラーの場合も同様に `errors` が返ってきます。例えば `query` か `mutation` かを明示的に定めないでリクエストを投げると以下のようなエラーが得られます。  

```json
{"data":null,"errors":[{"message":"Must provide an operation.","locations":[]}]}
```

エラーの中にはメッセージが格納されていて、何が悪いのかを教えてくれます。  
REST だと自分で丸めてた部分をやってくれてる感じです。便利。

## GET と POST の違い

上で GraphQL は GET と POST が使用できるという話をしましたが、これらの違いはなんなのでしょうか？  
[APOLLO DOCS POST and GET format](https://www.apollographql.com/docs/apollo-server/requests/) で触れられています。そもそもの形式が違うらしいです。  
また、GET を使う場合は URL をキャッシュしたいときなどに使用できるかもしれないなあなどと思いました。あまり詳しくないのでここらへん有識者の方アドバイスお願いします。


## application/json と application/graphql の違い

GraphQL でのやりとりは JSON を使用しているので HTTP header の `Content-Type` には `application/json` を使用します。しかし、どうやら `application/graphql` が使用できるらしいです。  
これは [iana.org の Media Types](https://www.iana.org/assignments/media-types/media-types.xhtml) で確認することができますが、`application/graphql` は含まれていません。つまりこれは標準ではなく GraphQL 独自の仕様ということがわかります。  
使い分けとしては、[GraphQL のドキュメント](https://graphql.org/learn/serving-over-http/) で触れられています。  

> If the "application/graphql" Content-Type header is present, treat the HTTP POST body contents as the GraphQL query string.

とのことなので、body を勝手に GraphQL として扱ってくれるみたいです。意図しない挙動を防ぐためにここら辺は理解したみがあります。


