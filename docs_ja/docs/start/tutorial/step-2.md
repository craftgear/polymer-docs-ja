---
layout: default
type: start
navgroup: docs
shortname: Start
title: "Step 2: 独自エレメントを作る"
subtitle: はじめてのPolymerアプリケーション
---

<link rel="import" href="/elements/side-by-side.html">

<link rel="stylesheet" href="tutorial.css">

{% include toc.html %}


## Step 2: あなただけのエレメント

さて、基本的なアプリケーションの構造は完成しました。次は記事を表示するカードエレメントを作る番です。完成したエレメントはプロフィール画像、名前、お気に入りボタン、およびコンテンツエリアを含みます。

<div layout vertical center>
  <img class="sample" src="/images/tutorial/card.png">
</div>

このステップでは、`<post-card>`エレメントを作ります。このエレメントは子要素のスタイルとレイアウトをコントローします。上の画像にあるようなカードを、次のようなシンプルなマークアップで作ることができるようになります:
 
    <post-card>
      <img src="profile-picture.png">
      <h2>A. Developer</h2>
      <p>Something really profound about code.</p>
    </post-card>

このステップで学ぶことは次のとおりです:

-   Polymerの機能を使ってカスタムエレメントを作る
-   shadow DOMを利用する

<aside class="alert alert-info">
<p><b>さらに詳しく:</b>Shadow DOMはDOM要素内にローカルなDOMツリーを追加する機能を提供します。ローカルDOMツリーはウェブページからは切り離され、ローカルなスタイルとマークアップを持ちます。
</p>
<p>shadow DOMについてより詳しく学ぶには、  <a href="/platform/shadow-dom.html">
Shadow DOM polyfill ドキュメント</a>を参照して下さい。</p>
</aside>

### post-card.html ファイルを編集する

`post-card.html`をエディタで開きます。このファイルにはカスタムエレメントのひな形が用意されています。まずはインポートから始めましょう:

<side-by-side>
<pre>
&lt;link rel="import" 
  href="../components/polymer/polymer.html">
&lt;link rel="import" 
  href="../components/core-icon-button/core-icon-button.html">
...
</pre>
<aside>
<h4>ポイント</h4>

<ul>
<li>前回と同じように、<code>&lt;link rel="import"&gt;</code>を使って <code>post-card</code> が必要とするエレメントをインポートします。</li>

</ul>
</aside>
</side-by-side>

次はエレメントそのものの定義です:

<side-by-side>
<pre>
&lt;polymer-element name="post-card">
  &lt;template>
    &lt;style>
    :host {
      display: block;
      position: relative;
      background-color: white;
      padding: 20px;
      width: 100%;
      font-size: 1.2rem;
      font-weight: 300;
    }
    .card-header {
      margin-bottom: 10px;
    }
    &lt;/style>

    &lt;!-- CARD CONTENTS GO HERE -->
  &lt;/template>
  ...
</pre>
<aside>
<h4>ポイント</h4>

<ul><li>
    <code>&lt;polymer-element&gt;</code>エレメントを使うのが、Polymerを使って新しいカスタムエレメントを作る方法です。この例では "post-card" という名前のエレメントを作ります。
    </li>
<li>
The <code>&lt;template&gt;</code> はエレメント内部のDOM構造すなわち<em>shadow DOM</em>を定義します。ここがカスタムエレメントのマークアップを記述する場所です。
    </li>
<li>
shadow DOM ツリー内部で、<code>:host</code>仮想クラスを呼び出すと、<em>このツリーを含んでいるエレメント</em>を指定します。この例では<code>&lt;post-card&gt;</code>エレメントになります。
    </li>
<li>
shadow DOM内で通常のセレクタを呼び出すと、そのセレクタはshadow DOM内に<em>限って</em>有効になります。<code>.card-header</code>というクラス指定は、このカスタムエレメントのshadow DOM内でのみ有効です。
    </li>
</ul>
</aside>
</side-by-side>

