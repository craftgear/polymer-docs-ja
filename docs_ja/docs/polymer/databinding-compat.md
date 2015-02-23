---
layout: default
type: guide
shortname: Docs
title: データバインディングの互換性について
subtitle: Data-binding
---

{% include toc.html %}

テンプレートの仕様のうちいくつかは、polyfillライブラリで完全に再現できません。以下に挙げるようなそのためいくつかの回避策が必要になります:

ブラウザによっては`<template>`エレメントを`<select>`や`<table>`といったエレメント内部に配置できないことがあります。
テンプレートをサポートしていないブラウザでは、ある種の属性のバインディング（例えば、`<img>`タグの`src`属性など）は正しく動作しません。

## テンプレートを内部に配置できないエレメント

`<template>`がHTMLの仕様になるまでは、`<select>`と`<table>`では子要素を制限するために特別な解析処理を行っていました。この古いルールのせいで、`<template>`をサポートしていないブラウザでは、コンテキストから`<template>`を含む予想外のエレメントを取り出して並置関係にしようとします。

例えば、次の例は`<template>`をサポートしないブラウザでは正常に動きません:

    <!-- Won't work in browsers that don't support <template>. -->
    <table>
      {%raw%}<template repeat="{{tr in rows}}">{%endraw%}
        <tr><td>...</td></tr>
      </template>
    </table>

`<template repeat>`の部分は外部に取り出され、並置関係になります:

    <!-- Unsupported browsers make the child <template> a sibling. -->
    {%raw%}<template repeat="{{tr in rows}}">{%endraw%}
      <tr><td>...</td></tr>
    </template>
    <table>
      ...
    </table>

** `<template>` をサポートしないブラウザでは**, `option`や`<tr>`といったタグで直接`template`属性を使うことで繰り返しができます。

    <table>
      {%raw%}<tr template repeat="{{tr in rows}}">{%endraw%}
        <td>Hello</td>
      </tr>
    </table>

`<select>`/`<option>`を使った例は次のようになります:

    <polymer-element name="my-select">
      <template>
        <select>
          {%raw%}<option template repeat="{{options}}">{{}}</option>{%endraw%}
        </select>
      </template>
      <script>
        Polymer('my-select', {
          ready: function() { this.options = []; }
        });
      </script>
    </polymer-element>
    <script>
      var select = document.createElement('my-select');
      select.options = ['One', 'Two', 'Three'];
    </script>

ユーザが`<template>`をサポートしないブラウザを使っている場合は、次に挙げるエレメントで`template`属性を使って下さい:

* `caption`
* `col`
* `colgroup`
* `optgroup`
* `option`
* `tbody`
* `td`
* `tfoot`
* `th`
* `tr`
* `thead`

**注意:** `<template>`をサポートしているブラウザでは、`<select>`と`<table>`の子要素として`<template>`が使えます。その場合は標準のテンプレートを使って下さい。
{: .alert .alert-info }


    <table>
      {%raw%}<template repeat="{{tr in rows}}">{%endraw%}
        <tr>
          <td>Hello</td>
        </tr>
      </template>
    </table>

## 属性へのバインド

ある種の属性に対するバインド式では、`<template>`をサポートしないブラウザにおいて、副作用が発生することがあります。
例えば、{% raw %}`<img src="/users/{{id}}.jpg">`{% endraw %}を実行すると、404になることがあります。

それに加えて、IEのようなブラウザではある種の属性は値のチェックが行われ、{% raw %}`{{}}`{% endraw %}を用いた置き換えが無効になります。

こういった副作用を防ぐため、ある種の属性でのバインディングでは"_"を先頭につけることが出来ます。

{% raw %}
    <img _src="/users/{{id}}.jpg">
    <div _style="color: {{color}}">
    <a _href="{{url}}">Link</a>
    <input type="number" _value="{{number}}">
{% endraw %}


