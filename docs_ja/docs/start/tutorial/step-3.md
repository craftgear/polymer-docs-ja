---
layout: default
type: start
shortname: Start
title: "Step 3: データバインディングを利用する"
subtitle: はじめてのPolymerアプリケーション
---

<link rel="import" href="/elements/side-by-side.html">

<link rel="stylesheet" href="tutorial.css">


{% include toc.html %}


## Step 3: データバインディングを利用する

一つの投稿を表示できたのは良いですが、アプリケーションとしては少しがらんとして見えます。
このステップでは、Webサービスからデータを取得し、Polymerのデータバインディングを使ってデータをカードの並びとして表示します。

データを取得するには、ひな形に入っている`<post-service>`エレメントを使います。このエレメントは仮想SNSのシンプルなAPIを提供します。このセクションでは、次に示す`post`オブジェクトの配列を返す`posts`プロパティを用いることになります:

    {
      "uid": 2,
      "text" : "Loving this Polymer thing.",
      "username" : "Rob",
      "avatar" : "../images/avatar-02.svg",
      "favorite": false
    }

このセクションで学ぶことは次のとおりです:
In this section you'll learn about:

-   データバインディングを利用する
-   プロパティを公開する

### post-list.htmlを編集する

`post-list.html`をエディタで開きます。

<side-by-side>
<pre>
&lt;link rel="import" href="../components/polymer/polymer.html">
&lt;link rel="import" href="../post-service/post-service.html">
&lt;link rel="import" href="post-card.html">

&lt;polymer-element name="post-list" attributes="show">
  &lt;template>
    &lt;style>
    :host {
      display: block;
      width: 100%;
    }
    post-card {
      margin-bottom: 30px;
    }
    &lt;/style>

    &lt;!-- add markup here -->
...
</pre>
  <aside>
    <h4>ポイント</h4>
    <ul>
      <li>ファイルはすでに<code>&lt;post-service&gt;</code>エレメントをインポート済みなので、すぐに利用可能です。
      </li>
      <li><code>attributes="show"</code>属性は、<code>show</code>という名前の  <a href="/docs/polymer/polymer.html#published-properties"> <em>公開プロパティ</em></a>を作ります.
      </li>
    </ul>
  </aside>
</side-by-side>


<a href="/docs/polymer/polymer.html#published-properties">
<em>公開プロパティ</em></a>はタグの属性を使って設定できるプロパティのことで、双方向データバインディングを使って他のプロパティに接続することもできます。`show`プロパティはこの後のステップで利用します。

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`<post-service>`エレメントを`<template>`エレメント内に追加します:

<side-by-side>
<pre>
...
<strong class="highlight nocode">&lt;post-service id="service" posts="{%raw%}{{posts}}{%endraw%}">
&lt;/post-service></strong>
...
</pre>
  <aside>
  <h4>ポイント</h4>
    <ul>
      <li>
        <code>posts="{%raw%}{{posts}}{%endraw%}"</code>属性は<code>&lt;post-service&gt;</code>エレメントと<code>&lt;post-list&gt;</code>エレメントの間に双方向データバインディングを追加します。      </li>
    </ul>
  </aside>
</side-by-side>

[_data binding_](/docs/polymer/databinding.html)は、サービスエレメントの`posts`プロパティとローカルのプロパティ(ここでは同じく`posts`という名前です)をリンクします。カスタムエレメント内に定義するメソッドはサービスのレスポンスに`this.posts`でアクセスできます。

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

サービスから取得したデータを元にカードのリストを表示しましょう。

次に示すように`<div>`タグと`<template>`タグを追加します:

<side-by-side>
{% raw %}
<pre>
...
&lt;post-service id="service" posts="{{posts}}">
&lt;/post-service>

<strong class="highlight nocode">&lt;div layout vertical center>

  &lt;template repeat="{{post in posts}}">
    &lt;post-card>
      &lt;img src="{{post.avatar}}" width="70" height="70">
      &lt;h2>{{post.username}}&lt;/h2>
      &lt;p>{{post.text}}&lt;/p>
    &lt;/post-card>
  &lt;/template>

&lt;/div></strong>
...
</pre>
{%endraw%}
<aside>
 <h4>ポイント</h4>

 <ul>
   <li><code>repeat="{%raw%}{{post in posts}}{%endraw%}"</code>という新しい書式は、テンプレートに<code>posts</code>配列内の個々の要素に対して新しいカードを作るよう指示します。
   </li>
   <li>新しいカード内では、それぞれのデータバインディング(例えば<code>{%raw%}{{post.avatar}}{%endraw%}</code>など)は、配列内の要素の対応する値で置き換えられます。
   </li>
 </ul>
</aside>
</side-by-side>


### index.htmlを編集する

`<post-list>`エレメントを`index.html`にインポートします。

`index.html`を開き、`post-list.html`のインポートリンクを追加します。すでにある`post-card`をインポートしていたリンクを置き換えてもよいでしょう:

<pre>
...
&lt;link rel="import" href="../components/paper-tabs/paper-tabs.html">
<strong class="highlight nocode">&lt;link rel="import" href="post-list.html"></strong>
...
</pre>

<div class="divider" layout horizontal center center-justified>
  <core-icon icon="polymer"></core-icon>
</div>

`<post-list>`エレメントを追加します。

前のステップで追加した`<post-card>`エレメントを`<post-list>`で置換えます:

<pre>
...
&lt;div class="container" layout vertical center&gt;
  <strong class="highlight nocode">&lt;post-list show="all"&gt;&lt;/post-list&gt;</strong>
&lt;/div>
...
</pre>

### ここまでの成果をみる

`index.html`ファイルを保存し、ページを再読み込みしてして下さい。カードのリストが次の画像のように表示されるはずです:

<div layout vertical center>
  <img class="sample" src="/images/tutorial/step-3.png">
</div>

もしうまく動かない場合は、`step-3`フォルダにあるファイルと自分のファイルを比較してみてください:

-   [`post-list.html`](https://github.com/Polymer/polymer-tutorial/blob/master/step-3/post-list.html)
-   [`index.html`](https://github.com/Polymer/polymer-tutorial/blob/master/step-3/index.html)

**やってみよう:** `post-service.html`を開き、コンポーネントの中身がどうなっているのかを見てみましょう。コンポーネント内では <code><a href="/docs/elements/core-elements.html#core-ajax">&lt;core-ajax&gt;</a></code>エレメントを使ってHTTPリクエストを送信しています。 
{: .alert .alert-info}

<div layout horizontal justified class="stepnav">
<a href="/docs/start/tutorial/step-2.html">
  <paper-button><core-icon icon="arrow-back"></core-icon>Step 2: 独自エレメントを作る</paper-button>
</a>
<a href="/docs/start/tutorial/step-4.html">
  <paper-button raises><core-icon icon="arrow-forward"></core-icon>Step 4: 最後の仕上げ</paper-button>
</a>
</div>
