---
layout: default
type: start
shortname: Start
title: "Step 1: アプリケーションの構造を作る"
subtitle: はじめてのPolymerアプリケーション
---

<link rel="import" href="/elements/side-by-side.html">

<link rel="stylesheet" href="tutorial.css">

{% include toc.html %}

## Step 1: アプリケーションの構造を作る

このステップでは予め用意されたPolymerエレメントを用いて、タブとツールバーを備えた基本的なアプリケーションの構造を作ります。

このステップで学ぶことは次のとおりです:

-   HTML importを使う
-   HTML/CSS/Javascriptと組み合わせて {{site.project_title}} エレメントを使う

### index.htmlを編集する

`starter`ディレクトリに移動し、エディタで`index.html`ファイルを開きます。　
初期状態のファイルは次のようになっています:

<side-by-side>
<pre>
&lt;!doctype html>
&lt;html>

&lt;head>

  &lt;title>unquote&lt;/title>

  &lt;meta name="viewport"
    content="width=device-width, minimum-scale=1.0, initial-scale=1.0, user-scalable=yes">

  &lt;script src="../components/webcomponentsjs/webcomponents.js">
  &lt;/script>

  &lt;link rel="import"
    href="../components/font-roboto/roboto.html">
  ...
</pre>
<aside>
  <h4>Key information</h4>
  <ul>
    <li>This bare-bones file defines some styles and embeds the <code>webcomponents.js</code> script, which supplies any missing platform features.</li>
    <li>The <code>link rel="import"</code> element is an <em>HTML import</em>, a new way of including resources into an HTML file.</li>
  </ul>
</aside>
</side-by-side>

**注意:** `font-roboto` import の行は、[Google Fonts API](https://developers.google.com/fonts/)を用いて `RobotoDraft`フォントを読み込みます。オフラインで作業をしていたり、GoogleAPIにアクセスできなかったりすると、この行がページが表示されるのを妨げることがあります。この問題に遭遇した場合は、この行をコメントアウトして下さい。
{: .alert .alert-info }


スタイル定義はひとまず飛ばします。ファイルの最後に見慣れないものがあります:

<side-by-side>
<pre>
...
&lt;body unresolved>

&lt;/body>
...
</pre>
<aside>
  <h4>ポイント</h4>
  <ul>
    <li><code>&lt;body></code>タグの<code>unresolved</code>属性はスタイルが適用される前の要素が一瞬表示されるのを防ぎます。この現象はカスタムエレメントをネイティブサポートしていないブラウザで起こります。より詳しくは <a href="/docs/polymer/styling.html#fouc-prevention">Polymer スタイルレファレンス</a>を参照して下さい。</li>
  </ul>
</aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`<core-header-panel>`と`<core-toolbar`、および`<paper-tabs>`をインポートするため、HTML imports 行を追加します:

<side-by-side>
<pre>
&lt;script
  src="../components/webcomponentsjs/webcomponents.js">
&lt;/script>

&lt;link rel="import"
  href="../components/font-roboto/roboto.html">
<strong class="highlight nocode">&lt;link rel="import"
  href="../components/core-header-panel/core-header-panel.html">
&lt;link rel="import"
  href="../components/core-toolbar/core-toolbar.html">
&lt;link rel="import"
  href="../components/paper-tabs/paper-tabs.html"></strong>
&lt;style>
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li>
        Polymerはコンポーネントを読み込むのに<a href="/platform/html-imports.html">HTML imports</a> を使います。HTML importsは依存性の管理を提供し、エレメントとその依存先が利用に先立って全て読み込まれることを保証します。
      </li>
      <li>
        このチュートリアルを通じて、あなたが追加する必要のあるコードは<code><strong class="highlight nocode">黒い太文字</strong></code>で表示されます。
      </li>
    </ul>
  </aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

ツールバーを追加します。`<body>`タグ内に以下のコードを追加して下さい。

<side-by-side>
<pre>
<strong class="highlight nocode">&lt;core-header-panel>

  &lt;core-toolbar>
  &lt;/core-toolbar>

  &lt;!-- main page content will go here -->

&lt;/core-header-panel></strong>
</pre>
  <aside>
    <h4>ポイント</h4>

    <ul>
      <li>
          <code><a href="/docs/elements/core-elements.html#core-header-panel">&lt;core-header-panel&gt;</a></code>エレメントはヘッダ（この例では<code>&lt;core-toolbar></code>エレメント）やその他の要素を保持するシンプルなコンテナです。デフォルトではヘッダはページ上部に常に表示されますが、コンテンツと一緒にスクロールさせることもできます。
          </li>
      <li><code><a href="/docs/elements/core-elements.html#core-toolbar">&lt;core-toolbar></a></code>エレメントはタブ、メニューボタン、その他コントロールのコンテナとして働きます。</li>
    </ul>
  </aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

タブを追加します。

このアプリケーションでは二つの異なるビューを切り替えるのにタブを使います。各々のタブにはメッセージ一覧とお気に入り一覧が表示されます。<code><a href="/docs/elements/paper-elements.html#paper-tabs">&lt;paper-tabs&gt;</a></code>エレメントは`<select>`エレメントのように振る舞いますが、一組のタブとして表示されます。

<side-by-side>
<pre>
...
&lt;core-toolbar>

  <strong class="highlight nocode">&lt;paper-tabs id="tabs" selected="all" self-end>
    &lt;paper-tab name="all">All&lt;/paper-tab>
    &lt;paper-tab name="favorites">Favorites&lt;/paper-tab>
  &lt;/paper-tabs></strong>

&lt;/core-toolbar>
...
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li>
        <code>&lt;paper-tabs></code> は選択状態のタブをname属性の値か、インデックスで識別します。      </li>
      <li>
        <code>selected="all"</code> 属性は、最初のタブを選択状態にします。
      </li>
      <li>この例では、子要素は<code>&lt;paper-tab></code>エレメントです。このエレメントはタブにスタイルと、タブが選択された時の"ink ripple"アニメーションを追加します。      </li>
      <li>
        <code>self-end</code> は Polymerの<a href="/docs/polymer/layout-attrs.html">レイアウト属性</a>です。
      </li>

    </ul>
  </aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

新しい要素にスタイルを適用します。以下のCSSルールを`<style>`タグ内に追加して下さい。

<side-by-side>
<pre>
html,body {
  height: 100%;
  margin: 0;
  background-color: #E5E5E5;
  font-family: 'RobotoDraft', sans-serif;
}
<strong class="highlight nocode">core-header-panel {
  height: 100%;
  overflow: auto;
  -webkit-overflow-scrolling: touch;
}
core-toolbar {
  background: #03a9f4;
  color: white;
}
#tabs {
  width: 100%;
  margin: 0;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  text-transform: uppercase;
}</strong>
</pre>
<aside>
  <h4>ポイント</h4>
  <ul>
    <li><code>&lt;core-header-panel&gt;</code> はレイアウト用の汎用要素で、ページ全体を覆ったり、ツールバーの一部として使ったりできます。core-header-panelをページ全体を覆うスクロール可能なコンテナとして使うには、高さを明示的に指定します。</li>
    <li>ここでは高さを100%に設定しています。この指定が意味を持つのは、親要素の<code>&lt;html&gt;</code> タグと <code>&lt;body&gt;</code>タグが100%の高さを占めることがすでにCSSで指定されているからです。
    </li>
    <li> <code>overflow</code> プロパティと <code>-webkit-overflow-scrolling</code> プロパティはタッチパネル搭載機器でスクロールがproperties ensure that
        scrolling works smoothly on touch devices, especially iOS.</li>
    <li>The <code>#tabs</code> selector selects the <code>&lt;paper-tabs&gt;</code> element. The toolbar adds a default margin on its children, to space controls appropriately. The tabs don't need this extra spacing.</li>
    <li>The <code>user-select</code> properties prevent the user from accidentally selecting the tab text.</li>
  </ul>
</aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

Add a `<script>` tag near the end of the file to handle the tab switching
    event.


<side-by-side>
<pre>
<strong class="highlight nocode">&lt;script>
  var tabs = document.querySelector('paper-tabs');

  tabs.addEventListener('core-select', function() {
    console.log("Selected: " + tabs.selected);
  });
&lt;/script>
</strong>&lt;/body>
</pre>
  <aside>
    <h4>Key information</h4>
    <ul>
      <li>
        The <code>&lt;paper-tabs></code> element fires a <code>core-select</code> event when you select a
        tab. You can interact with the element just like a built-in element.
      </li>
      <li>
        Right now there's nothing to switch; you'll finish hooking it up later.
      </li>
    </ul>
  </aside>
</side-by-side>


Save the file and open the project in your browser (for example, [http://localhost:8000/starter/](http://localhost:8000/starter/)). You have a Polymer app!


<div layout vertical center>
  <img class="sample" src="/images/tutorial/step-1.png">
</div>

**Note:** If you have the console open, you'll notice that you get two `core-select`
events each time you switch tabs &mdash; one for the previously-selected tab and one
for the newly-selected tab. The `<paper-tabs>` element inherits this behavior from
<code><a href="/docs/elements/core-elements.html#core-selector">&lt;core-selector&gt;</a></code>, which supports
both single and multiple selections.
{: .alert .alert-info }

If something isn't working, check your work against the `index.html` file in the `step-1` folder:

-   [`index.html`](https://github.com/Polymer/polymer-tutorial/blob/master/step-1/index.html)

In this step, you used HTML imports to import custom elements, and used them to create a simple app layout.

**Explore:** Can you use other children inside the `<paper-tabs>`? Try an image or a text span.
{: .alert .alert-info }

<div layout horizontal justified class="stepnav">
<a href="/docs/start/tutorial/intro.html">
  <paper-button><core-icon icon="arrow-back"></core-icon>Getting Started</paper-button>
</a>
<a href="/docs/start/tutorial/step-2.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Step 2: Creating your own element</paper-button>
</a>
</div>