**注意:** `<polymer-element>`タグ内 _直下_ には、`<template>`タグをひとつだけ含めることができます。このタグは要素のshadow DOMを定義します。一番外側の`<template>`タグ内に複数の`<template>`タグを含めることができます。 
{: .alert .alert-info }

エレメント定義の最後は`<script>`タグです:

<side-by-side>
<pre>
...
  &lt;script>
  Polymer({});
  &lt;/script>
&lt;/polymer-element>
</pre>
<aside>
<h4>ポイント</h4>
<ul>
<li>ファイル最後にある<code>Polymer</code>の呼び出しは、エレメントを<em>登録</em>し、ブラウザから認識できるようにします。この後のステップで更に詳しく見ていきます。 </li>
</ul>
</aside>
</side-by-side>


<aside class="alert alert-info">
<p><b>さらに詳しく:</b>
<code>&lt;post-card&gt;</code>のインスタンスを作ると、shadow DOMの<code>&lt;template&gt;</code>がエレメントの<em>shadow root</em>として挿入されます。このエレメントはブラウザ上で表示されますが、エレメントの<code>children</code>コレクションはその限りではありません。

<p>デフォルトでは、ユーザによってカスタムタグ内に追加された子要素は表示されません。例えば:</p>

<pre>&lt;post-card&gt;&lt;h3&gt;Hello!&lt;/h3&gt;&lt;/post-card&gt;</pre>

<p>このマークアップは、一つの<code>&lt;h3&gt;</code>タグを含んだ<code>&lt;post-card&gt;</code>エレメントを作りますが、<code>&lt;post-card&gt;</code>内の<code>&lt;h3&gt;</code>を表示するには、<em>insertion point</em>をテンプレートに追加する必要があります。insertion pointはshadow DOM内のどこに子要素を表示するのかをブラウザに指示します。
</p>
</aside>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

カードの構造を作ります。

`CARD CONTENTS GO HERE`というコメントを見つけて下さい。このコメントを、以下に示すように`<div>`タグと`<content>`タグで置き換えます。

<side-by-side>
<pre>
&lt;/style>

<strong class="highlight nocode">&lt;div class="card-header" layout horizontal center>
  &lt;content select="img">&lt;/content>
  &lt;content select="h2">&lt;/content>
&lt;/div>
&lt;content>&lt;/content></strong>
</pre>
  <aside>
  <h4>ポイント</h4>
    <ul>
    <li><code>layout horizontal center</code>属性はflexboxレイアウトをつくためのPolymerのショートカットです。 </li>
    <li>3つの <code>&lt;content&gt;</code> エレメントは<em>insertion points</em>を作ります。 <br />
    (shadow DOM 仕様ではこのプロセスをselecting nodes<em>distribution</em>と呼びます)</li>
    <li>子要素の<code>&lt;img&gt;</code>タグは最初の <code>&lt;content&gt;</code> タグの位置に挿入されます。</li>
    <li>二番目の <code>&lt;content&gt;</code> タグの位置には子要素の <code>h2</code> タグが挿入されます。</li>
    <li>最後の <code>&lt;content&gt;</code> タグには <code>select</code> 属性が無いため、残りの要素が全部個々に挿入されます。(おそらくこれがもっとも一般的な <code>&lt;content&gt;</code> エレメントの使われ方でしょう。)</li>
    </ul>
  </aside>
</side-by-side>

