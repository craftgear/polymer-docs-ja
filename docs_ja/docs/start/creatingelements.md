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

Learn more about all of the [lifecycle callbacks](/docs/polymer/polymer.html#lifecyclemethods).

### Declarative data binding

Data binding is a great way to quickly propagate changes in your element and reduce boilerplate code. You can bind properties in your component using the "double-mustache" syntax (`{%raw%}{{}}{%endraw%}`). The `{%raw%}{{}}{%endraw%}` is replaced by the value of the property referenced between the brackets.

{% include samples/name-tag.html %}

Note: {{site.project_title}}'s data-binding is powered under the covers by a sub-library called [TemplateBinding](/docs/polymer/template.html), designed for other libraries to build on top of.
{: .alert .alert-info}

#### Binding to markup

You can use binding expressions in most HTML markup, except for tag names themselves. In the following example, we create a new property on our component named `color` whose value is bound to the value of the `color` style applied to the custom element. Bindings ensure that any time a property like `color` is changed, the new value will be propagated to all binding points.

{% include samples/fav-color.html %}

#### Binding between components and built-in elements {#bindingtobuiltin}

You can use bindings with built-in elements just like you would with Polymer elements. This is a great way to leverage existing APIs to build complex components. The following example demonstrates binding component properties to attributes of native input elements.

{% include samples/age-slider.html %}

**Note:** Giving `age` an initial value of `25` gives {{site.project_title}}
a hint that this property is an integer.
{: .alert alert-info}

In this example, `nameChanged()` defines a property changed watcher. {{site.project_title}} will then call this method any time the `name` property is updated. Read more about [changed watchers](/docs/polymer/polymer.html#change-watchers).

### Publishing properties {#publishing}

Published properties can be used to define an element's "public API". {{site.project_title}}
establishes two-way data binding for published properties and provides access
to the property's value using `{%raw%}{{}}{%endraw%}`.

_Publish_ a property by listing it in the `attributes` attribute in your `<polymer-element>`. Properties declared this way are initially `null`. To provide a more appropriate default value, include the same property name directly in your prototype.

The following example defines two data-bound properties on the element, `owner` and `color`,
and gives them default values:

{% include samples/color-picker.html %}

In this example the user overrides the defaults for `owner` and `color`
by configuring the element with initial attribute values (e.g. `<color-picker owner="Scott" color="blue">`).

**Note**: When binding  a property that takes a type other than String, it's important to [hint a property's type](/docs/polymer/polymer.html#attrhinting). {{site.project_title}} relies on this information to correctly serialize and de-serialize values.
{: .alert .alert-success }

[Learn more about published properties](/docs/polymer/polymer.html#published-properties).

### Automatic node finding

The use of the `id` attribute has traditionally been discouraged as an anti-pattern because the document requires element IDs to be unique. Shadow DOM, on the other hand, is a self-contained document-like subtree; IDs in that subtree do not interact with IDs in other trees. This means the use of IDs in Shadow DOM is not only permissible, it's actually encouraged. Each {{site.project_title}} element generates a map of IDs to node references in the element's template. This map is accessible as `$` on the element and can be used to quickly select the node you wish to work with.

{% include samples/editable-color-picker.html %}

[Learn more about automatic node finding](/docs/polymer/polymer.html#automatic-node-finding)

## Next steps {#nextsteps}

Now that you know how to create your own elements, follow the 
[tutorial](/docs/start/tutorial/intro.html) to create your first 
{{site.project_title}} app, or dive deeper and read up on 
[{{site.project_title}}'s core API](/docs/polymer/polymer.html). 
Continue on to:

<a href="/docs/polymer/polymer.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>API developer guide</paper-button>
</a>

<a href="/docs/start/tutorial/intro.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Your first {{site.project_title}} app</paper-button>
</a>


