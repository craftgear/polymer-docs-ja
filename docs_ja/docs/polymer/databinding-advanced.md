---
layout: default
type: guide
shortname: Docs
title: データバインディングの深い話
subtitle: Data-binding
---

<style>
pre strong {
  color: #000;
}
</style>

{% include toc.html %}

このセクションでは{{site.project_title}}アプリケーションでデータバインディングを使うのに必要のない、より深い話を取り扱います。

## データバインディングの仕組み

なにがデータバインディングではないかを最初に理解するのが、データバインディングを理解する最も簡単な方法でしょう。データバインディングは今までのテンプレートシステムとは違います。

今までのAJAXアプリケーションでは、テンプレートシステムはエレメントのinnerHTMLを置き換える仕組みのことでした。innerHTMLには複雑なDOMサブツリーが含まれているため、この方法には二つの欠点があります:

既存の子ノードを置き換えると、その時点でのDOMノードの状態を破壊することになります。例えば、イベントリスナや入力値などが失われます。
わずか2，3の値を変更しただけでも、子ノード全体が破棄され、再生成されるのです。

対照的に、{{site.project_title}}のデータバインディングでは、**必要最小限の変更をDOMに加えます**。テンプレートインスタンスによって表現されるDOMノードは、対応するデータモデルが存在する限り、そのまま維持されます。

次に挙げるDOMを例にとって考えてみましょう。このDOMにはテンプレートと、テンプレートインスタンスがあります:

{% raw %}
    <table>
        <template repeat="{{item in items}}">
          <tr><td> {{item.name}} </td><td> {{item.count}} </td></tr>
        </template>
       <tr><td> Bass </td><td> 7 </td></tr>
       <tr><td> Catfish </td><td> 8 </td></tr>
       <tr><td> Trout </td><td> 0 </td></tr>
    </table>
{% endraw %}

配列を`item.count`で並べ替えした場合、{{site.project_title}}は単に対応するDOMサブツリーの順番を並べ替えるだけです。ノードが破棄されたり、作られたりすることはありません。ただひとつの変化は太字で示した二つのノードの順番だけです:

{% raw %}
<pre>
&lt;table>
    &lt;template repeat="{{item in items}}">
      &lt;tr>&lt;td> {{item.name}} &lt;/td>&lt;td> {{item.count}} &lt;/td>&lt;/tr>
    &lt;/template>
   <span class="nocode"><strong>&lt;tr>&lt;td> Catfish &lt;/td>&lt;td> 8 &lt;/td>&lt;/tr>
   &lt;tr>&lt;td> Bass &lt;/td>&lt;td> 7 &lt;/td>&lt;/tr></strong></span>
   &lt;tr>&lt;td> Trout &lt;/td>&lt;td> 0 &lt;/td>&lt;/tr>
&lt;/table>
</pre>
{% endraw %}

`item.count`を一か所変更した場合、DOMツリーで変更が起こるのは太字で示したバインドされた値のみです:

{% raw %}
<pre>
&lt;table>
    &lt;template repeat="{{item in items}}">
      &lt;tr>&lt;td> {{item.name}} &lt;/td>&lt;td> {{item.count}} &lt;/td>&lt;/tr>
    &lt;/template>
   &lt;tr>&lt;td> Catfish &lt;/td>&lt;td> 8 &lt;/td>&lt;/tr>
   &lt;tr>&lt;td> Bass &lt;/td>&lt;td> 7 &lt;/td>&lt;/tr>
   &lt;tr>&lt;td> Trout &lt;/td>&lt;td><span class="nocode"><strong> 2 </strong></span>&lt;/td>&lt;/tr>
&lt;/table>
</pre>
{% endraw %}


### データバインディングがテンプレートインスタンスを監視する方法

テンプレートがインスタンスを生成すると、テンプレートのすぐ後ろにインスタンスを挿入します。
そのため、テンプレート自身が最初のインスタンスのマーカーとして機能します。各テンプレートインスタンスでは、テンプレートの最後にある、区切りノードが監視の対象になります。簡単な例では区切りノードはテンプレートの最後のノードです。

次の図でテンプレートとそのインスタンスのDOMを示します:

{% raw %}
<pre>
    &lt;template repeat="{{item in myList}}">
       &lt;img>
      &lt;span>{{item.name}}&lt;/span>
    &lt;/template>
    &lt;img>
    &lt;span>foo&lt;/span>   <span class="nocode" style="color: red"><em>⇐ terminating node in template instance</em></span>
    &lt;img>
    &lt;span>bar&lt;/span>   <span class="nocode" style="color: red"><em>⇐ terminating node in template instance</em></span>
    &lt;img>
    &lt;span>baz&lt;/span>   <span class="nocode" style="color: red"><em>⇐ terminating node in template instance</em></span>
