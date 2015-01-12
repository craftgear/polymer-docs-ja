---
layout: default
type: start
shortname: Start
title: "Step 4: 最後の仕上げ"
subtitle: はじめてのPolymerアプリケーション
---

<link rel="import" href="/elements/side-by-side.html">

<link rel="stylesheet" href="tutorial.css">

<style>
.unquote-link {
  max-width: 360px;
}
.unquote-image {
  background-image: url(/images/tutorial/finished.png);
  background-size: cover;
  background-position: top;
  width: 360px;
  height: 320px;
  border: 1px solid black;
}
</style>

## Step 4: 最後の仕上げ

このセクションでは、カードにお気に入りボタンを追加し、タブを`<post-list>`コントロールに接続してアプリケーションを完成させます。

このセクションで学ぶことは次のとおりです:

-   宣言的なイベントハンドリング
-   プロパティとメソッドをエレメントのprototypeに追加
-   自動ノード検索

### post-card.htmlを編集する

`post-card.html`をエディタで開き、<code><a href="/docs/elements/core-elements.html#core-icon-button">&lt;core-icon-button></a></code>エレメントを追加します。

<side-by-side>
<pre>
&lt;div class="card-header" layout horizontal center>
  &lt;content select="img">&lt;/content>
  &lt;content select="h2">&lt;/content>
&lt;/div>
<strong class="highlight nocode">
&lt;core-icon-button
  id="favicon"
  icon="favorite"
  on-tap="{%raw%}{{favoriteTapped}}{%endraw%}">
&lt;/core-icon-button>
</strong>
&lt;content>&lt;/content>
</pre>
<aside>
  <h4>ポイント</h4>
  <ul>
    <li>名前からわかるとおり、<code>&lt;core-icon-button&gt;</code>はアイコン付きのボタンを作ります。{{site.project_title}}には拡大可能なアイコンのセットがいくつかあります。</li>
    <li><code>icon="favorite"</code> 属性はデフォルトのアイコンセットからハートアイコンを選びます。</li>
    <li><code>on-tap=</code><code>"{%raw%}{{favoriteTapped}}{%endraw%}"</code> 属性は、ボタンがタップされた時に<code>post-card</code>エレメントが呼び出すメソッドを指定します。</li>
  </ul>
</aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`favorite`プロパティと`favoriteTapped`メソッドをエレメントのprototypeに追加します。

<side-by-side>
<pre>
&lt;script><strong class="highlight nocode">
Polymer({
  publish: {
    favorite: {
      value: false,
      reflect: true
    }
  },
  favoriteTapped: function(event, detail, sender) {
    this.favorite = !this.favorite;
    this.fire('favorite-tap');
  }
});</strong>
&lt;/script>
</pre>
  <aside>
    <ul>
      <li><code>publish</code>オブジェクトは公開するプロパティを指定するもうひとつの方法です。ステップ3で見た<code>attributes</code>属性のように、 <code>favorite</code>プロパティのデフォルト値は<code>false</code>に設定され、DOMに<em>反映</em>されます。すなわち、<code>favorite</code>プロパティが更新されると、DOM内の<code>favorite</code>属性もまた更新されます。
      </li>
      <li><code>favoriteTapped</code> イベントは<code>favorite</code>プロパティ(<code>this.favorite</code>)の状態を切り替えます。また同時に、<code>fire</code>メソッドに実装されたカスタムイベントを発行します。(<code>fire</code>は{{site.project_title}}がすべてのカスタムエレメントのprototypeに追加するユーティリティメソッドのうちの一つです)</li>
    </ul>
  </aside>
</side-by-side>

この変更の結果、お気に入りボタンがタップされると、favoriteプロパティが更新され、対応するfavorite属性も更新されます。

現在のところ、ボタンが押されたことを示す視覚効果は何もありません。

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

ボタンのスタイルを変更するため、以下のCSSルールを追加します:

<side-by-side>
<pre><strong class="highlight nocode">
core-icon-button {
  position: absolute;
  top: 3px;
  right: 3px;
  color: #636363;
}
:host([favorite]) core-icon-button {
  color: #da4336;
}</strong>
&lt;/style>
</pre>
  <aside>
    <ul>
      <li><code>color</code> プロパティはアイコンの塗りつぶし色を設定します。</li>
      <li><code>:host([favorite])</code> <code>core-icon-button</code> セレクタはカスタムエレメントの<code>favorite</code>属性が設定された時の塗りつぶし食を設定します。</li>
    </ul>
  </aside>
</side-by-side>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`post-card.html`を保存して下さい。

この時点で、ページを再読み込みするとお気に入りボタンが動くようになっているはずです。
しかし、アプリケーションを完成させるにはまだ少し作業が残っています。

### index.htmlを編集する

`index.html`を開き、ユーザがタブを切り替えた時に、タブイベントハンドラで`<post-list>`のビューを切り替えるようにします:

