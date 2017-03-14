---
title: React.js製 静的サイトジェネレーターGatsbyを利用してブログを構築してみる
date: "2017-03-14T00:00:00.00Z"
layout: post
path: "/gatsby-usage/"
category: "Front-end, JavaScript, Gatsby, React.js"
description: "このサイトは、GatsbyというReact.js製の静的サイトジェネレーターで作っています。 ここでは、このサイトを構築するまでの手順を書いていきます。"
---

## Gatsbyとは
[Gatsby](https://github.com/gatsbyjs/gatsby)は、[React.js](https://facebook.github.io/react/)で作られた静的サイトジェネレーターです。  
記事は、マークダウン形式で記述します。  
React.js製ということもあり面白そうだと思って選択しました。  
名前だけだと、某デオドラントスプレーを思いうかべちゃいますね。

## 導入方法
Node.jsとnpmが既に入っていることが前提です。

インストール  
``
$ npm install -g gatsby
``

プロジェクト作成  
``
$ gatsby new project https://github.com/gatsbyjs/gatsby-starter-blog
``

プロジェクトに移動  
``
$ cd project
``  

開発開始  
``
$ gatsby develop
``

***localhost:8000*** にアクセスするとページが確認できます。  
ライブリロードが有効になっているので変更を保存するとリアルタイムで変更が反映される。


## テーマの変更

[ドキュメント](https://github.com/gatsbyjs/gatsby#gatsby-starters)にも書いてありますが、現状ですと8種類のテーマが用意されているそうです。  
ここでは[Gatstrap](https://github.com/jaxx2104/gatsby-starter-bootstrap)【[Demo](https://jaxx2104.github.io/gatsby-starter-bootstrap/)】を利用します。

プロジェクト作成の際に以下を叩きます。  
``
$ gatsby new project https://github.com/jaxx2104/gatsby-starter-bootstrap
``

CSSは少しカスタマイズしています。


## 各ディレクトリ&ファイルについて
- config.toml：設定ファイル
- components/：コンポーネントディレクトリ  
- pages/pages/：ページディレクトリ  
- pages/articles/：記事ディレクトリ  
- static/：CSSやSCSS、fontsなど


## Github Pagesに公開する
Githubリポジトリを作り、gh-pagesブランチを設定します。  
***condig.toml***の***linkPrefix***にプロジェクト名を登録して以下を実行します。  
``
$ npm run deploy
``

実行すると、package.jsonの以下が実行されて、gh-pagesブランチでサイトが構築&更新されます。
``
$ gatsby build --prefix-links && gh-pages -d public
``

本当は、masterブランチでdocs/以下に構築して欲しいんですが、デプロイでコケたのでいったん公式通りにやってみました。

以上となります。

徐々に探っていきます。