</pre>
{% endraw %}

並置関係にあるノード（とその子ノード）はテンプレートノードのすぐ後に続いて配置され、最初のテンプレートインスタンスのDOMを作る区切りノードを含みます。他のテンプレートインスタンスも同様に配置されます。

myList配列内のオブジェクトが移動・削除されると、対応するDOMノードも移動・削除されます。

テンプレートが入れ子になっている場合は、区切りノードの識別はもっと複雑になります。次のようなテンプレートを考えてみましょう:

{% raw %}
    <template repeat="{{user in users}}">
      <p>{{user.name}}</p>
      <template repeat="{{alias in user.aliases}}">
         <p>a.k.a. {{alias}}</p>
      </template>
    </template>
{% endraw %}

この例では、外側のテンプレートの最後のノードが内側のテンプレートです。しかし、外側のテンプレートインスタンスが生成されると、内側のテンプレートは自分自身のインスタンスを生成します。（次の例では読みやすくするために、テンプレートインスタンスの前後にスペースを入れてあります。）

{% raw %}
<pre class="prettyprint">
&lt;template repeat="{{user in users}}">
  &lt;p>{{user.name}}&lt;/p>
  &lt;template repeat="{{alias in user.aliases}}">
     &lt;p>a.k.a. {{alias}}&lt;/p>
  &lt;/template>
&lt;/template>

&lt;p>Bob&lt;/p>              <span class="nocode" style="color: red"><em>⇐ start of 1st outer template instance</em></span>
&lt;template repeat="{{alias in user.aliases}}">
  &lt;p>a.k.a. {{alias}}&lt;/p>
&lt;/template>

&lt;p>a.k.a. Lefty&lt;/p>     <span class="nocode" style="color: red"><em>⇐ 1st inner template instance</em></span>

&lt;p>a.k.a. Mr. Clean&lt;/p> <span class="nocode" style="color: red"><em>⇐ 2nd inner template instance</em></span>
                         <span class="nocode" style="color: red"><em> (terminating node for outer template instance)</em></span>


&lt;p>Elaine&lt;/p>           <span class="nocode" style="color: red"><em>⇐ start of 2nd outer template instance</em></span>
&lt;template repeat="{{alias in user.aliases}}">
  &lt;p>a.k.a. {{alias}}&lt;/p>
&lt;/template>

&lt;p>a.k.a. The Wiz&lt;/p>    <span class="nocode" style="color: red"><em>⇐ 1st inner template instance</em></span>
                          <span class="nocode" style="color: red"><em> (terminating node for outer template instance)</em></span>
</pre>
{% endraw %}

この例では、外側のテンプレートインスタンスの区切りノードは内側のテンプレートの最終インスタンスの区切りノードと同じになっています。

### テンプレートから生成されたDOMノードを複製する

通常、**テンプレートから生成されたDOMノードを手動で複製する必要はありません**; バインディングを使ってモデルからテンプレートインスタンスを作ることで多くの場合用が足ります。

もし、_どうしても_テンプレートから生成されたDOMノードを複製する必要があるなら、テンプレートインスタンスの区切りノードを含めるようにするのが無難です。これを実現する最も簡単な方法は、テンプレートの中身を別のエレメントでくるむことです:

{% raw %}
    <template repeat="{{item in listItems}}">
      <section on-click={{rowSelected}}>
        <h2>{{item.title}}</h2>
        <p>{{item.text}}</p>
      </section>
    </template>
{% endraw %}

この例では、外側の<section>が各テンプレートインスタンスの区切りノードになります。<section>ノードを削除しない限り、<section>内部のDOMノードを好きなように複製できます。
例えば、rowSelectedイベントハンドラがsectionがクリックされた時に呼び出され、処理を行うことができます:

{% raw %}
    rowSelected: function(e, detail, sender) {
      var blinkTag = document.createElement('blink-tag');
      blinkTag.textContent = 'BREAKING NEWS';
      sender.insertBefore(blinkTag, sender.querySelector('p'));
    }
{% endraw %}

**注意**: 実際には、データモデルに値を設定して、条件付きテンプレートを使うほうがより簡単で簡潔に同じことを行えます。この例では単にデータバインディングがインスタンスの複製をどのように処理するかを示したにすぎません。


sectionをクリックすると、次のようにDOMが変化します。（読みやすくするためにスペースを追加してあります）:

