---
layout: default
type: guide
shortname: Docs
title: データバインディングの概要
subtitle: Data-binding
---

{% include toc.html %}


{{site.project_title}}は双方向データバインディングをサポートしています。データバインディングはHTMLとDOM API群を拡張し、アプリケーションのUI(DOM)とその下にあるデータ(モデル)のわかりやすい分離をサポートします。データモデルの更新はDOMに反映され、DOMに対するユーザの入力は即剤にデータモデルに割り当てられます。

{{site.project_title}}エレメントにとって、**データモデルは常にエレメントそのものです**。例えば、次のような単純なエレメントを考えてみましょう:

{% raw %}
    <polymer-element name="name-tag">
      <template>
        This is <b>{{owner}}</b>'s name-tag element.
      </template>
      <script>
        Polymer('name-tag', {
          // initialize the element's model
          ready: function() {
            this.owner = 'Rafael';
          }
        });
      </script>
    </polymer-element>
{% endraw %}

`owner`プロパティは`name-tag`エレメントのデータモデルです。`owner`プロパティを更新すると:

    document.querySelector('name-tag').owner = 'June';

タグの内容を変更できます:

This is **June**'s name-tag element.



## `<template>` エレメント {#template}

HTMLテンプレートエレメントはHTMLのひな形を作って、それを複製して使えるようにします。 `<template>`エレメントの中にあるコンテンツはブラウザには表示されず、`querySelector`を使って取得できないので _隠された_ 状態であり、また、リソースを読み込んだりスクリプトを実行したりすることがないので、_不活性の_ 状態です。

{{site.project_title}}では、テンプレートには特別な用途が2つあります:

*   {{site.project_title}}エレメントの定義において、最初の(トップレベルの)`<template>`エレメントは、カスタムエレメントのshadow DOMを定義します。

*   {{site.project_title}}エレメントの内部では、動的コンテンツを表示するためにテンプレートをデータバインディングと合わせて利用できます。

