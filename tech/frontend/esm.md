---
title: ESM について
head:
  - - meta
    - name: og:title
      content: ESM について
  - - meta
    - name: twitter:title
      content: ESM について
  - - meta
    - name: og:description
      content: 恥ずかしながら ESM についてしっかり理解していなかったので MDN を中心に読んで理解を深める
  - - meta
    - name: twitter:description
      content: 恥ずかしながら ESM についてしっかり理解していなかったので MDN を中心に読んで理解を深める
  - - meta
    - name: og:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:ESM について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
  - - meta
    - name: twitter:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:ESM について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
---

# {{ $frontmatter.title }}

恥ずかしながら ESM についてしっかり理解していなかったので MDN やその周辺のドキュメントや記事を読んで理解を深めます。  


## ESM の歴史

昔々の JS は必要に応じてサイトをいじる、独立的な処理を書く程度の程度の小さいものでした。  
しかし、それからしばらくして JS は大量のスクリプトやモジュールを管理しなければなりません。また、Web サイトに限らず Node.js などの他の場面で実行することも多々あります。  
そのため、近年はモジュールに分割して必要な時にインポートできるような仕組みの提供が検討されるようになってきました。  
Node.js は長年この機能を提供しており、モジュールの利用を可能にするライブラリーやフレームワークも数多くあります。（Commonjs, webpack, babel ...など）  
  
そして、近年、モダンなブラウザはこのモジュールを扱う機能がサポートされています。ブラウザはモジュールの読み込みを最適化できるので外部ライブラリを使用したクライアント側の無駄な処理を行うよりも効率的にすることができます。  
ブラウザの ESM のサポート状況は以下のようになります。

import の場合  

![esm_import](/public/import_esm.png) 

export の場合  

![esm_export](/public/export_esm.png)  

IE 以外のブラウザは import/export できるみたいです。ただ、一部対応してない機能などがあるのでそこは注意したいです。


## ESM とは
ES2015 から仕様に入ったモジュールシステムで、みんながよく使ってるやつです。  
`import` して読み込み、`export` して吐き出すあれです。  
書き方の例としては以下のようになります。  

```js
import hoge from 'hoge';
import * as fuga from 'fuga';

export function func() {}
export class className {}
```


特徴としては以下のようなものがあります。  

- import / export はトップレベルのみでしか宣言できない（もし非同期にしたかったら dynamic import を使用する）
  - これにより実行前にエラーを発見することが可能
- import は hoisting される
  - どこに書いても宣言がモジュールの最初で行われます
- トップレベルの this は undefined になる
- モジュールは strict mode になる

  
ブラウザに ESM だということを伝えるには以下のように記述します。  

```html
<script type="module" src="main.js"></script>
```

ESM をサポートしていないブラウザの場合、`type="module"` がないので同じ記述をすると `type:text/javascript` として扱われます。  
  



## `.mjs` という拡張子

`.mjs` という拡張子についてちょこちょこ見かけますが、これを指定することによってどのファイルがモジュールで、どのファイルが通常の JS であるかを明確にすることができます。  
これにより、Node.js のようなランタイムや bable のようなビルドツールで、モジュールファイルがモジュールとして解析されるようになります。  
  
ただ、これは今後 ESM がネイティブで導入されていったときに（ほぼ確実に導入される、てか上でも言ったけどブラウザではされてる）あまりうれしくありません。できれば `.js` という拡張子を維持した状態で ESM を使用したいと思うのは当然です。  
これは `package.json` に `{"mode": "esm"}` を記述することで解決することができます。これを書くことによりその `package.json` のの対象となるプロジェクトは `.js` でも ESM として扱われます。  
もし `package.json` が存在しない場合は `commonjs` として扱われます。