{% raw %}
    <template repeat="{{item in listItems}}">
      <section on-click={{rowSelected}}>
        <h2>{{item.title}}</h2>
        <p>{{item.text}}</p>
      </section>
    </template>

    <section>
      <h2>Bigfoot spotted in lower Haight!</h2>
      <p>Police cite, release famous urban legend.</p>
    </section>

    <section>
      <h2>Shadow DOM lands in all major browsers</h2>
      <blink-tag>BREAKING NEWS</blink-tag>
      <p>Cheering crowds greet announcement.</p>
    </section>
{% endraw %}

テンプレートは区切りノードを目印にしてインスタンスを区別するため、インスタンスに対する変更は並べ替えが行われてもそのままです:

{% raw %}
    <template repeat="{{item in listItems}}">
      <section on-click={{rowSelected}}>
        <h2>{{item.title}}</h2>
        <p>{{item.text}}</p>
      </section>
    </template>

    <section>
      <h2>Shadow DOM lands in all major browsers</h2>
      <blink-tag>BREAKING NEWS</blink-tag>
      <p>Cheering crowds greet announcement.</p>
    </section>

    <section>
      <h2>Bigfoot spotted in lower Haight!</h2>
      <p>Police cite, release famous urban legend.</p>
    </section>
{% endraw %}


もちろん、データモデルにバインドされている値を変更した場合は、データモデルの変更で上書きされます。双方向データバインディングはインプット要素のDOMに対してのみ有効です。プログラムからDOMノードを変更した場合には双方向にはなりません。

## {{site.project_title}}エレメント外でデータバインディングを使う {#bindingoutside}

{{site.project_title}} のデータバインディングは{{site.project_title}}内で有効です。データバインディングを他の場所で使うには、二つの方法があります:

*   {{site.project_title}}を使っているなら、[auto-binding template](#autobinding)を利用して、カスタムエレメントを作ることなくデータバインディングを利用できます。

*   {{site.project_title}}を使って_いない_なら、[Template Binding](/docs/polymer/template.html) ライブラリを直接使って下さい。このライブラリは{{site.project_title}}内部で使われており、単独でも利用可能です。
*   If you _aren't_ using the rest of {{site.project_title}}, use the
    [Template Binding](/docs/polymer/template.html) library directly. The template binding library is
    used internally by {{site.project_title}}, and can be used directly, with or without the rest of
    {{site.project_title}}. (Note that if you use template binding by itself, you cannot use {{site.project_title}}
    expressions.)

**Note:** Earlier versions of {{site.project_title}} included an element called `<polymer-body>`.
If you were using `<polymer-body>` previously, the closest substitute is an auto-binding template.
{: .alert .alert-info }

### Using the auto-binding template element {#autobinding}

The `auto-binding` element is a {{site.project_title}} custom element that extends the standard
`<template>` element. You can use it when you want to use {{site.project_title}} data
binding in a page without having to create a custom element just for this purpose. Auto-binding
templates support a subset of the features available when you create your own custom element:

-   Full-featured data binding, with {{site.project_title}} expressions.
-   [Declarative event mapping](polymer.html#declarative-event-mapping).
-   [Automatic node finding](polymer.html#automatic-node-finding).

For an auto-binding template, the data model is on the template itself. For example, to use data
binding at the top level of a page:

{% include samples/databinding/auto-binding.html %}

The auto-binding template inserts the instances it creates immediately after
itself in the DOM tree (_not_ in its shadow DOM). In this case, the quotes are
inserted as children of the `body` element.

After adding the instances, the auto-binding template fires the `template-bound`
event.

The `auto-binding` element is currently included automatically when you load the
{{site.project_title}} library.

## Inserting data-bound HTML {#boundhtml}

The {{site.project_title}} data binding escapes any HTML in the bound data.
This avoids many potential security pitfalls with including arbitrary HTML.

However, for those special cases where you need to add HTML dynamically, {{site.project_title}} 
elements provide the `injectBoundHTML` instance method. `injectBoundHTML` replaces
the contents of a target element with an arbitrary block of HTML. Any data binding 
expressions in the HTML are bound to the element.

For example, in the following example, a message is injected into the `message_area` `<div>`.

Changing the `message` property changes the data displayed in the `message_area`.

{%raw%}
    <polymer-element name="my-element">
      <template>
        <div id="message_area"></div>
      </template>
      <script>
        Polymer({
          message: 'hi there',
          ready: function() {
            this.injectBoundHTML('<b>{{message}}</b>', this.$.message_area);
          }
        });
      </script>
    </polymer-element>
{%endraw%}

Note that the rules for data binding using `injectBoundHTML` are the same as the rules for 
standard data binding. For example, if `message` contains HTML, the HTML is escaped.