**注意:** `<template>`エレメントはHTML標準の新しいエレメントです。{{site.project_title}}とは関係なくテンプレートを使う方法についてはHTML5Rocksの[HTML's New  Template Tag](http://www.html5rocks.com/tutorials/webcomponents/template/)を参照して下さい。
{: .alert .alert-info }

## データバインディングとテンプレート

テンプレートはそれ自体が便利なものですが、{{site.project_title}}は宣言的な双方向データバインディングをテンプレートに追加します。データバインディングを使うと、JavaScriptのオブジェクトをテンプレートの_データモデル_として割り当てることが出来ます。データモデルを割り当てられたテンプレートでは:

*   テンプレートのコンテンツのコピー(_テンプレートインスタンス_)を保持します。テンプレートインスタンスはDOMツリーの元になったテンプレートの並びに挿入されます。

*   配列の各アイテムに対して、_テンプレートインスタンスの集合_を保持します。各インスタンスは配列の各アイテムに割り当てられます。

*   各テンプレートインスタンスの内部では、DOMノードの値と、インスタンスに割り当てられたデータモデルの値の間で、双方向データバインディングが維持されます。

以下にデータバインディングを使った{{site.project_title}}エレメントの例を示します:

{%raw%}
    <polymer-element name="greeting-tag">
      <!-- outermost template defines the element's shadow DOM -->
      <template>
        <ul>
          <template repeat="{{s in salutations}}">
            <li>{{s.what}}: <input type="text" value="{{s.who}}"></li>
          </template>
        </ul>
      </template>
      <script>
        Polymer('greeting-tag', {
          ready: function() {
            // populate the element’s data model
            // (the salutations array)
            this.salutations = [
              {what: 'Hello', who: 'World'},
              {what: 'GoodBye', who: 'DOM APIs'},
              {what: 'Hello', who: 'Declarative'},
              {what: 'GoodBye', who: 'Imperative'}
            ];
          }
        });
      </script>
    </polymer-element>
{%endraw%}


[エレメントの定義](polymer.html#element-declaration)で見たとおり、このカスタムエレメントは自身のshadow DOMを定義する`<template>`エレメントを外側に持ちます。

この外側のテンプレート内部には、2つ目のテンプレートがあり、二重の波括弧でくくられた式{%raw%}`{{`&nbsp;`}}`{%endraw%}を含んでいます:

{%raw%}
    <template repeat="{{s in salutations}}">
      <li>{{s.what}}: <input type="text" value="{{s.who}}"></li>
    </template>
{%endraw%}

何がこのテンプレートで行われているのでしょうか？

*  {%raw%}`repeat="{{s in salutations}}"`{%endraw%} という属性はテンプレートが`salutations`配列の各アイテムに対して、DOMフラグメント（インスタンス）を生成することを示しています。

*   テンプレート内のコンテンツは各テンプレートインスタンスがどんな形になるかを定義します。この例では、`<input>`とテキストノードをを子要素に持つ`<li>`です。

*   {%raw%}`{{s.what}}`{%endraw%} と {%raw%}`{{s.who}}`{%endraw%} という二つの式は、`salutations`配列のアイテムに対しるデータバインディングを示します。

{%raw%}`{{`&nbsp;`}}`{%endraw%} 内の値は <em>{{site.project_title}} 式</em>です。このセクションの例では、式はJavaScriptのオブジェクト(例 `salutations`)か、パス(例 `salutations.who`)です。(式にはリテラル値や算術演算子を含めることも出来ます。-- 詳しくは[Expressions](#expressions)を参照して下さい。)

`<greeting-tag>`をDOMで呼び出すと、`salutations`配列が初期化されます:

    this.salutations = [
      {what: 'Hello', who: 'World'},
      {what: 'Goodbye', who: 'DOM APIs'},
      {what: 'Hello', who: 'Declarative'},
      {what: 'Goodbye', who: 'Imperative'}
    ];

これが単なるJavaScriptのデータであることに注意して下さい。**データを特別なオブジェクトに格納する必要がありません**。`this.salutations`配列はテンプレートの_データモデル_になります。

データモデルを作るか変更するかすると、テンプレートの出力結果を見ることが出来ます。以下に例を示します:

![ScreenShot](/images/databinding/example-1.png)

そして、DOMは次のようになります:

![ScreenShot](/images/databinding/example-1-dom.png)

テンプレートが自身のすぐ後に4つのインスタンを作っていることがわかります。


## 動的な双方向データバインディング

サーバでのテンプレート処理とは異なり、{{site.project_title}}でのデータバインディングは_動的_です。データモデルの値を変更すると、DOMはその変更を反映して更新されます。次に示す例ではデータモデルを更新するメソッドを追加します。ボタンを押すと、モデルデータの変更が即座にDOMに反映されます。

{% include samples/databinding/greeting-tag.html %}

しかし、DOMはデータモデルの変更を反映するだけではありません。DOMエレメントでユーザからの入力を受け付けるようにすると、入力内容をデータモデルに_通知_します。

![ScreenShot](/images/databinding/input-to-model.png)

**注意:** [プロパティの監視](polymer.html#observeprops)を使ってデータモデルが変更された時に独自の処理を実行することが出来ます。
{: .alert .alert-info }

最後に、`salutaions`配列にアイテムを追加したり、配列からアイテムを削除したりする例を見てみましょう:

![ScreenShot](/images/databinding/update-model-array.png)

`repeat`属性は配列の各アイテムに対して一つのインスタンスを作ります。`salutations`の中程から二つのアイテムを削除し、その場所に一つのアイテムを追加しました。`<template>`はこれに対応して二つのインスタンスを削除し、同じ場所に新しいインスタンスを一つ作ります。

おわかりになったでしょうか？
データバインディングはデータの配置先と、ドキュメント構造を持ったHTMLを使って、HTMLを作ることが出来ます。この過程はテンプレートに与えるデータによってコントロールされます。

## イベントハンドリングとデータバインディング

データバインディングを使うと、[宣言的なイベントマッピング](polymer.html#declarative-event-mapping) (on-_event_ handlers)を使ってイベントハンドラを追加するのが簡単になります。

{%raw%}
    <template>
      <ul>
        <template repeat="{{s in stories}}">
          <li on-click={{selectStory}}>{{s.headline}}</li>
        </template>
      </ul>
    </template>
{%endraw%}

Often, you’ll want to identify the event with the model data used to generate
the template instance, either to update the model data or to access a piece
of data that isn’t rendered by the template.

You can get the model data from the event’s `target.templateInstance.model`
property. Any identifiers that you could access inside the template are
available as properties on the `.model` object.

For example, the  `selectStory` method might look like this:

    selectStory: function(e, detail, sender) {
      var story = e.target.templateInstance.model.s;
      console.log("Clicked " + story.headline);
      this.loadStory(story.id); // accessing non-rendered data from the model
    }

Continue on to:

<a href="/docs/polymer/binding-types.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Types of bindings</paper-button>
</a>

<a href="/docs/polymer/expressions.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Expressions</paper-button>
</a>
