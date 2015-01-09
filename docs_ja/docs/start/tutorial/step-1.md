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
    <li> <code>overflow</code> プロパティと <code>-webkit-overflow-scrolling</code> プロパティはタッチパネル搭載機器、特にiOS搭載機器でスクロールがスムーズに動くようにします。
    </li>
    <li><code>#tabs</code>セレクタは<code>&lt;paper-tabs&gt;</code>エレメントのスタイルを指定します。core-toolbarは子要素を適切に配置するため、デフォルトマージンを設定します。そのためpaper-tabsには余白は必要ありません。
    </li>
    <li><code>user-select</code>プロパティはユーザが誤ってタブのテキストを選択してしまうことを防ぎます。</li>
  </ul>
</aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`<script>`タグをファイルの最後の方に追加し、タブの切り替えイベントを処理できるようにします。

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
    <h4>ポイント</h4>
    <ul>
      <li>
        <code>&lt;paper-tabs&gt;</code>エレメントは、タブが選択されると<code>core-select</code>イベントを発行します。ビルトインエレメントと同様に扱うことができます。
      </li>
      <li>
        今のところ切り替えるものは何もありません。後ほど処理を追加します。
      </li>
    </ul>
  </aside>
</side-by-side>


ファイルを保存し、ブラウザでプロジェクトページ（例えば、[http://localhost:8000/starter/](http://localhost:8000/starter/)）を開いて下さい。Polymerアプリケーションが表示されます。


<div layout vertical center>
  <img class="sample" src="/images/tutorial/step-1.png">
</div>

**注意:** コンソールを開いていると、タブを切り替えるたびに`core-select`イベントが二回発行されるのに気がつくと思います。&mdash; 一つはもともと選択されていたタブからのイベントで、もうひとつは新しく選択されたタブからのイベントです。`<paper-tabs>`エレメントは、複数選択もサポートする<code><a href="/docs/elements/core-elements.html#core-selector">&lt;core-selector&gt;</a></code>からこの振る舞いを継承しています。
{: .alert .alert-info }

うまく動かない時は `step-1` フォルダにある `index.html` ファイルと付き合わせて違いを探して下さい:

-   [`index.html`](https://github.com/Polymer/polymer-tutorial/blob/master/step-1/index.html)

このステップではカスタムエレメントをインポートするのにHTML importsを使い、インポートしたカスタムエレメントを簡単なアプリケーションのレイアウトを作るのに利用しました。

**やってみよう:** `<paper-tabs>`に他の子要素を追加できるでしょうか？imageやspanを追加してみましょう。
{: .alert .alert-info }

<div layout horizontal justified class="stepnav">
<a href="/docs/start/tutorial/intro.html">
  <paper-button><core-icon icon="arrow-back"></core-icon>プロジェクトを始めよう</paper-button>
</a>
<a href="/docs/start/tutorial/step-2.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Step 2: 独自エレメントを作る</paper-button>
</a>
</div>