<pre>
&lt;script>
var tabs = document.querySelector('paper-tabs');
<strong class="highlight nocode">var list = document.querySelector('post-list');

tabs.addEventListener('core-select', function() {
  list.show = tabs.selected;
});</strong>
&lt;/script>
</pre>

`index.html`を保存して下さい。

### post-list.htmlを編集する

`post-list.html`をエディタで開きます。

`<post-card>`を作るテンプレートを更新し、お気に入り状態を反映するようにします:

<side-by-side>
  {% raw %}
<pre>
  &lt;template repeat="{{post in posts}}">
    <strong class="highlight nocode">
    &lt;post-card
      favorite="{{post.favorite}}"
      on-favorite-tap="{{handleFavorite}}"
      hidden?="{{show == 'favorites' && !post.favorite}}"></strong>
      &lt;img src="{{post.avatar}}" width="70" height="70">
      &lt;h2>{{post.username}}&lt;/h2>
      &lt;p>{{post.text}}&lt;/p>
    &lt;/post-card>
  &lt;/template>
</pre>
  {% endraw %}
  <aside>
    <ul>
      <li><code>favorite=<wbr>"{%raw%}{{post.favorite}}{%endraw%}"</code> はカードの<code>favorite</code>の値を<code>&lt;post-service&gt;</code>にある配列の値と結びつけます。</li>
      <li><code>on-favorite-tap</code> 属性は、<code>&lt;post-card&gt;</code>によって発行された<code>favorite-tap</code>イベントのイベントハンドラを設定します。</li>
      <li><code>hidden?=</code><wbr><code>"{%raw%}{{}}{%endraw%}"</code> はブーリアン属性用の特別な書式で、属性の値に設定された式が真の場合に、属性を有効にします。</li>
    </ul>
  </aside>
</side-by-side>

`hidden`に設定された式は、すべてのカードとお気に入りのカードを切り替える役割を果たします。
`hidden`属性はHTML5の標準属性です。`hidden`をネイティブサポートしないブラウザのために、{{site.project_title}}のデフォルトスタイルシートは`hidden`を`display:none`に設定するルールを含んでいます。

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`post-list.html`の`favorite-tap`イベントにイベントハンドラを設定します:

<side-by-side>
<pre>
&lt;script>
<strong class="highlight nocode">Polymer({
  handleFavorite: function(event, detail, sender) {
    var post = sender.templateInstance.model.post;
    this.$.service.setFavorite(post.uid, post.favorite);
  }
});
</strong>&lt;/script>
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li><code>sender<wbr>.templateInstance<wbr>.model</code> は テンプレートインスタンスを作るときに使われたデータへの参照です。この例では、<code>&lt;post-card&gt;</code>を作る際に使われた<code>post</code>オブジェクトを指します。したがって、postのIDと<code>favorite</code>の値を取得できます。</li>
      <li><code>this.$.service</code> は<code>&lt;post-service&gt;</code>エレメントの参照を返します。カスタムエレメントのshadow DOM内にあるすべてのエレメントは<code>id</code>属性を持ち、この<code>id</code>は<code>this.$</code>に格納されています。これは<a href="/docs/polymer/polymer.html#automatic-node-finding">自動ノード検索</a>と呼ばれています。
      </li>
      <li>もしこれが本物のSNSなら、<code>setFavorite</code>メソッドはサーバに変更を保存するでしょう。見ての通りこの例ではコンソールにメッセージを表示する以外何もしません。</li>
    </ul>
  </aside>
</side-by-side>

### ついに完成!

`post-list.html`を保存し、ページを再読み込みしてしてください。

これで完成です、お疲れ様でした！運が良ければあなたのアプリケーションは次の画像のようにあっているでしょう:

<figure layout vertical center>
  <a href="/apps/polymer-tutorial/finished/" class="unquote-link">
    <div class="unquote-image"></div>
  </a>
  <figcaption>
    Click screenshot for demo
  </figcaption>
</figure>

もしどこかおかしいところがあるなら、`finish`ディレクトリにあるファイルと自分のファイルを比較してみてください:

-   [`post-card.html`](https://github.com/Polymer/polymer-tutorial/blob/master/finished/post-card.html)
-   [`post-list.html`](https://github.com/Polymer/polymer-tutorial/blob/master/finished/post-list.html)
-   [`index.html`](https://github.com/Polymer/polymer-tutorial/blob/master/finished/index.html)

### 新たなプロジェクトを始める

新しいプロジェクトを始める準備はいいですか？{{site.project_title}}コンポーネントをインストールして早速仕事にとりかかりましょう！

<div layout horizontal justified class="stepnav">
<a href="/docs/start/tutorial/step-3.html">
  <paper-button><core-icon icon="arrow-back"></core-icon>Step 3: データバインディングを利用する</paper-button>
</a>
<a href="/docs/start/getting-the-code.html#installing-components">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>コンポーネントをインストールする</paper-button>
</a>
</div>

