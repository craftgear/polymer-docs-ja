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

**例:** エレメント内でCSSアニメーションを定義して利用する

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

**注:** `polymer-scope="global"`はグローバルスコープを含むスタイルシートと`<style>`タグでのみ有効にすべきです。  (例 `@keyframe` や `@font-face`).
{: .alert .alert-error}
{%endcomment%}
 -->

## polyfillのCSS機能補助を制御する {#stylingattrs}

{{site.project_title}}ではどこでどのようにShadowDOMのpolyfillがCSSの機能補助を実行するかを制御するフックが備わっています。

### スタイルに対する機能補助を無効にする {#noshim}

エレメント内で`<style>`や`<link rel="stylesheet">`に`no-shim`属性を追加すると、{{site.projet_title}}はその内部にあるスタイルをpolyfillの対象から外し、機能補助は実行されません。

    <polymer-element ...>
      <template>
        <link rel="stylesheet"  href="main.css" no-shim>
        <style no-shim>
         ...
        </style>
      ...

スタイルシートにShadowDOMのCSS機能が無いとわかっている場合には、この属性を追加することで少しパフォーマンスが改善します。

### polymerエレメント外でCSSの機能補助を利用する {#sdcss}

polyfill環境下では{{site.project_title}}は`<polymer-element>`内にあるスタイルシートやスタイルタグを自動的に検証します。これはShadowDOMのCSSの機能補助が実行され、[polyfill-*](#directives)セレクタが処理されるために行われます。例えば、エレメント内で`::shadow`や`/deep/`を利用していたら、これらのセレクタをサポートしてないブラウザでも動作するように書き換えが行われます。より詳しくは[Reformatting rules](#reformatrules)を参照して下さい。

しかし、パフォーマンス上の理由から、エレメント外のスタイルは機能補助の対象になりません。
したがってメインページのスタイルシートで`::shadow`や`/deep/`を利用している場合には、`<style>``<link rel="stylesheet"`に`shim-shadowdom`属性を追加するのを忘れないようにして下さい。
この属性は{{site.project_title}}にスタイルが機能補助の対象であることを指示します。

    <link rel="stylesheet"  href="main.css" shim-shadowdom>

## polyfillの詳細

### スコープ付きスタイルの取り扱い

ShadowDOMが実装されている環境では、スコープ付きスタイルを使ってスタイルの隠蔽を行えます。未実装のブラウザでは{{site.project_title}}のpolyfillがスコープ付きスタイルの機能の内_いくつか＿を再現しようと試みます。

ShadowDOMのスタイル機能をpolyfillによって再現するのは難しいため、{{site.project_title}}では正確さよりもパフォーマンスと実用性を重んじています。例えば、polyfillではShadowDOMエレメントをドキュメントのCSSの適用対象から除外しません。

{{site.project_title}} がエレメントの定義を解釈する際に、`<style>`エレメントとスタイルシートを探します。これらはエレメントのShadowDOMの`<template>`から削除され、代わりに以下のルールに従ってスタイルを改変し、メインドキュメントの`<style>`エレメントに追加します。

#### 改変ルール {#reformatrules}

1. **`:host`と`:host(<compound selector>)`をエレメントのタグ名を先頭につけて置き換える **

      例えば、以下の`x-foo`にあるルールは:

        <polymer-element name="x-foo">
          <template>
            <style>
              :host { ... }
              :host(:hover) { ... }
              :host(.foo) > .bar { ... }
            </style>
          ...

      次のようになります:

        <polymer-element name="x-foo">
          <template>
            <style>
              x-foo { ... }
              x-foo:hover { ... }
              x-foo.foo > .bar, .foo x-foo > bar {...}
            </style>
          ...

1. **セレクタの先頭にエレメント名が追加され、セレクタがエレメント内に限定される。** これによってエレメントのShadowRoot外にスタイルが影響を及ぼすことを防ぐ。 (例: 上位方向のカプセル化).  
      例えば、以下の`x-foo`にあるルールは:

        <polymer-element name="x-foo">
          <template>
            <style>
              div { ... }
            </style>
          ...

      次のようになります:

        <polymer-element name="x-foo">
          <template>
            <style>
              x-foo div { ... }
            </style>
          ...

      このテクニックは下位方向のカプセル化を保証しないことに注意して下さい。下位方向のカプセル化には [厳格なスタイルを強制する](#strictstyling)が使えます。

1. **`::shadow`と`/deep/`を`<space>`キャラクタで置換えます**

### 厳格なスタイルを強制する {#strictstyling}

デフォルトでは{{site.projet_title}}は下位方向のカプセル化を強制しません。下位方向の境界とは、挿入ポイントとShadhowHostの子要素ののあいだのことです。

`Olatform.ShadowCSS.strictStyling`を設定することで下位方向のカプセル化を有効に出来ます:

    Platform.ShadowCSS.strictStyling = true;

これがまだデフォルトでない理由は、ShadowRootにある全てのDOMノードにエレメント名を属性として追加しなければならないからです。(例 `<span x-foo>`)


### 手動でスタイルの機能補完を有効にする {#manualshim}

まれなケースとして、スタイルシートの機能補完を手動で有効にする必要があるかもしれません。{{site.project_title}}のShadowDOM polyfillの機能は次のようにして手動で実行できます:

    <style id="newstyles">
     ...
    </style>

    var style = document.querySelector('#newstyles');

    var cssText = Platform.ShadowCSS.shimCssText(
          style.textContent, 'my-scope');
    Platform.ShadowCSS.addCssToDocument(cssText);

'my-scope'のスタイルに機能補完を実行し、その結果をメインドキュメントのスタイルに追加しています。
