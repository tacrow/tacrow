---
title: AMP対応したときのエラー
date: "2017-03-27T00:00:00.00Z"
layout: post
path: "/amp-error/"
categories: ["Frontend", "AMP", "CSS"]
description: "AMP対応をしていたら、腑に落ちないエラーに遭遇したという小話"
---

## AMP対応
[AMP](https://www.ampproject.org/ja/)とは、Accelerated Mobile Pagesの略です。  
Googleが推し進めている、モバイル端末でのウェブページ高速表示プロジェクトです。  
最近SEOに関連して記事などのページでよくこの対応します。

## AMPでのCSS読み込み
AMPではCSSファイルを読み込むとエラーになります。  
```
<style amp-boilerplate>body{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}</style><noscript><style amp-boilerplate>body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}</style></noscript>
<style amp-custom>インラインのCSS</style>
```
このように、***style amp-boilerplateタグ***を先に記述し、  
その後の***style amp-customタグ***の中で***インライン***でCSSを記述します。

style amp-customタグで読み込むインラインのCSSには、容量制限（***50,000B = 50KB***）があります。  
インラインで管理するのはとても面倒かつ圧縮もしなければならないので、管理としては普通にファイルとして管理し、PHPなどでstyle amp-customタグ内にインラインに書き込むのが一般的のような気がします。

## エラー
さて問題のエラーです。  
AMP Validatorで以下のエラーがずっと表示され一向に解決しない。  
```
CSS syntax error in tag 'style amp-custom' - invalid declaration. Debug.
```
どうやらstyle amp-customタグ内のCSSに関するエラーのようなのですが、  
特に問題がないように見受けられます。

有益な情報も見つからない感じだったので、  
CSS圧縮を[cssnano](https://github.com/ben-eb/cssnano)から[gulp-cssmin](https://github.com/chilijung/gulp-cssmin)に変更してみたら、キレイにエラーが消えました。

CSS圧縮の方式によってはエラーになるようです。

僕の勉強不足なのか、日頃の行いが悪いのか、腑に落ちない。
