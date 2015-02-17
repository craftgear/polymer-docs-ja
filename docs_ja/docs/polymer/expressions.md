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

計算済みプロパティでは、式のスコープは常にエレメントそのものです。

`bind`や`repeat`を含まないテンプレートは現在のスコープを共有します。

式を含まない`bind`や`repeat`は、現在のスコープを指定した式を含む`bind`や`repeat`と同じです。

### 入れ子になったスコープのルール {#nested-scoping-rules}

`<template>`に名前付きスコープを持つ複数の`<template>`が含まれている場合、親要素のスコープは全て参照可能です。これには名前付きスコープを**含まない**最初の親要素も含まれます。例えば:

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

言い換えると:

- テンプレートが名前付きスコープを持つ場合、親要素のスコープは全て参照可能です。
- テンプレートが名前付きスコープを含まない場合、親要素のスコープは参照**できません**。

## フィルタリング式 #filters}

式の出力結果を修正するのにフィルタを使うことが出来ます。{{site.project_title}}にはいくつかのデフォルトフィルタが用意されています。フィルタはバインディング内で式をパイプすることで利用します:

<pre class="prettyprint">
{% raw %}{{ <var>expression</var> | <var>filterName</var> }}{% endraw %}
</pre>

{{site.project_title}} には `tokenList`と`styleObject`という二つの定義済みフィルタがあります。[カスタムフィルタ](#custom-filters)を作ることも出来ます。

式の識別子やパスをフィルタの対象にする場合、プロパティの変更では式が再計算されないことに注意して下さい。例えば、次のような式があったとします:

    {% raw %}{{user | formatUserName}}{% endraw %}

`user.firstName`のようなプロパティが変更されたとしても、式は再計算されません。プロパティの変更時にフィルタを再度実行したい場合は、次のようにしてプロパティを明示的に式に含めるようにします:

    {% raw %}{{ {firstName: user.firstName, lastName: user.lastName} | formatUserName}}{% endraw %}

`user.firstName`と`user.lastName`が明示的に式に含まれているため、これらのプロパティは変更監視の対象になります。

### tokenListフィルタ

`tokenList`フィルタは`class`属性に値をバインドする際に便利です。`tokenList`に渡されるオブジェクトに基づいて、動的にクラス名を設定/削除することが出来ます。オブジェクト内のキーが真の値を持つなら、キー名がクラス名として設定されます。

例えば:

{% raw %}
    <div class="{{ {active: user.selected, big: user.type == 'super'} | tokenList}}">
{% endraw %}

`user.selectd == true`かつ`user.type == 'super'`である場合、この例は次のような出力結果になります:

    <div class="active big">

### styleObjectフィルタ

`styleObject`フィルタはCSSスタイルを含むJSONオブジェクトを`style`属性に適した書式に変換します。

プロパティ一つの場合{{site.project_title}}では直接`style`属性にバインド可能です:

{% raw %}
    <div style="color: {{color}}">{{color}}</div>
{% endraw %}

エレメントの`color`プロパティが"red"なら、これは次のような出力結果になります:

    <div style="color: red">red</div>

しかし、キー名と値のペアで複数のスタイルを表現するオブジェクトを`style`属性に適用したい場合は、`styleObject`フィルタをつかって変換することが出来ます。

{% raw %}
    <div style="{{styles | styleObject}}">...</div>
{% endraw %}

この例で、`styles`は`{color: 'red', background: 'blue'}`というオブジェクトであったとすると、`styleObject`フィルタが出力するCSSスタイルは、`"color: 'red'; background: 'blue'"` となります。

### カスタムフィルタを作成する {#custom-filters}

フィルタは入力値を変更して返す関数に過ぎません。エレメントのprototypeにメソッドを追加することで、_カスタムフィルタ_を作成できます。例えば、`toUpperCase`というフィルタを追加するには:

    Polymer('greeting-tag', {
      ...
      toUpperCase: function(value) {
        return value.toUpperCase();
      },

とし、次のようにしてこのフィルタを使います:

{% raw %}
    {{s.who | toUpperCase}}
{% endraw %}

このフィルタは値がDOMに挿入された時に変更を行います。`s.who`の値が`world`なら、出力は`WORLD`となります。同様にDOMの値からデータモデルに逆の変換をするフィルタを作ることも出来ます(例：inputエレメントの値をバインドするような場合)。この場合は、フィルタをオブジェクトにし、`toDOM`と`toModel`という名前の関数を作ります。例えば、データモデルに格納するテキストを常に小文字にするなら、`toUpperCase`フィルタを次のように変更します:

    toUpperCase: {
      toDOM: function(value) {
        return value.toUpperCase();
      },
      toModel: function(value) {
        return value.toLowerCase();
      }
    }

**注意:** ユーザがバインド済みのinputエレメントに値を入力すると、データモデルに値が格納される前に、`toModel`フィルタが実行されます。しかし、`toDOM`フィルタはデータモデルがプログラムで変更された時にしか呼び出されません。したがって、ユーザの入力値は`toDOM`フィルタによって変換されず、大文字にはなりません。ユーザの入力に応じて値を変換するには、`on-input`イベントか、`on-blur`イベントが使えます。
{: .alert .alert-info }

#### フィルタの引数

フィルタには引数を渡すことが出来ます。例えば:

{% raw %}
    {{myNumber | toFixed(2)}}
{% endraw %}

`toFixed`フィルタの実装は次のようになります:

    toFixed: function(value, precision) {
      return Number(value).toFixed(precision);
    }

フィルタの引数は変更監視の対象になります。

#### フィルタの連鎖

出力を別のフィルタに渡すことでフィルタをつなげて使うことが出来ます:

{% raw %}
    {{myNumber | toHex | toUpperCase}}
{% endraw %}

### グローバルフィルタの作成

{{site.project_title}}では個別エレメントのprototypeに[カスタムフィルタ](#custom-filters) を追加することが出来ますが、複数のエレメントで再利用できるグローバルフィルタを登録したいことがあるかもしれません。

グローバルフィルタを登録するには、カスタムフィルタを `PolymerExpressions`オブジェクトのprototypeに追加します。

#### グローバルフィルタの例

文字列を全て大文字に変換するフィルタを作ってみましょう。

{% raw %}
    {{Hello Polymer | uppercase}}
{% endraw %}

`uppercase`をグローバルフィルタとして登録するには、`PolymerExpressions`オブジェクトのprototypeに追加します:

{% raw %}
    PolymerExpressions.prototype.uppercase = function(input) {
      return input.toUpperCase();
    };
{% endraw %}

グローバルフィルタに引数を渡すことも出来ます。次に挙げるのは、配列内で指定した文字から始まる要素だけを返す`startWith`フィルタの例です:

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

グローバルフィルタについてより詳しく知りたい場合は、コミュニティの有志によって作成されている{{site.project_title}}の[フィルタコレクション](https://github.com/addyosmani/polymer-filters)が参考になります。

#### グローバルフィルタをインポートする

複数のエレメントにアクセするフィルタを使うには、スクリプト内でフィルタを定義し、エレメントの最初でそのフィルタをインポートします。
(after polymer.html).


    <link rel="import" href="uppercase-filter.html"/>
