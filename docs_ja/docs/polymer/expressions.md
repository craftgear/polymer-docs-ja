---
layout: default
type: guide
shortname: Docs
title: 式
subtitle: Data-binding
---

{% include toc.html %}

`<polymer-element>`内部では、{%raw%}`{{`&nbsp;`}}`{%endraw%} を使ってバインディングを行った場合、インライン式や名前付きスコープ、マークアップの繰り返しなどを書くのに{{site.project_title}}の[式ライブラリ](https://github.com/polymer/polymer-expressions)が利用可能です。

式は[計算済みプロパティ](/docs/polymer/polymer.html#computed-properties)を定義するのにも使えます。

## 式の書式

{{site.project_title}}はJavaScriptの厳密なサブセットを{%raw%}`{{}}`{%endraw%}での式としてサポートしています。この機能を使うには、その振る舞いと限界を理解しておくことが重要です:

- インライン式の目的はシンプルな値と関係の表現を可能にすることです。複雑なロジックをHTMLに記述するのは多くの場合良くない実装です。
- 式はページ内のスクリプトを実行する(例 `eval`)ことはありません。グローバルオブジェクト(例 `window`)にはアクセスできません。式はパースされると、式で表現されたパスに含まれる値に変換されます。
- 式を使ってHTMLを挿入することは出来ません。XSSを避けるため、式の出力結果はバインドされた値が挿入される前にHTMLエスケープされます。

**注意:** HTMLを動的に挿入する必要があるというまれな場合には、
[バインドされたHTMLを挿入する](databinding-advanced.html#boundhtml)を参照して下さい。
{: .alert alert-info }

式は次に挙げるJavaScriptのサブセットをサポートします:

| 機能 | 例 | 説明
|---------|
|識別子とパス | `foo`, `match.set.game` | これらの値は、現在のスコープに対する相対パスとして扱われ、展開、監視されます。式内部のどれかの値が変更されると式が再計算されますが、プロパティの変更では再計算は行われません。例えば、`foo.bar`の値を変更しても`foo`は再計算されません。
| 配列としてのアクセス | `foo[bar]` | `foo`と`bar`は識別子かパスです。式は`foo`か`bar`、もしくは`foo[bar]`の値が変更されると再計算されます。
| 論理否定演算子 | `!` |
| 単項演算子 | `+foo`, `-bar` | `Number`に変換されます。 あるいは`Number`に変換された後、否定されます。
| 二項演算子 | `foo + bar`, `foo - bar`, `foo * bar` |  `+`, `-`, `*`, `/`, `%` が利用可能です
| 比較演算子 | `foo < bar`, `foo != bar`, `foo == bar` | `<`, `>`, `<=`, `>=`, `==`, `!=`, `===`, `!==` が利用可能です。
| 論理演算子 | `foo && bar || baz` | `||`, `&&` が利用可能です。
| 三項演算子 | `a ? b : c` |
| グループ化 (括弧) | `(a + b) * (c + d)` |
| リテラル値 | numbers, strings, `null`, `undefined` | エスケープされた文字列と10進数以外の数値はサポートされません。
| 配列とオブジェクト | `[foo, 1]`, `{id: 1, foo: bar}` |
| 関数 | `reverse(my_list)` | 式の値は関数の返り値になります。関数の引数は変更監視の対象であり、引数のどれかが変更されると式は再計算されます。
{: .first-col-nowrap .responsive-table .expressions-table }


JavaScriptのサブセットに加えて、式には_フィルタ_を含めることが出来ます。フィルタはJavaScript式の出力結果を変更します。より詳しくは[四季をフィルタリングする](#filters)を参照して下さい。

## 式を計算する

式は、バインディングか計算済みプロパティの中にあると、パースされます:

<pre class="nocode">
{%raw%}<b>{{</b> <var>expression</var> <b>}}</b>{%endraw%}
</pre>

<pre class="nocode">
<b>[[</b> <var>expression</var> <b>]]</b>
</pre>

<pre class="nocode">
<b>computed: {</b>
  <var>property_name</var><b>: '</b><var>expression</var><b>'
}</b>
</pre>

パース後、式の値は計算され、バインディングの値として計算結果が挿入されます:

{% raw %}
    <div>Jill has {{daughter.children.length + son.children.length}} grandchildren</div>
{% endraw %}

の結果は：
may result in:

    <div>Jill has 100 grandchildren</div>

となります。

標準のバインディング(二重ひげ記法)と計算済みプロパティでは、式内のパスのうちどれかが変更されると、式は再計算されます。

## 式のスコープ {#expression-scopes}

式は現在の_スコープ_で評価されます。スコープはどの識別子やパスが参照可能か定義しています。
`bind`属性、`repeat`属性 あるいは `if`属性における式は、親テンプレートのスコープで評価されます。エレメントの最も外側にあるテンプレートでは、パスと識別子はエレメント自身に対する相対パスとして解釈されます。(そのため`this.prop`は{%raw%}`prop`{%endraw%}と書けます)

For computed properties, the scope of an expression is always the element itself.

Templates that don't include `bind` or `repeat` share the current scope.

A `bind` or `repeat` without an expression is the same as using an expression that
specifies the current scope.

### Nested scoping rules {#nested-scoping-rules}

If a `<template>` using a named scope contains child `<template>`s,
all ancestor scopes are visible, up-to and including the first ancestor **not** using a named scope. For example:

{% raw %}
    <template>
      <!-- outermost template -- element's properties available -->
      <template bind="{{organization as organization}}">
        <!-- organization.* available -->
        <template bind="{{organization.contact as contact}}">
          <!-- organization.* & contact.* available -->
          <template bind="{{contact.address}}">
            <!-- only properties of address are available -->
            <template bind="{{streetAddress as streetAddress}}">
              <!-- streetAddress.* and properties of address are available.
                   NOT organization.* or contact.* -->
            </template>
          </template>
        </template>
      </template>
    </template>
{% endraw %}

In other words:

- If a template uses a named scope, its parent scope is visible.
- If a template uses an unnamed scope, its parent scope is _not_ visible.

## Filtering expressions {#filters}

Filters can be used to modify the output of expressions. {{site.project_title}} supports several
default filters for working with data. They're used in bindings by piping an input expression
to the filter:

<pre class="prettyprint">
{% raw %}{{ <var>expression</var> | <var>filterName</var> }}{% endraw %}
</pre>

{{site.project_title}} provides two predefined filters, `tokenList` and `styleObject`. You can also
create your own [custom filters](#custom-filters).

If your filter depends on the properties of one of the paths or identifiers in your expression,
note that the expression isn't re-evaluated when properties change. For example, if you have an
expression like:

    {% raw %}{{user | formatUserName}}{% endraw %}

The expression isn't re-evaluated when a property, such as `user.firstName` changes. If you need
the filter to be re-run when a property changes, you can include it explicitly in the expression,
like this:

    {% raw %}{{ {firstName: user.firstName, lastName: user.lastName} | formatUserName}}{% endraw %}

Since `user.firstName` and `user.lastName` are included explicitly in this expression, both
properties are observed for changes.

### tokenList

The `tokenList` filter is useful for binding to the `class` attribute. It allows you
to dynamically set/remove class names based on the object passed to it. If the object
key is truthy, the name will be applied as a class.

For example:

{% raw %}
    <div class="{{ {active: user.selected, big: user.type == 'super'} | tokenList}}">
{% endraw %}

results in the following if `user.selected == true` and `user.type == 'super'`:

    <div class="active big">

### styleObject

The `styleObject` filter converts a JSON object containing CSS styles into a string of CSS suitable for
assigning to the `style` attribute.

For simple property values {{site.project_title}} allows you to bind to the `style` attribute
directly:

{% raw %}
    <div style="color: {{color}}">{{color}}</div>
{% endraw %}

If the element's `color` property is "red", this results in the following:

    <div style="color: red">red</div>

However, if you have an object containing a set of styles as name:value pairs,
use the `styleObject` filter to transform it into the appropriate format.

{% raw %}
    <div style="{{styles | styleObject}}">...</div>
{% endraw %}

In this examples `styles` is an object of the form `{color: 'red', background: 'blue'}`, and
the output of the `styleObject` filter is a string of CSS declarations (for example,
`"color: 'red'; background: 'blue'"`).

### Writing custom filters {#custom-filters}

A filter is simply a function that transforms the input value. You can create a _custom filter_ for
your element by adding a method to the element's prototype. For example, to add a filter called
`toUpperCase` to your element:

    Polymer('greeting-tag', {
      ...
      toUpperCase: function(value) {
        return value.toUpperCase();
      },

And use the filter like this:

{% raw %}
    {{s.who | toUpperCase}}
{% endraw %}

This filter modifies values when they're being inserted into the DOM, so if `s.who` is set to `world`,
it displays as `WORLD`. You can also define a custom filter that operates when converting back from
a DOM value to the model (for example, when binding an input element value). In this case, create
the filter as an object with `toDOM` and `toModel` functions. For example, to keep your model text
in lowercase, you could modify the `toUpperCase` as follows:

    toUpperCase: {
      toDOM: function(value) {
        return value.toUpperCase();
      },
      toModel: function(value) {
        return value.toLowerCase();
      }
    }

**Note:** If the user enters text in a bound input field, the `toModel` filter is invoked before the
value stored to the model. However, `toDOM` filter is only called when the model is changed
imperatively. So the text entered by the user isn't filtered (that is, it doesn't
get capitalized). To validate or transform a value as the user types it, you can use a `on-input`
or `on-blur` event handler.
{: .alert .alert-info }

#### Filter parameters

You can pass parameters to a filter. For example:

{% raw %}
    {{myNumber | toFixed(2)}}
{% endraw %}

The code for the `toFixed` filter could look like this:

    toFixed: function(value, precision) {
      return Number(value).toFixed(precision);
    }

The parameters passed to a filter are observed for changes.

#### Chaining filters

You can also chain filters, passing the output of one filter to another:

{% raw %}
    {{myNumber | toHex | toUpperCase}}
{% endraw %}

### Writing global filters


Although {{site.project_title}} supports adding [custom filters](#custom-filters) to the element prototype,
you may want to register global filters which can be reused across multiple elements.

To register a global filter, add a custom filter to the prototype of the
`PolymerExpressions` object.

#### Examples

Let's create a filter for transforming the letters in any string to uppercase.

{% raw %}
    {{Hello Polymer | uppercase}}
{% endraw %}

To register `uppercase` as a global filter, add it to the prototype for
`PolymerExpressions` object:

{% raw %}
    PolymerExpressions.prototype.uppercase = function(input) {
      return input.toUpperCase();
    };
{% endraw %}

You can also pass parameters to global filters. Here's an example of a `startsWith`
filter that returns only those items in an array that begin with a certain letter:

{% raw %}
    {{arrayOfFriends | startsWith('M')}}
{% endraw %}

Filter definition:

{% raw %}
    PolymerExpressions.prototype.startsWith = function (input, letter) {
      return input.filter(function(item){
        return item[0] === letter;
      });
    };
{% endraw %}

A useful set of community-driven [collection](https://github.com/addyosmani/polymer-filters) of {{site.project_title}} filters is available if you need further
examples of global filters.

#### Importing global filters

To use a filter across multiple elements, define it in a script and import it at the top of your element
(after polymer.html).


    <link rel="import" href="uppercase-filter.html"/>
