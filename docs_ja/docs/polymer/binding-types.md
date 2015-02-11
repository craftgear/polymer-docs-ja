---
layout: default
type: guide
shortname: Docs
title: バインディングの種類
subtitle: Data-binding
---

{% include toc.html %}

データをテンプレートにバインドする方法はいくつかあります:

*   `bind`属性に一つのオブジェクトを指定してテンプレートのインスタンスを一つ作る
*   `repeat`属性にオブジェクトの配列を指定して、テンプレートのインスタンスを複数作る
*   `if`属性に渡された値が真と判断された場合にのみ、テンプレートのインスタンスを作る

**注意:** テンプレートへの値のバインドは{{site.project_title}}エレメント内部でのみ有効です。例えば、`<template>`エレメントが直接`<body>`タグ内にある場合には、`bind`属性はここで解説したようには動きません。テンプレートへの値のバインドを{{site.project_title}}の外で行う必要がある場合は、 [{{site.project_title}} エレメントの外でデータバインディングを行う](/docs/polymer/databinding-advanced.html#bindingoutside)を参照して下さい。
{: .alert .alert-info }

テンプレート_内部_でバインディングを行う場合、_ノードバインディング_がデータモデルの値をDOMノードに結びつけます。ノードバインディングは、バインディングが起こったエレメントの種類に基づいてノードによって解釈されます。詳しくは[Node bindings](#node-bindings)を参照して下さい。

## 単独のテンプレートインスタンス

`bind`属性を使うことで、一つのオブジェクトに結びついた単独のテンプレートインスタンスを作ることが出来ます。

{% raw %}
    <template>
      <template bind="{{person}}">
        This template can bind to the person object’s properties, like
        {{name}}.
      </template>
    </template>
{% endraw %}

`person`はオブジェクトです（より正確には、オブジェクトとして評価される[{{site.project_title}} 式](/docs/polymer/expressions.html)です）。

テンプレート内部でのバインディングはバインドされるオブジェクトのコンテキストで評価されます。例えば、`person`が`name`プロパティを持っていれば、{%raw%}`{{name}}`{%endraw%}は`person.name`の値として評価されます。

オブジェクトをバインドする際に_名前付きスコープ_を作ることが出来て便利です:

{% raw %}
    <template>
      <template bind="{{person as p}}">
        This template uses a named scope to access properties, like
        {{p.name}}.
      </template>
    </template>
{% endraw %}

この例では、`p`という名前付きスコープを使って`person`オブジェクトのプロパティにアクセスすることが出来ます。名前付きスコープはテンプレートを入れ子にする際に便利です。



## テンプレートの繰り返し

テンプレートの繰り返しでは、配列の各アイテムに対して一つのテンプレートインスタンスが生成されます。各インスタンスは配列の各アイテムに結び付けられます。

連プレートの繰り返しの最も単純な形は次のようになります:

{% raw %}
    <template>
      <template repeat="{{array}}">
        Creates an instance with {{}} bindings  for every element in the array collection.
      </template>
    </template>
{% endraw %}

`array`の現在のアイテムを、空のバインド式{%raw%}`{{}}`{%endraw%}で参照しています。
この式は現在のバインドスコープに一致します。現在のアイテムのプロパティを参照するには{%raw%}<code>{{<var>propertyname</var>}}</code>{%endraw%}を使います。

`bind`属性と同様に、`repeat`属性でも名前付きスコープが使えます:

{% raw %}
    <template>
      <template repeat="{{user in users}}">
        {{user.name}}
      </template>
    </template>
{% endraw %}

`repeat`属性と共に名前付きスコープを使うと、次のようにして各アイテムの配列内のインデックスを参照できます。

{% raw %}
    <template>
      <template repeat="{{user, userIndex in users}}">
        <template repeat="{{userFile, userFileIndex in user}}">
          {{userIndex}}:{{userFileIndex}}.{{userFile}}
        </template>
      </template>
    </template>
{% endraw %}

`bind`属性と同様に、`repeat`属性の値を省略して、親のスコープを継承することが出来ます。
例えば、次のようなオブジェクトの配列があったとしましょう:

    this.items = [
      {name: "Milk"},
      {name: "Bread"},
      {name: "Cereal"}
    ];

配列と、配列内の各アイテムに次のようにして個別にアクセスできます:

{% raw %}
    <template>
      <template bind="{{items}}">
        // {{length}} evaluates as items.length
        <p>Item count: {{length}}</p>
        <ul>
        <template repeat>
          // {{name}} here evaluates as the name of a single item
          <li>{{name}}</li>
        </template>
        </ul>
      </template>
    </template>
{% endraw %}

出力結果は:

Item count: 3

*   Milk
*   Bread
*   Cereal

となります。

## 条件付きテンプレート

条件付きテンプレートでは`if`属性を使ってテンプレートインスタンスを作るかどうかを決めます。

{% raw %}
    <template>
      <template if="{{conditionalValue}}">
        Binds if and only if conditionalValue is truthy.
      </template>
    </template>
{% endraw %}

条件付きテンプレートでは{%raw%}`bind={{expression}}`{%endraw%}書式を使って明示的にバインドするオブジェクトを指定できます。

明示的なバインドがない場合、入れ子になったテンプレートは親テンプレートのスコープを継承します。条件付きテンプレートは次のようにして使われることが多いです:

{% raw %}
    <template>
      <template bind="{{myOptions as m}}">
        <template if="{{m.showCounter}}">
          <div>Counter: {{m.counter}}</div>
        </template>
      </template>
    </template>
{% endraw %}

テンプレートの入れ子に関してより詳しくは[式のスコープ](/docs/polymer/expressions.html#expression-scopes)を参照して下さい。

`if`は`repeat`と一緒に使うことが出来ます。

{% raw %}
    <template>
      <template bind="{{myList as list}}">
        <template repeat="{{item in list.items}}" if="{{list.showItems}}">
          <li>{{item.name}}</li>
        </template>
      </template>
    </template>
{% endraw %}

## 参照を使ってテンプレートをインポートする

別々の場所でテンプレートを再利用したい、あるいは別の場所で生成されたテンプレート参照したいことがあるかもしれません。
そんな時には`ref`が役に立ちます:

{% raw %}
    <template>
      <template id="myTemplate">
        Used by any template which refers to this one by the ref attribute
      </template>

      <template bind ref="myTemplate">
        When creating an instance, the content of this template will be ignored,
        and the content of #myTemplate is used instead.
      </template>
    </template>
{% endraw %}

`ref`属性を使って、ツリー構造のようなテンプレートの再起を表現できます:

{% raw %}
    <template>
      <template>
        <ul>
        <template repeat="{{items}}" id="t">
          <li>{{name}}
          <ul>
            <template ref="t" repeat="{{children}}"></template>
          </ul>
        </li>
      </template>
    </template>
{% endraw %}

さらに、テンプレートを動的に参照するために`ref`の参照先を_自分自身_にすることが出来ます:

{% raw %}
    <template>
      <template bind ref="{{node.nodeType}}"></template>
    </template>
{% endraw %}

## ノードバインディング

ノードバインディングはテンプレートのコンテンツの各バインドについて行われます。ノードバインディングはデータモデルの値とDOMノードのつながりに名前をつけます。

ノードがバインディングを解釈する方法は_エレメントの種類_と_バインド名_に依存します。{{site.project_title}}では、バインド名はマークアップ内でバインディングが起こった場所に基づきます:

* {%raw%}`<span>{{someText}}</span>`{%endraw%}のようなエレメントのテキストコンテンツのバインディングは、`textContent`という名前になります。
* {%raw%}`<span style="{{someStyles}}">`{%endraw%} のようなエレメントの属性値へのバインディングは属性名をバインド名に使います。


### テキストに対するバインディング

タグ内でのバインディングは、エレメントに`textContent`バインディングを生成します。

{% raw %}
    <p>This paragraph has some {{adjective}} text.</p>
{% endraw %}

全てのテキストノードにおける`textContent`バインディングを一方通行です。つまり、ノードに結び付けられたデータモデルの値を変更すると、テキストノードの値も変更されますが、テキストノードの値を変更してもデータモデルは変更されません。

### 属性に対するバインディング

属性に値をバインドすると、属性名がバインディング名になります。例えば、次のバインディングの名前は`style`になります。

{% raw %}
    <span style="color: {{myColor}}">Colorful text!</span>
{% endraw %}

バインド先のエレメントに応じて、この種のバインディングは次のように振る舞います:

- _ほとんどの_標準DOMエレメントにおいては、この種のバインディングは属性に対する一方通行のバインディングです。例えば、データモデルの`myColor`プロパティを変更するとエレメントのテキスト色が変わりますが、プログラムで`style`属性を変更しても、データモデルの`myColor`プロパティは_変更されません_。

- `input`, `option`, `select`, `textarea` といったフォームの入力エレメントは、特定の属性について双方向データバインディングをサポートしています。

- {{site.project_title}}エレメントは公開プロパティに対する双方向データバインディングをサポートしています。`publish`オブジェクトか`attributes`属性を使ってプロパティを公開しているなら、双方向データバインディングが有効です。

- カスタムエレメントは他の方法でバインディングを解釈することが出来ます。{{site.project_title}}エレメント以外のカスタムエレメントは[Node.bind](node_bind.html)ライブラリを使ってデフォルトのバインディングの名前の付け方を変更できます。

### 入力値に対するバインディング

Two-way bindings are supported as a special case on some user input elements. Specifically, the following attributes support two-way bindings:

- `input` element: `value` and `checked` attributes.
- `option` element: `value` attribute.
- `select` element: `selectedIndex` and `value` attributes.
- `textarea` element: `value` attribute.

### Binding to {{site.project_title}} published properties

When you bind to a [published property](polymer.html#published-properties) on a {{site.project_title}} element, you get a two-way binding to the property.

In the following sample, the `intro-tag` binds to a published property on the `say-hello` element:

{% raw %}
    <!-- say-hello element publishes the 'name' property -->
    <polymer-element name="say-hello" attributes="name">
      <template>
        Hello, <b>{{name}}</b>!
      </template>
      <script>
        Polymer('say-hello', {
          ready: function() {
            this.name = 'Stranger'
          }
        });
        </script>
    </polymer-element>
    <polymer-element name="intro-tag" noscript>
      <template>
        <!-- bind yourName to the published property, name -->
        <p><say-hello name="{{yourName}}"></say-hello></p>
        <!-- bind yourName to the value attribute -->
        <p>What's your name? <input value="{{yourName}}" placeholder="Enter name..."></p>
      </template>
    </polymer-element>

    <intro-tag></intro-tag>
{% endraw %}

Here, `yourName` is bound to _both_ the `say-hello` element's `name` property and
the `input` element's `value` attribute. Both bindings are two-way, so when the user enters
a name, it's pushed into the `say-hello` element's `name` property. If you change the
value of the `name` property, the value is pushed into the `input` element.

**Note:** The `intro-tag` element doesn't define a `yourName` property. In this case, the data
binding system creates the property automatically.
{: .alert .alert-info }


#### Binding objects and arrays to published properties

Most of the examples show data binding with simple string values,
but {{site.project_title}} lets you bind references between elements
using published properties.

Let's modify the `name-tag` example to take an object instead of individual
properties.

    <polymer-element name="name-tag" attributes="person">
      <template>
        Hello! My name is <span style="color:{%raw%}{{person.nameColor}}{%endraw%}">
        {%raw%}{{person.name}}{%endraw%}</span>
      </template>
      <script>
        Polymer('name-tag', {
          created: function() {
            this.person = {
              name: "Scott",
              nameColor: "orange"
            }
          }
        });
      </script>
    </polymer-element>

Now, imagine we make a new component called `<visitor-creds>` that uses `name-tag`:

    <polymer-element name="visitor-creds">
      <template>
        <name-tag person="{%raw%}{{person}}{%endraw%}"></name-tag>
      </template>
      <script>
        Polymer('visitor-creds', {
          created: function() {
            this.person = {
              name: "Scott2",
              nameColor: "red"
            }
          }
        });
      </script>
    </polymer-element>

When an instance of `<visitor-creds>` is created, its `person` property (an object)
is also bound to `<name-tag>`'s `person` property. Now both components are using
the same `person` object.



### Conditional attributes

For boolean attributes, you can control whether or not the attribute appears using the special conditional attribute syntax:

{% raw %}
<pre class="prettyprint">
<var>attribute</var>?={{<var>boolean-expression</var>}}
</pre>
{%endraw%}

If _boolean-expression_ is truthy, _attribute_  appears in the markup; otherwise it is omitted. For example:

{% raw %}
    <span hidden?="{{isHidden}}">This may or may not be hidden.</span>
{% endraw %}

### One-time bindings

{% include experimental.html %}

Sometimes, you may not need dynamic bindings. For these cases, there are one-time bindings.

Anywhere you use {% raw %}`{{}}`{% endraw %} in expressions, you can use double brackets
(`[[]]`) to set up a one-time binding. The binding becomes inactive after {{site.project_title}}
sets its value for the first time.

Example:

    <input type="text" value="this value is inserted once: [[ obj.value ]]">

One time bindings can potentially be a performance win if you don't need the overhead of setting up property observation.
