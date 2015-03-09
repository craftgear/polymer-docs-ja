---
layout: default
type: guide
shortname: Docs
title: エレメントのスタイル指定
subtitle: Guide
---

{% include toc.html %}

**注意:** {{site.project_title}}エレメントにスタイルを指定するのは、カスタムエレメントにスタイル指定するのと何ら変わりません。
基本についての完全なガイドは"[A Guide to Styling Elements](/articles/styling-elements.html)"を参照して下さい。
{: .alert }

[standard features for styling Custom Elements](/articles/styling-elements.html)に加えて、{{site.project_title}}にはエレメントのスタイルを完全にコントロールする追加の機能が備わっています。これらは、ちらつき防止（FOUC)、ShadowDOMのポリフィルがスタイルに適用される方法の指定、現在の制限を回避する方法などです。

## ちらつき防止

カスタムエレメントが[upgrade](http://www.html5rocks.com/tutorials/webcomponents/customelements/#upgrades) される前に望ましくない形で表示されてしまうことがあります。これをFOUC(要素のちらつき)問題と呼びます。この問題を緩和するために、{{site.project_title}}では[`:unresolved` 偽クラス](/articles/styling-elements.html#preventing-fouc)を提供しています。単純なアプリケーションの場合は、`unresolved`属性をbodyに追加できます。これによって全てのエレメントがカスタムエレメントにupgradeされるまで、ページは隠された状態になります。

    <body unresolved>

Class name | Behavior
|-
`body[unresolved]` | bodyを `opacity: 0; display: block; overflow: hidden`にします。
`[resolved]` | 200ミリ秒かけてbodyをフェードインさせます。
{: .table .responsive-table .fouc-table }

より細かい制御がしたい場合は、`unresolved`をbodyではなく、個別のエレメントに追加して下さい。これによってページそのものはすぐに表示されますが、unresolvedなエレメントがどのように見える化をコントロールできるようになります:

    <style>
      [unresolved] {
        opacity: 0;
        /* other custom styles for unresolved elements */
      }
    </style>
    <x-foo unresolved>If you see me, elements are upgraded!</x-foo>
    <div unresolved></div>
[`polymer-ready`](/docs/polymer/polymer.html#polymer-ready)イベントが発行されると、{{site.project_title}}は次の処理を実行します:

1. `[unresolved]`属性を取り除く
2. `[resolved]`属性を追加する
3. 最初の`transitioned`イベントを受信したタイミングで`[resolved]`属性を取り除く

### 起動後に要素を表示する {#unveilafterboot}

ページロードのタイミング意外にもちらつきを防止するためにこの表示プロセスを利用できます。
実際には`[unresolved]`属性を対象のエレメントに追加し、エレメントの表示準備が整ったらそれを`[resolved]`属性と置き換えます。例えば、

    element.setAttribute('unresolved', '');

    // ... some time later ...
    element.setAttribute('resolved', '');
    element.removeAttribute('unresolved');

## エレメントにスタイルシートを含める

{{site.porject_title}}では`<polymer-element>`タグ内部にスタイルシートを含めることが出来ます。この機能はShadowDOMの仕様にはないものです。次のようにして利用します:

    <polymer-element name="my-element">
      <template>
        <link rel="stylesheet" href="my-element.css">
         ...
      </template>
      <script>
        Polymer('my-element',...);
      </script>
    </polymer-element>

{{site.project_title}}は自動的に`my-element.css`を`<style>`タグにインライン展開します:

    <polymer-element ...>
      <template>
        <style>.../* Styles from my-element.css */...</style>
         ...
      </template>
      <script>
        Polymer('my-element',...);
      </script>
    </polymer-element>

スタイルシートをテンプレートタグの内側に置くことに注意して下さい。スタイルシートを含める場合は特に`<script>`タグをテンプレートのすぐ下に置くことをおすすめします。もちろんこれは仕様ではなくアドバイスに過ぎません。

## PolyfillによるCSSセレクタ {#directives}

ShadowDOM polyfillを使っている場合は、{{site.projet_title}}によって特別な`polyfill-*`というCSSセレクタが追加されます。
このCSSセレクタを使うことでスタイルの適用方法をより細かく制御できるようになります。

### polyfill-next-selector {#at-polyfill}

`polyfill-next-selector` セレクタはpolyfillが動いている場合にネイティブのCSSセレクタを置き換えるのに使われます。

ネイティブのCSSルールを置き換えるには、`polyfill-next-selector {}` を polyfillによって変更したいセレクタの上に置きます。`polyfill-next-selector`の中で、`content`プロパティを追加します。この値はネイティブのCSSルールとほぼ同じになります。{{site.project_title}}はこの値をネイティブセレクタの機能を補完するために使います。

例えば、{{site.project_title}}の初期のバージョンでは、`::content`を使った複数ノードにまたがるスタイル指定はShadowDOMをネイティブ実装している環境でしか動きませんでした。
しかし現在ではこのかぎりではありません。polyfillによって機能を補助されたブラウザでは、`::content`エレメントは削除されます。polyfill環境では次に示すセレクタは:

    .foo ::content .bar {
      color: red;
    }

このようになります:

    .foo .bar {
      color: red;
    }

これはつまり、`polyfill-next-selector`が必要になるのは、`::content`が削除された時に動かなくなるよようなことをする必要があるときだけ、ということです。

例えば、`::content > *` というスタイル指定はpolyfill環境のブラウザでは動きません。なぜなら、このセレクタは実際には `> *` のように解釈され、これは正しい書式ではないからです。この場合、つぎのように書き直すことが出来ます:

    polyfill-next-selector { content: ':host > *' }
    ::content > * { }

ShadowDOMをネイティブサポートしている環境ではなにも変わりません。polyfill環境下では `::content`で始まるネイティブセレクタの内容は、直前にある `polyfille-next-selector` によって置き換えられます。

### polyfill-rule {#at-polyfill-rule}

`polyfill-rule`セレクタはShadowDOM polyfillが有効になっている時 *だけ* 適用したいルールを作るためのものです。
ShadowDOMをネイティブサポートした環境と、polyfill環境で、同じスタイルが違った表示になる場合は、このセレクタを使って別々のスタイル指定をすることが出来ます。しかし、{{site.project_title}}がスタイル機能を補助するので、このセレクタが必要になることは実際にはまれです。

`polyfill-rule`を利用するには、ルールを作ってスタイルを追加します。次に`content`プロパティを追加し、裂きほど追加したスタイルが適応されるCSSセレクタを指定します。例えば:

    polyfill-rule {
      content: '.bar';
      background: red;
    }

    polyfill-rule {
      content: ':host.foo .bar';
      background: blue;
    }

この二つのルールは、ShadowDOMをネイティブサポートした環境ではなんの効果もありませんが、polyfill環境下では、`content`で指し示したセレクタとして動作します。{{site.project_title}}はまた、エレメント名をルールの先頭に追加します。

先程の例は次のようになります:

    x-foo .bar {
      background: red;
    }

    x-foo.foo .bar {
      background: blue;
    }

### polyfill-unscoped-rule {#at-polyfill-unscoped-rule}

`polyfill-unscoped-rule`セレクタは一点をのぞいて`polyfill-rule`と同じです。違いはpolyfillによってスコープが制限されないということです。セレクタは記述そのままで適用されます。

    polyfill-unscoped-rule {
      content: '#menu > .bar';
      background: blue;
    }

produces:

    #menu > .bar {
      background: blue;
    }

`polyfill-unscoped-rule`はまれなケースで使うだけにすべきです。{{site.project_title}}ではCSSOM用いてスタイルを修飾しており、いくつかのルールはCSSOM経由では正しく解釈されないことが知られています。一つの例はSafariにおける`calc()`です。このようなまれなケースでは`polyfill-unscoped-rule`を利用すべきです。

<!-- {%comment%}
## グローバルなスタイルを定義する

CSSの仕様によると、`@keyframe`や`@font-face`といったある種の@ルールは、`<style scoped>`内部では定義できないことになっています。そのため、これらのルールはShadowDOM内では有効になりません。カスタムエレメント外でこれらのルールを定義する必要があります。

インポートされるHTMLにあるスタイルシートや`<style>`タグは、自動的にメインドキュメントに含まれます:

    <link rel="stylesheet" href="animations.css">

    <polymer-element name="x-foo" ...>
      <template>...</template>
    </polymer-element>

グローバルな`<style>`定義の例は次のようになります:

    <style>
      @-webkit-keyframes blink {
        to { opacity: 0; }
      }
    </style>

    <polymer-element name="x-blink" ...>
      <template>
        <style>
          :host {
            -webkit-animation: blink 1s cubic-bezier(1.0,0,0,1.0) infinite 1s;
          }
        </style>
        ...
      </template>
    </polymer-element>

また、`polymer-scope="global"`属性を使って、インラインスタイルシートや`<style>`タグをグローバルにする方法もあります。

**例:** スタイルシートをグローバルにする

    <polymer-element name="x-foo" ...>
      <template>
        <link rel="stylesheet" href="fonts.css" polymer-scope="global">
        ...
      </template>
    </polymer-element>

`polymer-scope="global"`を指定したスタイルシートはメインページの`<head>`に移動されます。これは一度だけ実行されます。

**Example:** Define and use CSS animations in an element

    <polymer-element name="x-blink" ...>
      <template>
        <style polymer-scope="global">
          @-webkit-keyframes blink {
            to { opacity: 0; }
          }
        </style>
        <style>
          :host {
            -webkit-animation: blink 1s cubic-bezier(1.0,0,0,1.0) infinite 1s;
          }
        </style>
        ...
      </template>
    </polymer-element>

**Note:** `polymer-scope="global"` should only be used for stylesheets or `<style>`
that contain rules which need to be in the global scope (e.g. `@keyframe` and `@font-face`).
{: .alert .alert-error}
{%endcomment%}
 -->

## Controlling the polyfill's CSS shimming {#stylingattrs}

{{site.project_title}} provides hooks for controlling how and where the Shadow DOM polyfill
does CSS shimming.

### Ignoring styles from being shimmed {#noshim}

Inside an element, the `no-shim` attribute on a `<style>` or `<link rel="stylesheet">` instructs {{site.project_title}} to ignore the styles within. No style shimming will be performed.

    <polymer-element ...>
      <template>
        <link rel="stylesheet"  href="main.css" no-shim>
        <style no-shim>
         ...
        </style>
      ...

This can be a small performance win when you know the stylesheet(s) in question do not contain any Shadow DOM CSS features.

### Shimming styles outside of polymer-element {#sdcss}

Under the polyfill, {{site.project_title}} automatically examines any style or link elements inside of a `<polymer-element>`. This is done so Shadow DOM CSS features can be shimmed and [polyfill-*](#directives) selectors can be processed. For example, if you're using `::shadow` and `/deep/` inside an element, the selectors are rewritten so they work in unsupported browsers. See [Reformatting rules](#reformatrules) above.

However, for performance reasons styles outside of an element are not shimmed.
Therefore, if you're using `::shadow` and `/deep/` in your main page stylesheet, be sure to include `shim-shadowdom` on the `<style>` or `<link rel="stylesheet">` that contains these rules. The attribute instructs {{site.project_title}} to shim the styles inside.

    <link rel="stylesheet"  href="main.css" shim-shadowdom>

## Polyfill details

### Handling scoped styles

Native Shadow DOM gives us style encapsulation for free via scoped styles. For browsers
that lack native support, {{site.project_title}}'s polyfills attempt to shim _some_
of the scoping behavior.

Because polyfilling the styling behaviors of Shadow DOM is difficult, {{site.project_title}}
has opted to favor practicality and performance over correctness. For example,
the polyfill's do not protect Shadow DOM elements against document level CSS.

When {{site.project_title}} processes element definitions, it looks for `<style>` elements
and stylesheets. It removes these from the custom element's Shadow DOM `<template>`, rejiggers them according to the rules below, and appends a `<style>` element to the main document with the reformulated rules.

#### Reformatting rules {#reformatrules}

1. **Replace `:host`, including `:host(<compound selector>)` by prefixing with the element's tag name**

      For example, these rules inside an `x-foo`:

        <polymer-element name="x-foo">
          <template>
            <style>
              :host { ... }
              :host(:hover) { ... }
              :host(.foo) > .bar { ... }
            </style>
          ...

      becomes:

        <polymer-element name="x-foo">
          <template>
            <style>
              x-foo { ... }
              x-foo:hover { ... }
              x-foo.foo > .bar, .foo x-foo > bar {...}
            </style>
          ...

1. **Prepend selectors with the element name, creating a descendant selector**.
This ensures styling does not leak outside the element's shadowRoot (e.g. upper bound encapsulation).

      For example, this rule inside an `x-foo`:

        <polymer-element name="x-foo">
          <template>
            <style>
              div { ... }
            </style>
          ...

      becomes:

        <polymer-element name="x-foo">
          <template>
            <style>
              x-foo div { ... }
            </style>
          ...

      Note, this technique does not enforce lower bound encapsulation. For that,
      you need to [forcing strict styling](#strictstyling).

1. **Replace `::shadow` and `/deep/`** with a `<space>` character.

### Forcing strict styling {#strictstyling}

By default, {{site.project_title}} does not enforce lower bound styling encapsulation.
The lower bound is the boundary between insertion points and the shadow host's children.

You can turn lower bound encapsulation by setting `Platform.ShadowCSS.strictStyling`:

    Platform.ShadowCSS.strictStyling = true;

This isn't yet the default because it requires that you add the custom element's name as an attribute on all DOM nodes in the shadowRoot (e.g. `<span x-foo>`).


### Manually invoking the style shimmer {#manualshim}

In rare cases, you may need to shim a stylesheet yourself. {{site.project_title}}'s Shadow DOM polyfill shimmer can be run manually like so:

    <style id="newstyles">
     ...
    </style>

    var style = document.querySelector('#newstyles');

    var cssText = Platform.ShadowCSS.shimCssText(
          style.textContent, 'my-scope');
    Platform.ShadowCSS.addCssToDocument(cssText);

Running this shims the styles, scopes their rules with 'my-scope', and adds the result
to the main document.
