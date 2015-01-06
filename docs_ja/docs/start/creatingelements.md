---
layout: default
type: start
shortname: Start
title: 10分でわかるPolymer
subtitle: Creating elements
---

{% include toc.html %}

{{site.project_title}} makes it simple to create web components, declaratively.
Custom elements are defined using our custom element,  `<polymer-element>`, and can leverage
{{site.project_title}}'s special features. These features reduce boilerplate
and make it even easier to build complex, web component-based applications:

- [Two-way data binding](/docs/polymer/databinding.html)
- [Declarative event handling](/docs/polymer/polymer.html#declarative-event-mapping)
- [Declarative inheritance](/docs/polymer/polymer.html#extending-other-elements)
- [Property observation](/docs/polymer/polymer.html#observeprops)


## セットアップ {#basics}

### 1. {{site.project_title}}のインストール {#install}

{{site.project_title}} の最新バージョンを[コードを入手する](/docs/start/getting-the-code.html)に書いてあるとおりにインストールします。

なにもインストールせずに {{site.project_title}} に触ってみたいなら、[{{site.project_title}}の機能を利用する](#features)に進んで下さい。[Plunker](http://plnkr.co/)を使ってオンラインでサンプルを試すことができます。

### 2. {{site.project_title}}エレメントを作る {#createpolyel}
{{site.project_title}}はカスタムタグを作るのに便利な機能を提供します。Polymerの機能を使って作ったカスタムタグのことを、我々は"{{site.project_title}}エレメント"と呼んでいます。
見た目は普通のDOMエレメントですが、中身は双方向データバインディングを始めとした便利な[{{site.project_title}} マジック](/docs/polymer/polymer.html)がぎっしり詰まっています。
そのおかげで複雑なコンポーネントを少ないコードで簡単に作ることができます。

新しいエレメントを作るには、

1. [{{site.project_title}} コア](/docs/polymer/polymer.html) (`polymer.html`) を読み込みます。
2. `<polymer-element>` タグを用いて、エレメントを宣言します。

以降の例では、`<my-element>`という名前の新しいエレメントを作り、`elements/my-element.html`というファイルに保存します。
そして HTML Import で必要となる`polymer.html`を読み込みます。

**my-element.html**

    <link rel="import" href="../bower_components/polymer/polymer.html">

    <polymer-element name="my-element" noscript>
      <template>
        <span>Hello from <b>my-element</b>. This is my Shadow DOM.</span>
      </template>
    </polymer-element>

ここで注目すべき点は2つあります:

* `name`属性は必須であり、**必ず**"-"を含まなければなりません。name属性によってマークアップの際に指定するHTMLタグの名前が決まります。

* `noscript` 属性はこのエレメントがスクリプトを含まないということを示しています。`noscript`属性を持つエレメンtのは自動的に登録されます。

#### 他のエレメントを再利用する {#reuse}

単純なエレメントを組み合わせることで、より表現力に富んだ複雑なエレメントを作ることができます。
`<polymer-element>`内で他のエレメントを再利用するには、まず対象のエレメントをインストールします:

    bower install Polymer/core-ajax

次に`my-element.html`内に、必要なファイルを読み込むimport指示を追加します。

{%raw%}
    <link rel="import" href="../bower_components/polymer/polymer.html">
    <link rel="import" href="../bower_components/core-ajax/core-ajax.html">

    <polymer-element name="my-element" noscript>
      <template>
        <span>I'm <b>my-element</b>. This is my Shadow DOM.</span>
        <core-ajax url="http://example.com/json" auto response="{{resp}}"></core-ajax>
        <textarea value="{{resp}}"></textarea>
      </template>
    </polymer-element>
{%endraw%}

### 3. アプリケーションを作る {#creatapp}

最後に`index.html`ファイルを作り、そこに今作った新しいエレメントを読み込みます。
polyfillを提供する`webcomponents.min.js`を読み込むのを忘れないようにして下さい。

最終的に次のようなファイルが出来上がります:

    <!DOCTYPE html>
    <html>
      <head>
        <!-- 1. Load platform support before any code that touches the DOM. -->
        <script src="bower_components/webcomponentsjs/webcomponents.min.js"></script>
        <!-- 2. Load the component using an HTML Import -->
        <link rel="import" href="elements/my-element.html">
      </head>
      <body>
        <!-- 3. Declare the element by its tag. -->
        <my-element></my-element>
      </body>
    </html>

**注意:** [HTML Imports](/platform/html-imports.html)を正しく機能させるには、ウェブサーバ上でアプリケーションを実行する必要があります。ブラウザのセキュリティ制限のため、`file://`プロトコルでは動作しません。
{: .alert .alert-info }

最終的なディレクトリ構造は以下のようになります:

    yourapp/
      bower_components/
        webcomponentsjs/
        polymer/
      elements/
        my-element.html
      index.html

さて、これで準備は整いました。いよいよPolymerの機能を使ってみましょう。

## {{site.project_title}}の機能を使う {#features}

{{site.project_title}}はweb componentsを作るための便利なAPIを提供します。以下に概要を示しますが、詳しいことは[APIリファレンス](/docs/polymer/polymer.html)を参照して下さい。

### 属性とメソッドを追加する {#propertiesmethods}

新しいエレメントを作る際には、多くの場合[公開 API](/docs/polymer/polymer.html#published-properties)をユーザが設定できるようにする必要があります。
公開APIを定義するには`{{site.project_title}}(...)`コンストラクタを呼ぶ`<script>`タグを含めます。
`{{site.project_title}}(...)`コンストラクタは[`document.registerElement`](/platform/custom-elements.html#documentregister)のラッパーです。それと同時にエレメントに特別な機能、データバインディングやイベントマッピングなど、を授けます。{{site.project_title}}コンストラクタはエレメントのプロトタイプオブジェクトを引数に取ります。

{% include samples/proto-element.html %}

### ライフサイクルメソッドの追加

[ライフサイクルコールバック](/docs/polymer/polymer.html#lifecyclemethods) は特別なメソッドです。これらのメソッドをエレメントに定義することで、エレメントの状態が変わった時にイベントを発行することができます。
カスタムエレメントが登録されると、そのエレメントの`created()`コールバック関数が（もし定義されていれば）呼ばれます。
{{site.project_title}} がエレメントの初期化を終了すると、今度は`ready()`メソッドが呼ばれます。
`ready`コールバック関数はコンストラクタでするような初期化作業をするのに最適な場所です。

{% include samples/ready-element.html %}

[ライフサイクルコールバック](/docs/polymer/polymer.html#lifecyclemethods)でより詳しい情報を参照できます。

### 宣言的なデータバインディング

データバインディングはエレメントの変化を素早く伝え、お決まりのコードを減らす素晴らしい方法です。
"二重ひげ"書式(`{%raw%}{{}}{%endraw%}`)を使ってコンポーネントのプロパティをバインドすることができます。`{%raw%}{{}}{%endraw%}`で記述されたところは参照先のプロパティの値によって置き換えられます。

{% include samples/name-tag.html %}

注意: {{site.project_title}}のデータバインディングは、[テンプレートバインディング](/docs/polymer/template.html)というサブライブラリによって実現されています。このサブライブラリは他のライブラリから利用することを目的として作られています。
    
{: .alert .alert-info}

#### マークアップにバインドする

バインディングはタグ名そのものを除いたほとんどのHTMLマークアップで利用できます。以降の例では先ほど作ったコンポーネントに`color`という新しいプロパティを追加し、その値をカスタムエレメントの`color`スタイルにバインドします。このバインディングによって、`color`プロパティが変わるたびに、その値がすべてのバインド先に確実に伝えられます。

{% include samples/fav-color.html %}

#### コンポーネントと組み込みエレメントの間でのバインディング {#bindingtobuiltin}

バインディングはPolymerエレメント同様に組み込みエレメントにも使えます。これは既存のAPIを活用して複雑なコンポーネントを作る素晴らしい方法です。次の例ではコンポーネントのプロパティをINPUT要素の属性にバインドしています。

{% include samples/age-slider.html %}

**注意：** `age`の初期値に`25`を与えることで、{{site.project_title}}にこの属性が整数値であることを暗に示しています

{: .alert alert-info}

この例では`nameChanged()`がプロパティの変更を監視しています。{{site.project_title}}は`name`プロパティが変更されるたびにこのメソッドを呼び出します。より詳しくは[変更監視](/docs/polymer/polymer.html#change-watchers)を参照して下さい。

### プロパティを公開する {#publishing}

プロパティを公開するとエレメントの"パブリックAPI"を定義できます。{{site.project_title}}は公開されたプロパティに対して双方向データバインディングと`{%raw%}{{}}{%endraw%}`を用いた値へのアクセスを提供します。

プロパティを_公開_するには`<polymer-element>`内の`attributes`属性にプロパティの名前を追加します。この方法で宣言されたプロパティは`null`で初期化されます。より適切なデフォルト値を与えるには、prototypeに直接同じプロパティ名を含めます。

次の例では`owner`と`color`の二つのプロパティに対してデータバインディングを行い、デフォルト値を与えています。

{% include samples/color-picker.html %}

この例ではユーザーがindex.htmlファイルでカスタムタグを呼び出す際に、属性値を設定することで、`owner`と`color`のデフォルト値を上書きしています。(例 `<color-picker owner="Scott" color="blue">`)

**注意**: 文字列以外の値を取るプロパティをバインドする際には、[プロパティの型を示す](/docs/polymer/polymer.html#attrhinting)ことが重要です。{{site.project_title}}は値のシリアライズとデシリアライズを行う際にこの情報に依存します。
{: .alert .alert-success }

より詳しくは[プロパティの公開についてさらに学ぶ](/docs/polymer/polymer.html#published-properties)を参照して下さい。

### 自動的なノードの発見

`id`属性の利用は従来アンチパターンとして避けるべきものとされてきました。なぜならドキュメント内でIDはユニークでなければならないからです。一方Shadow DOMはそれ自体がドキュメントのような自己完結したツリー構造であり、そのサブツリー内におけるIDは他のサブツリーのIDとは干渉しません。すなわち、Shadow DOM内でのIDの利用は差支えがないだけでなく、むしろ推奨されます。個々の{{site.project_title}}エレメントはIDとエレメントのテンプレート内ノードを対応させた地図を作ります。この地図には`$`を使ってエレメントからアクセスすることができ、操作対象のノードを素早く選択することができます。

{% include samples/editable-color-picker.html %}

より詳しくは[自動的なノードの発見についてより詳しく学ぶ](/docs/polymer/polymer.html#automatic-node-finding)を参照して下さい。

## 次のステップ {#nextsteps}

さてこれでエレメントの作り方がわかりました。つづいて[チュートリアル](/docs/start/tutorial/intro.html)で初めてのアプリケーションを作るか、[{{site.project_title}}コアAPI]を読んでより深くPolymerについて理解して下さい。

いずれかに続く:

<a href="/docs/polymer/polymer.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>API デベロッパーガイド</paper-button>
</a>

<a href="/docs/start/tutorial/intro.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>はじめての {{site.project_title}} アプリケーション</paper-button>
</a>