**コンテンツの選択について**: `content`エレメントの`select` 属性には[限られたCSSセレクタ](http://w3c.github.io/webcomponents/spec/shadow/#satisfying-matching-criteria)のみ指定可能です。親ノード直下のエレメントのみ選択可能です。
{: .alert .alert-info }

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

インポートしたコンテンツのスタイルを設定します。

たくさんの新しいCSSセレクタが出てきます。`post-card.html`ファイルには先ほど説明したとおり、すでに`:host`セレクタがあります。`:host`セレクタはトップレベルの`<post-card>`エレメントを装飾します。

`<content>`エレメントに追加された子要素を装飾するには、次に示すCSSコードを`<style>`タグの既存のルールの後に追加して下さい。

<side-by-side>
<pre>.card-header {
  margin-bottom: 10px;
}<strong class="highlight nocode">
polyfill-next-selector { content: '.card-header h2'; }
.card-header ::content h2 {
  margin: 0;
  font-size: 1.8rem;
  font-weight: 300;
}
polyfill-next-selector { content: '.card-header img'; }
.card-header ::content img {
  width: 70px;
  border-radius: 50%;
  margin: 10px;
}</strong>
&lt;/style>
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li> <code>::content</code> 仮想セレクタで、<code>&lt;content&gt;</code>タグで作られた挿入ポイントを示します。
      この例では、<code>::content h2</code>のスタイルが、挿入ポイント内にあるすべての<code>h2</code>に適用されます。
      </li>
      <li>shadow DOMをネイティブサポートしていないブラウザのために、<br/> <code>polyfill-next-selector</code>ルールがshadow DOM polyfillに<code>::content</code>ルールをshadow DOMではないルールに変換する方法を指定します。例えば、<code>post-card h2</code>はカード内のすべての<code>&lt;h2&gt;</code>にマッチします。 </li>
    </ul>
  </aside>
</side-by-side>

**注意:** 
挿入ポイントそのものを装飾することはできません。したがって<code>::content</code>仮想セレクタは常に子要素のセレクタと共に利用されます。
{: .alert .alert-info }

### index.htmlを編集する

新しいエレメントを`index.html`にインポートしましょう。

`post-card.html`を保存し、`index.html`をエディタで開きます。既存のインポートの後に、`post-card.html`のインポート指示を追加します。

<side-by-side>
<pre>
...
&lt;link rel="import"
  href="../components/paper-tabs/paper-tabs.html">
<strong class="highlight nocode">&lt;link rel="import" href="post-card.html"></strong>
...
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li>これで<code>index.html</code>内で<code>&lt;post-card&gt;</code>エレメントが使えるようになります。</li>
    </ul>
  </aside>
</side-by-side>

 <div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div> 

`<post-card>`エレメントを`index.html`の`<core-toolbar>`の直後に追加します:

<side-by-side>
<pre>
...   
<strong class="highlight nocode">&lt;div class="container" layout vertical center>

  &lt;post-card>
    &lt;img width="70" height="70" 
      src="../images/avatar-07.svg">
    &lt;h2>Another Developer&lt;/h2>
    &lt;p>I'm composing with shadow DOM!&lt;/p>
  &lt;/post-card>
  
&lt;/div></strong>
...
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li>ここでpost-cardタグ内に追加された子要素は、<code>&lt;post-card&gt;</code>内の挿入ポイントに<em>配置</em>されます。</li>
    </ul>
  </aside>
</side-by-side>

### ここまでの成果をみる

変更を保存しページを再読み込みして下さい。今やあなたのアプリケーションは次の画像のようになっているはずです:

<div layout vertical center>
  <img class="sample" src="/images/tutorial/step-2.png">
</div>

カードにはまだお気に入りボタンを追加する必要があります。しかし形はできてきました。

もしうまく動かない場合は、`step-2`フォルダにあるファイルと自分のファイルを比較してみてください。


-   [`post-card.html`](https://github.com/Polymer/polymer-tutorial/blob/master/step-2/post-card.html)
-   [`index.html`](https://github.com/Polymer/polymer-tutorial/blob/master/step-2/index.html)


**やってみよう:** 挿入ポイントの動作をより良く理解するために色々と試してみましょう。
`<post-card>`の子要素の順番を入れ替えるとなにか変わるでしょうか？複数のイメージタグを追加したら？あるいはテキストならどうでしょう？`post-card.html`の`select=`属性を入れ替えてみるのもいいかもしれません。
{: .alert .alert-info }


<div layout horizontal justified class="stepnav">
<a href="/docs/start/tutorial/step-1.html">
  <paper-button><core-icon icon="arrow-back"></core-icon>Step 1: アプリケーションの構造を作る</paper-button>
</a>
<a href="/docs/start/tutorial/step-3.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Step 3: データバインディングを利用する</paper-button>
</a>
</div>
