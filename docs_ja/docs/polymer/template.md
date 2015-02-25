---
layout: default
type: guide
shortname: Docs
title: テンプレートバインディング
subtitle: Library

feature:
  summary: Polymerの[テンプレートバンディング](https://github.com/polymer/TemplateBinding)ライブラリは、[HTML Template Element](http://www.w3.org/TR/html5/scripting-1.html#the-template-element)の機能を拡張したもので、JavaScriptで定義されたデータに紐付けられたコンテンツを生成、操作、削除できるようにしてあります。Polymer内部のみならず、単独でも便利なライブラリです。
参考リンク:
- "HTML5Rocks - HTML's New Template Tag": http://www.html5rocks.com/tutorials/webcomponents/template/
---

{% include spec-header.html %}

## 連プレートの仕組みを学ぶ

テンプレートバインディングは`<template>`エレメントを拡張したもので、データをバインドするための新しい属性が追加されています:

1. `bind`
1. `repeat`
1. `if`
1. `ref`

### 基本的な使い方

#### bind

{% raw %}
    <template bind="{{ singleton }}">
      配列でないモデルデータsingletonにバインドされたインスタンスを作ります
    </template>
{% endraw %}

#### repeat

{% raw %}
    <template repeat="{{ collection }}">
      配列内の各要素ごとにバインドされたインスタンスを作ります。
    </template>
{% endraw %}

名前付きスコープ:

{% raw %}
    <template repeat="{{ user in users }}">
      {{user.name}}
    </template>
{% endraw %}

インデックス付き呼び出し:

{% raw %}
    <template repeat="{{ foo, i in foos }}">
      <template repeat="{{ value, j in foo }}">
        {{ i }}:{{ j }}. {{ value }}
      </template>
    </template>
{% endraw %}

#### if

{% raw %}
    <template bind if="{{ conditionalValue }}">
      conditionalValueが真の値である場合のみバインドされます。
    </template>

    <template if="{{ conditionalValue }}">
      conditionalValueが真の値である場合のみバインドされます。(*bind if*と同じです)
    </template>

    <template repeat if="{{ conditionalValue }}">
      conditionalValueが真の値である場合のみ繰り返しバインドされます。
    </template>
{% endraw %}

#### ref

{% raw %}
    <template id="myTemplate">
      ref属性でこのテンプレートを参照している別のテンプレート内で使われます。
    </template>

    <template bind ref="myTemplate">
      インスタンス生成の際にこのテンプレート内部にある要素は無視され、#myTemplate 内部の要素が代わりに表示されます。
    </template>
{% endraw %}

### テンプレートを有効にする

データモデルをテンプレートに設定することで
Setting data model on the template causes any `bind`, `repeat` or `if` attribute
directive to begin acting:

    document.querySelector('template').model = {...};

### Unbinding a model {#nodeunbind}

`Node.unbind(<property>)` can be used to unbind a property. For example, to unbind
a model set using the `bind` attribute, call `template.unbind('bind')`:

{% raw %}
    <button onclick="removeGo()">test</button>
    <template id="greeting" bind="{{ salutations }}">
      Hello, {{who}} - {{what}}
    </template>

    <script>
      var t = document.querySelector('#greeting');
      var model = {
        salutations: { what: 'GoodBye', who: 'Imperative' }
      };
      t.model = model;

      function removeGo() {
        t.unbind('bind');
      }
    </script>
{% endraw %}

### Examples

Please refer to the [HowTo examples](https://github.com/Polymer/TemplateBinding/tree/master/examples/how_to).

{% include other-resources.html %}
