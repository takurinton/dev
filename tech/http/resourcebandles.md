---
title: リソースバンドラについて
head:
  - - meta
    - name: og:title
      content: 技術メモ | リソースバンドラについて
  - - meta
    - name: twitter:title
      content: 技術メモ | リソースバンドラについて
  - - meta
    - name: og:description
      content: 技術メモ | Web ページのサブリソースを一つにまとめる resourcebundles についてまとめる
  - - meta
    - name: twitter:description
      content: 技術メモ | Web ページのサブリソースを一つにまとめる resourcebundles についてまとめる
  - - meta
    - name: og:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:リソースバンドラについて/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
  - - meta
    - name: twitter:image
      content: https://res.cloudinary.com/dtapptgdd/image/upload/w_1000/l_text:Sawarabi Gothic_70_bold:リソースバンドラについて/v1620370500/Screen_Shot_2021-05-07_at_15.54.47_extlvu.png
---

# {{ $frontmatter.title }}

JS のバンドラ作ったら HTTP のリソースのバンドラについても興味が出てきてしまった。  
[WICG/resource-bundles](https://github.com/WICG/resource-bundles) を中心にインターネット上に落ちている記事を読んでそれをまとめる感じにする。  

## resource bundles とは

resource bundles とは、Web ページのサブリソースを一つにまとめるものである。  
