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
The veiling process can be used to prevent FOUC at times other than page load. To do so, apply the `[unresolved]` attribute to the desired elements and swap it out for the `[resolved]` attribute when the elements should be displayed. For example,

    element.setAttribute('unresolved', '');

    // ... some time later ...
    element.setAttribute('resolved', '');
    element.removeAttribute('unresolved');

## Including stylesheets in an element

{{site.project_title}} allows you to include stylesheets in your `<polymer-element>` definitions, a feature not supported natively by Shadow DOM. That is:

    <polymer-element name="my-element">
      <template>
        <link rel="stylesheet" href="my-element.css">
         ...
      </template>
      <script>
        Polymer('my-element',...);
      </script>
    </polymer-element>

{{site.project_title}} will automatically inline the `my-element.css` stylesheet using a `<style>`:

    <polymer-element ...>
      <template>
        <style>.../* Styles from my-element.css */...</style>
         ...
      </template>
      <script>
        Polymer('my-element',...);
      </script>
    </polymer-element>

Be careful to put the stylesheet inside the template. We recommend putting the `<script>` tag below the template, especially if you include a stylesheet, however note this is optional otherwise.

## Polyfill CSS selectors {#directives}

When running under the Shadow DOM polyfill, {{site.project_title}} provides special `polyfill-*`
CSS selectors to give you more control on how style rules are shimmed.

### polyfill-next-selector {#at-polyfill}

The `polyfill-next-selector` selector is used to replace a native CSS selector with one that
will work under the polyfill.

To replace native CSS style rules, place `polyfill-next-selector {}` above the
selector you need to polyfill. Inside of `polyfill-next-selector`, add a
`content` property. Its value should be a CSS selector that is roughly equivalent to
the native rule. {{site.project_title}} will use this value to shim the native selector.

For example, in earlier versions of {{site.project_title}} targeting distributed nodes using `::content` only worked under the native Shadow DOM. This is no longer the case and in polyfilled browsers the `::content` elements will be removed. Under the polyfill, the following selector:

    .foo ::content .bar {
      color: red;
    }

Becomes:

    .foo .bar {
      color: red;
    }

This means you only need `polyfill-next selector` when doing something that would not work if `::content` were removed.

For example: `::content > *` will not work in a polyfilled browser because `> *` is not a valid selector. This selector could be rewritten as follows:

    polyfill-next-selector { content: ':host > *' }
    ::content > * { }

Under native Shadow DOM nothing changes. Under the polyfill, the native selector is replaced with the one defined in its `polyfill-next-selector` predecessor.

### polyfill-rule {#at-polyfill-rule}

The `polyfill-rule` selector is useful for creating style rules that *only* apply when the Shadow DOM polyfill is in use. When you can't write a style rule that works across native and Shadow DOM polyfill, it's your solution. However, because of the style shimming {{site.project_title}} provides, you should rarely need to use this selector.

To use `polyfill-rule`, create the rule and include a list of styles. Then, add a `content` property describing the CSS selector those styles should apply to. For example:

    polyfill-rule {
      content: '.bar';
      background: red;
    }

    polyfill-rule {
      content: ':host.foo .bar';
      background: blue;
    }

These rules are a noop under native Shadow DOM. Under the polyfill, `polyfill-rule` is replaced by the selector in `content`. {{site.project_title}} also prefixes the rule with the element name.

The previous examples become:

    x-foo .bar {
      background: red;
    }

    x-foo.foo .bar {
      background: blue;
    }

### polyfill-unscoped-rule {#at-polyfill-unscoped-rule}

The `polyfill-unscoped-rule` selector is exactly the same as `polyfill-rule` except that the rules inside it are not scoped by the polyfill. The selector you write is exactly what will be applied.

    polyfill-unscoped-rule {
      content: '#menu > .bar';
      background: blue;
    }

produces:

    #menu > .bar {
      background: blue;
    }

You should only need `polyfill-unscoped-rule` in rare cases. {{site.project_title}} uses CSSOM to modify styles and there are a several known rules that don't round-trip correctly via CSSOM (on some browsers). One example using CSS `calc()` in Safari. It's only in these rare cases that `polyfill-unscoped-rule` should be used.

<!-- {%comment%}
## Making styles global

According to CSS spec, certain @-rules like `@keyframe` and `@font-face`
cannot be defined in a `<style scoped>`. Therefore, they will not work in Shadow DOM.
Instead, you'll need to declare their definitions outside the element.

Stylesheets and `<style>` elements in an HTML import are included in the main document automatically:

    <link rel="stylesheet" href="animations.css">

    <polymer-element name="x-foo" ...>
      <template>...</template>
    </polymer-element>

Example of defining a global `<style>`:

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

{{site.project_title}} also supports making a `<style>` or inline stylesheet global using the
`polymer-scope="global"` attribute.

**Example:** making a stylesheet global

    <polymer-element name="x-foo" ...>
      <template>
        <link rel="stylesheet" href="fonts.css" polymer-scope="global">
        ...
      </template>
    </polymer-element>

Stylsheets that use `polymer-scope="global"` are moved to the `<head>` of the main page. This happens once.

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
