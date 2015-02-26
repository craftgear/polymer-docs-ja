---
layout: default
type: guide
shortname: Docs
title: Node.bind()
subtitle: Library

feature:
  summary: "[`Node.bind()`](https://github.com/polymer/NodeBind) は Polymerのデータバンディングライブラリです。このライブラリを使うことで、DOMノードはプロパティをデータモデルにバインドできるようになります。Polymer内部のみならず、単独でも便利なライブラリです。

---

{% include spec-header.html %}

## データバインディングの仕組みを学ぶ

### なぜNode.bind()が必要なのか?

`Node.bind()`は全てのDOMノードに追加される新しいメソッドで、DOMノードのプロパティを与えられたデータモデルにバインドします。これによってアプリケーションではDOMに反映されるデータモデルをJavaScriptで作ることが可能になります。

### 基本的な使い方

"`obj.path.to.value`の値をテキストノードの`.textContent`にバインドします":

    var obj = {
      path: {
        to: {
          value: 'hi'
        }
      }
    };

    var textNode = document.createTextNode('mytext');
    textNode.bind('textContent', new PathObserver(obj, 'path.to.value'));

`path.to.value`の値が変更されると、`Node.bind()`はその値を`.textContent`に随時反映します。

## バインドの種類

バインディング名の意味するところは、`bind()`が呼び出されたノードによって解釈されます。エレメントのうちいくつかは双方向データバインディングに使える特別なプロパティを持っています:

- `Text` ノード - `textContent`プロパティに対するバインディングのみ有効です
- `HTMLInputElement` - `value`プロパティと`checked`プロパティに対するバインディングが有効です
- `HTMLTextareaElement` - `value`プロパティに対するバインディングが有効です
- `HTMLSelectElement` - `value`プロパティと`selectedIndex`プロパティに対するバインディングが有効です

**上記以外の全てのエレメントは属性に対するバインドが有効です**。

### テキストノード

    textNode.bind('textContent', new PathObserver(someObj, 'path.to.value'));

この例では、`Text`ノードの`textContent`プロパティの値が`someObj.path.to.value`の値によって変化するようになります。

<table class="table">
  <tr>
    <th>Case</th><th>Result</th>
  </tr>
  <tr>
    <td>Bound value is interpreted as</td>
    <td><code>String(someObj.path.to.value)</code></td>
  </tr>
  <tr>
    <td>The path is <code>null</code>, <code>undefined</code>, or unreachable</td>
    <td><code>""</code> (the empty string)</td>
  </tr>
</table>

### &lt;input> エレメント {#inputelements}

`<input>`エレメントには二つの特別なプロパティがあります。`value`と`checked`では双方向データバインディングが有効です。

#### value

    myValueInput.bind('value', new PathObserver(someObj, 'path.to.value'));

`input`の`value`プロパティの値が、`String(someObj.path.to.value)`と等しくなるようにしています。バインディング実行時に、指定されたパスに値があれば`value`はその値にセットされます。もしパスに値がなく、データモデルにプロパティを一つ設定することでパスの値が設定できるなら、プロパティの値は`value`と等しくなります。

#### checked

    myCheckboxOrRadioInput.bind('checked', new PathObserver(someObj, 'path.to.value'));

`input`の`checked`プロパティが`Boolean(someObje.path.to.value)`の値と等しくなるようにします。

<table class="table">
  <tr>
    <th>Case</th><th>Result</th>
  </tr>
  <tr>
    <td>Bound value is interpreted as</td>
    <td><code>Boolean(someObje.path.to.value)</code></td>
  </tr>
  <tr>
    <td>The path is <code>null</code>, <code>undefined</code>, or unreachable</td>
    <td><code>false</code> for <code>&lt;input type="checked"></code> and <code>&lt;input type="radio"></code>.</td>
  </tr>
</table>

### &lt;textarea> エレメント {#textarea}

The `<textarea>` element has a special property, `value` for two-way binding.

#### value

    textarea.bind('value', new PathObserver(someObj, 'path.to.value'));

`HTMLTextAreaElement.value` mimics the behavior of `input.value` (see above).

### &lt;select> element {#select}

The `<select>` element has two special properties, `selectedIndex` and `value` for two-way binding.

#### value

    select.bind('value', new PathObserver(someObj, 'path.to.value'));

Instructs the `HTMLSelectElement` to make its `value` property dependent on the
value in `path.to.value`.

#### selectedIndex

    select.bind('selectedIndex', new PathObserver(someObj, 'path.to.value'));

Instructs the `HTMLSelectElement` to make its `selectedIndex` property dependent on the
value in `path.to.value`. Note, "`selectedIndex`" is case sensitive.

<table class="table">
  <tr>
    <th>Case</th><th>Result</th>
  </tr>
  <tr>
    <td>Bound value is interpreted as</td>
    <td><code>Number(someObj.path.to.value)</code></td>
  </tr>
  <tr>
    <td>The path is <code>null</code>, <code>undefined</code>, or unreachable</td>
    <td><code>0</code></td>
  </tr>
</table>

### Element attribute values {#attributevalues}

    myElement.bind('title', new PathObserver(someObj, 'path.to.value'));

Instructs the element to make the value its `title` attribute dependent on the value `someObj.path.to.value`.

<table class="table">
  <tr>
    <th>Case</th><th>Result</th>
  </tr>
  <tr>
    <td>Bound value is interpreted as</td>
    <td><code>String(someObj.path.to.value)</code></td>
  </tr>
  <tr>
    <td>The path is <code>null</code>, <code>undefined</code>, or unreachable</td>
    <td><code>""</code> (the empty string)</td>
  </tr>
</table>

### Element attribute presence {#attributepresence}

    myElement.bind('hidden?', new PathObserver(someObj, 'path.to.value'));

Instructs the element add/remove its `hidden` attribute based on the truthiness of  `someObj.path.to.value`.

<table class="table">
  <tr>
    <th>Case</th><th>Result</th>
  </tr>
  <tr>
    <td>Bound value is interpreted as</td>
    <td><code>""</code> (the empty string) if <code>someObj.path.to.value</code> is reachable and truthy</td>
  </tr>
  <tr>
    <td>Other cases</td>
    <td>The attribute is removed from the element</td>
  </tr>
</table>

## Custom element bindings

[Custom Elements](/platform/custom-elements.html) may choose to interpret bindings
as they wish. They do this by overriding the `bind()` method.

    MyFancyHTMLWidget.prototype.bind = function(name, observable, oneTime) {
      if (name == 'myBinding') {
        // interpret the binding meaning
        // if oneTime is false, this should return an object which
        // has a close() method.
        // this will allow TemplateBinding to clean up this binding
        // when the instance containing it is removed.
      }
      else {
         return HTMLElement.prototype.bind.call(
          this, name, observable, oneTime
        );
      }
    };

If the element does not handle the binding, it should give its super class the
opportunity to by invoking its `bind()` method.
