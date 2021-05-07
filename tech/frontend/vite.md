---
title: Vitejs について
head:
  - - meta
    - name: og:title
      content: 技術メモ | Vitejs について
  - - meta
    - name: twitter:title
      content: 技術メモ | Vitejs について
  - - meta
    - name: og:description
      content: 技術メモ | Vitejs を普段使ったりドキュメントの日本語翻訳チームを作ったりしてるので布教の意味も込めて
  - - meta
    - name: twitter:description
      content: 技術メモ | Vitejs を普段使ったりドキュメントの日本語翻訳チームを作ったりしてるので布教の意味も込めて
  - - meta
    - name: og:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:Vitejs について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
  - - meta
    - name: twitter:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:Vitejs について/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
---

# {{ $frontmatter.title }}

Vitejs はいいぞって話です。実際にこのサイトも Vitejs を使用しています。  
<!-- nuxt/vite とかいうプラグインがあったので素振りするために Nuxt で pornhub API を叩いて素振りをしたところ vercel にデプロイできなくて死んだのでそれ以来このプラグインは触っていません。（ぇ -->

## Vitejs ってなに

[Vitejs](https://vitejs.dev/) は [Vuejs](https://vuejs.org/) の作者の [Evan You 氏](https://evanyou.me/) が開発しているノーバンドルなビルドツールです。  
よく使いますが、開発者体験が非常に良いです。  
開発時にはバンドル不要でサーバが立ち上がるため、起動や更新が非常に高速です。また、HMR がモジュールの総数と切り離されているため、規模に関わらず高速に動作します。スターターがあったり最近だとプラグインが充実してきて、正式リリースもされたのでプロダクション環境で使用している人も Twitter で数人観測しています。  
  
また、Evan You 氏がメインとして作成していますが vue-cli の代用や for Vue のような位置付けではなく preact, React, svelte, lit-element などのスターターが用意されていることからもっと広い目で見てるバンドルツールということが窺えます。  


## ノーバンドルツールってなに
 
最近のフロントエンドのツールはここら辺にフォーカスしてきている気がしています。Vitejs や [Snowpack](https://www.snowpack.dev/) が最たる例であり、非常に高速で動作するため開発者体験が良いとされています。  
最近のブラウザは Native ESM を実行できるようになっているため開発環境では localhost にアクセスすることで高速に直接モジュールを import して外部ライブラリだけ事前にコンパイルしておくという手法が最近の流行りな気がしています。逆にプロダクション環境ではこれまで通りのビルドをするといった感じです。  
これがノーバンドルツールです。  
  
ここら辺については作者の Evan You 氏が [アルキメデスみたいなこと言ってます（？）](https://twitter.com/youyuxi/status/1252173663199277058?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1252173663199277058%7Ctwgr%5E%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fqiita.com%2Fryo2132%2Fitems%2Fc1530dd590e34e68c494)。おもしれ〜。
  
## Vitejs の特徴

とても速いです。

## 使ってみて

明らかに開発者体験が良いです。大きなプロジェクトで使ったことがないのでなんとも言えないのですが、少なくとも webpack よりは良いと思います。先日、初めて Next.js の `create-next-app` を使用したのですがサーバの起動や差分検知による HMR の動作が非常に遅く感じました。また、現在仕事で webpack を使うこともあるのですがそこに比べて圧倒的にビルドのスピードが違うように感じます。（もちろん規模の違うプロジェクトで比較するのはナンセンスですが）  
  
webpack は確かバンドルする過程でメモリ上にファイルを置いていたりして開発環境を圧迫してしまうみたいな点があるのですが、そこも Native ESM の直接的な import によって解決されていてとても良いと感じました。  
また、最近だと Rollup のプラグインそのまま使えたり、vite 用のプラグインが充実してきたりしているため新しいツールとはいえ意外と痒いところに手が届いたりするのかなと思います。config では esbuild の書き方を採用したりもできるのでとても使い勝手が良い、最近のバンドルツールという印象を受けました。  
  
ただ、まだ完璧とは言えず、上でプロダクション環境で使用している人をちらほら観測してると言いましたが、おそらく部分的な導入になっているのかなと思います。  
  
<!-- 
## snowpack との違い
 -->

