---
title: Svelteについて
date: "2017-05-18T00:00:00.00Z"
layout: post
path: "/svelte-demo/"
categories: ["Frontend", "JavaScript", "Svelte", "Framework"]
description: "Svelteをドキュメントを見ながら少し触ってみたという話"
---
<div class="kv-box border">
   <img src="./kv.png" alt="Svelte">
</div>

## Svelteとは
[Svelte](https://svelte.technology/)は、2016年11月に出たばかりのJavaScriptフレームワークです。  
Backbone, Angular, React, Vueなど様々なJavaScriptフレームワークが出ていますが、Svelteはそれらとはコンセプトが違います。  
一言で表わすと***コンパイラ***のようなフレームワークです。

例えば、(Vue.js+Vuex or React.js+Redux)+ES2015+WebpackなどでSPAを開発したとすると、書いたコードがブラウザで動くようにビルドしなければならず、当然そのビルドをするための設定コードをプロジェクト立ち上げ時に整備する必要があります（なかなか骨が折れる作業です）。  
また、フレームワーク側が複雑な処理を担当しているため、自分が書いたもの以外の必要なコードが大量に追加されます。


対して、SvelteはHTMLファイルをUIコンポーネントとして実装していき、まとめて***素のJavaScript***に変換するだけです。変換されたJavaScriptファイルは、外部フレームワークを読み込みません。  
シンプルで楽ですね。

## 導入
svelte-cliをインストール  

``npm install -g svelte-cli``  
または、  
``yarn global add svelte-cli``

## 基本の使い方
プロジェクト作成して、以下のようなファイルを作る。  
Svelteでは、HTMLファイルにテンプレートとロジックを書いていくのが基本です。
```
<!-- index.html -->
<!DOCTYPE html>
<html lang="ja">
<body>
  <div id="app"></div>
  <script src="App.js"></script>
  <script>
    new App({
      target: document.getElementById('app'),
      data: { name: 'world' }
    });
  </script>
</body>
</html>
```  
```
<!-- App.html -->
<h1>Hello {{name}}!</h1>
```

``App.html``があるディレクトリに移動して以下を実行。    
``svelte compile --format iife App.html > App.js``  

``App.js``というファイルが出力され、  
``index.html``をブラウザで見ると「Hello world!」と表示されます。

変換された``App.js``は以下のような素のJavaScriptになります。

```
// App.js
var App = (function () { 'use strict';
function create_main_fragment ( state, component ) {
  var text_1_value;
  var h1 = createElement( 'h1' );
  appendNode( createText( "Hello " ), h1 );
  var text_1 = createText( text_1_value = state.name );
  appendNode( text_1, h1 );
  appendNode( createText( "!" ), h1 );

  return {
    mount: function ( target, anchor ) {
      insertNode( h1, target, anchor );
    },
    update: function ( changed, state ) {
      if ( text_1_value !== ( text_1_value = state.name ) ) {
        text_1.data = text_1_value;
      }
    },
    destroy: function ( detach ) {
      if ( detach ) {
        detachNode( h1 );
      }
    }
  };
}

... 長いので省略

return App;

}());
```

## 機能  
### 1. Scoped styles
CSSはstyleタグを使います。
```
<!-- App.html -->
<h1 class="header">Hello {{name}}!</h1>
<style>
.header {
  color: #ff0000;
}
</style>
```
変換すると、styleタグを挿入するコードが追加されます。  
ScopedなCSSです。
```
// App.js
function add_css () {
  var style = createElement( 'style' );
  style.id = "svelte-1022934213-style";
  style.textContent = "\n[svelte-1022934213].header, [svelte-1022934213] .header {\n  color: #ff0000;\n}\n";
  appendNode( style, document.head );
}
```  

### 2. Nested components
いまのところSvelte自体にバンドルする仕組みがないようなので、  
[Browserify](http://browserify.org/)と[sveltify](https://github.com/TehShrike/sveltify)を利用します。  
以下のように、ファイルを用意したとします。

```
<!-- index.html -->
<!DOCTYPE html>
<html lang="ja">
<body>
  <div id="app"></div>
  <script src="public/App.js"></script>
</body>
</html>
```

エントリーファイル
```
// App.js
const App = require('./App.html')
new App({
  target: document.getElementById('app'),
  data: { 'text': 'Svelteを使ってみる。' }
});
```

親ファイル
```
<!-- App.html -->
<Header />
<Main text="{{text}}"/>
<Footer />

<script>
import Header from './components/Header.html'
import Main from './components/Main.html'
import Footer from './components/Footer.html'
export default {
  components: {
    Header,
    Main,
    Footer
  }
}
</script>
```
子コンポーネントとなるファイル
```
<!-- components/Header.html -->
<header>ヘッダー</header>
```
```
<!-- components/Main.html -->
<main>
  <section>
    <p>{{text}}</p>
  </section>
</main>
```
```
<!-- components/Footer.html -->
<footer>
  <p>Copyright © tacrow All Rights Reserved.</p>
</footer>
```

子コンポーネントのファイルを親ファイルで``import``し、``components``に渡して参照させて、  
以下を実行するとバンドルされたJSファイル(``public/App.js``)が生成されます。

``browserify -t sveltify App.js > public/App.js``

### 3. Template syntax
#### 3-1. Tags
```
<!-- App.html -->
<h1 style="color: {{color}};">{{color}}</h1>
```
```
// App.js
import App from './App.html'
new App({
  ...
  data: { 'color': '#999' }
});
```

#### 3-2. Triples
```
<!-- App.html -->
<!-- HTML文字列として表示 -->
<p>HTML: {{html}}</p>
<!-- HTMLとしてレンダリング -->
<p>Render: {{{html}}}</p>
```
```
// App.js
import App from './App.html'
new App({
  ...
  data: { 'html': 'Some <b>bold</b> text.' }
});
```

#### 3-3. If 条件分岐
if-else
```
<!-- App.html -->
{{#if user.loginFlag}}
  <a href="/logout">LOGOUT</a>
{{else}}
  <a href="/login">LOGIN</a>
{{/if}}
```
```
// App.js
import App from './App.html'
new App({
  ...
  data: {
    user: { 'loginFlag': false }
  }
});
```
if-elseif-else
```
<!-- App.html -->
{{#if x > 10}}
  <p>{{x}} is greater than 10.</p>
{{elseif 5 > x}}
  <p>{{x}} is less than 5.</p>
{{else}}
  <p>{{x}} is between 5 and 10.</p>
{{/if}}
```
```
// App.js
import App from './App.html'
new App({
  ...
  data: { 'x': 7 }
});
```

#### 3-4. Each 繰り返し
```
<!-- App.html -->
<ul>
{{#each lists as list}}
  <li>{{list.item}}</li>
{{/each}}
</ul>
```
```
// App.js
import App from './App.html'
new App({
  ...
  data: { 
    'lists': [
      { 'item': 'item-1' },
      { 'item': 'item-2' },
      { 'item': 'item-3' }
    ]
  }
});
```

#### 3-5. Directives 命令
```
<!-- App.html -->
<p>Count：{{count}}</p>
<button on:click="set({ count: count+1 })">COUNT-UP</button>
```
```
// App.js
import App from './App.html'
new App({
  ...
  data: { 'count': 0 }
});
```

### 4. Computed property
Vueなどで馴染みのある算出プロパティ  
参照している値を監視して最低限の実行で処理する。
```
<p>The Time is ...</p>
<strong>{{hour}}:{{min}}:{{sec}}</strong>

<script>
export default {
  data() {
    return { time: new Date() };
  },
  computed: {
    hour: time => time.getHours(),
    min:  time => time.getMinutes(),
    sec:  time => time.getSeconds()
  }
};
</script>
```

### 5. Lifecycle
ライフサイクルは、``oncreate``と``ondestroy``のみです。  
先のコードを1秒ごとに時間を更新させるようにしました。
```
<p>The Time is ...</p>
<strong>{{hour}}:{{min}}:{{sec}}</strong>

<script>
export default {

  // 現在時刻を再取得
  oncreate() {
    this.interval = setInterval( () => {
      this.set({ time: new Date() });
    }, 1000);
  },
  
  // 時間をクリア
  ondestroy() {
    clearInterval( this.interval );
  },
  
  data() {
    return { time: new Date() };
  },
  computed: {
    hour: time => time.getHours(),
    min:  time => time.getMinutes(),
    sec:  time => time.getSeconds()
  }
};
</script>
```

## さいごに
2番煎じ感バリバリな記事ですが、取りあえず主要と思われる部分を簡単にまとめてみました。  

テンプレート部分の、  
``{{#if}}...{{/if}}``や``{{#each}}...{{/each}}``などは好みが分かれるかもしれません。

まだまだ開発途上のものなので業務で利用するにはまだまだかなと考えますが、シンプルで導入がラクで学習コストも低い感じなので今後もチェックしていきたいと思います。  

***【svelte-demo】***  
[https://github.com/tacrow/svelte-demo](https://github.com/tacrow/svelte-demo)
