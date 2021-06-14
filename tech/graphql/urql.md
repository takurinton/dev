---
title: urql について
head:
  - - meta
    - name: og:title
      content: 技術メモ | urql について
  - - meta
    - name: twitter:title
      content: 技術メモ | urql について
  - - meta
    - name: og:description
      content: 技術メモ | 自作 GraphQL client を実装しようと思い、urql を参考にしようと思ったけど基礎知識が足りてないと感じたのでメモ
  - - meta
    - name: twitter:description
      content: 技術メモ | 自作 GraphQL client を実装しようと思い、urql を参考にしようと思ったけど基礎知識が足りてないと感じたのでメモ
  - - meta
    - name: og:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:urql について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
  - - meta
    - name: twitter:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:urql について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
---

# {{ $frontmatter.title }}

自作 GraphQL client を実装しようと思い、小さめライブラリの urql を参考にしようと思ったけど基礎知識が足りてないと感じたのでメモする。  
そもそもこれを知った理由として、preact のコアコミッターの [Jovi 氏](https://twitter.com/JoviDeC) が urql のコアコミッターでもあることがあり、初めて GraphQL を使ったときも preact と一緒にこれを使用した。

## urql とは

GraphQL クライアント、Apollo よりも軽量でシンプルなキャッシュの仕組みが提供されてるらしい。  
express の middleware に似たような Exchange による拡張性が提供されてる（ここがいまいち理解できなかった）  
外部のライブラリとして、stream を扱う [wonka](https://wonka.kitten.sh/) と [@graphql/typed-document-node](https://github.com/dotansimha/graphql-typed-document-node) を使用してる。  
  
## 使い方

client を作成して Provider で囲うだけです。  
Apollo でいう `ApolloClient` は `createClient` となっています。

```js
import { createClient } from '@urql/core';

const client = createClient({
  url: 'http://localhost:3000/graphql',
});
```

1回のみクエリを送信したい場合は以下のようにします。  
`client.mudation` にすることで mutation を送信することもできます。

```js
const QUERY = `
  query Test($id: ID!) {
    getUser(id: $id) {
      id
      name
    }
  }
`;

client
  .query(QUERY, { id: 1 })
  .toPromise()
  .then(result => {
    console.log(result); // { data: ... }
});
```

キャッシュをクエリから同期的に読み込むには `readQuery` を使用します。  

```js
const QUERY = `
  query Test($id: ID!) {
    getUser(id: $id) {
      id
      name
    }
  }
`;

const result = client.readQuery(QUERY, { id: 1 });

result; // null or { data: ... }
```

## キャッシュの仕組み

urql は [documeht caching](https://formidable.com/open-source/urql/docs/basics/document-caching/) という仕組みがある。  
全てのクエリと変数の組み合わせはハッシュ化され、レスポンスと共にキャッシュされる。  
コードで言うと [ここらへん](https://github.com/FormidableLabs/urql/blob/main/packages/core/src/utils/stringifyVariables.ts)  
WeakMap を用いてキャッシュが保持され、ハッシュとして格納されている。  
ハッシュには [djb2](http://www.cse.yorku.ca/~oz/hash.html) が使われている。  
  
つまり、1度キャッシュされたクエリと同じクエリの場合はリクエストを送らずにキャッシュされているデータを返すといった風になっている。  
また、__typename が同じ mutation が送られた時にはリソースの更新とみなされ、キャッシュがクリアされる。  
`stringifyVariables.ts` で　`stringifyVariables` と `stringify` がそれぞれ実装されてるのはここらへんの仕組みが主な理由。  
  

## urql のキャッシュ

urql は大きくわけて4つのキャッシュコントロールが実装されている。  

| ポリシー | 内容 | 用途 |
| :--- | :--- | :--- |
| cache-first | 1回目のリクエストでキャッシュをして、キャッシュがあればリクエストは投げずそのキャッシュを返す | 変更の少ないリソースへのアクセス |
| cache-only | リクエストを投げずにキャッシュのみを返す | 普通は使わないわね...。 |
| network-only | cache-only の逆。キャッシュは使わずに常にリクエストを投げる | 変更の多いリソースへのアクセス |
| cache-and-network | キャッシュがあればキャッシュを返し、その後リクエストを投げる | 頻繁には変更がされない、早く見せたいリソースへのアクセス |

このようになっている。それぞれの使い方について理解したい。

## Exchange 

ここがわからなかった、実装してて謎になってしまった。（まだ実装できてないんだけど）  
どうやら urql ではリクエスト関数を operation という単位で扱う（最初 operation ってなんだって思いながらコード書いてた）  
このライフサイクルはリクエストを投げてから変えてくるまでの双方向の流れを stream 的に扱う、このための wonka。  

urql のキャッシュは Exchange を使用して全て設定することができる。  
上でも述べたが、`cacheExchange` は document cache を提供する。  
urql の正規化されたキャッシュは `@urql/exchange-graphcache` として提供されている。  
  
  
Exchanges には以下の特徴がある。  

- 正規化されたキャッシュ
- カスタムのキャッシュとその解決
- Subscription と Mutation によるキャッシュの更新
- 最適化された Mutation の更新
- スキーマの認識
- オフラインサポート
- エラーと警告

長くなりそうなので [Graphcacheについて](./graphcache) という別記事にする。