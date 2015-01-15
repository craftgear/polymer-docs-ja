---
layout: default
title: shadow DOMについて
type: start
shortname: Platform
subtitle: ローカルDOMを定義し、要素の合成を行う

feature:
  spec: https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/shadow/index.html
  code: https://github.com/Polymer/webcomponentsjs
  summary: "Shadow DOM is designed to provide encapsulation by hiding DOM subtrees under shadow
roots. It provides a method of establishing and maintaining functional boundaries
between DOM trees and how these trees interact with each other within a document,
thus enabling better functional encapsulation within the DOM."

links:
- "What the Heck is Shadow DOM?": http://glazkov.com/2011/01/14/what-the-heck-is-shadow-dom/
- "Web Components Explained - Shadow DOM": https://dvcs.w3.org/hg/webcomponents/raw-file/57f8cfc4a7dc/explainer/index.html#shadow-dom-section
- "HTML5Rocks - Shadow DOM 101": http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/
- "HTML5Rocks - Shadow DOM 201: CSS and Styling": http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom-201/
- "HTML5Rocks - Shadow DOM 301: Advanced Concepts & DOM APIs": http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom-301/

---

{% include toc.html %}

## なぜShadow DOMが必要なのか?

shadow DOMはコンテンツとその表示のされ方を分離します。それによって名前の衝突を無くし、コードを読みやすくします。

## 基本的な使い方

shadow DOM仕様では、shadow DOMサブツリーをエレメントに追加するのに、`createShadowRoot`というプログラム的な方法がさだめられています。
{{site.project_title}}エレメントを作るときには、エレメントのshadow DOMに含まれる部分を`<template>`タグを使って指定することができます:

    <polymer-element name="my-custom-element" noscript>
      <template>
        <span>People say: </span>
          <content select="q"></content> 
        <footer>sometimes</footer>
      </template>
    </polymer-element>

`span`, `content`, `footer`の各エレメントはこのカスタムエレメント内に閉じ込められ、ページの他の部分からは見えなくなります。

## Shadow DOMサブツリーについて

shadow DOMを使うことで一つのノードに3つのサブツリーが含まれることになります。
3つのサブツリーとは、 _light DOM_, _shadow DOM_, _composed DOM_ のことです。

light DOMとshadow DOMを合わせて _logical DOM_ と呼びます。開発者がlogical DOMを操作する一方で、ブラウザはcomposed DOMを画面上に描画します。

**Light DOM**

あなたの作ったカスタムエレメントを呼び出す際に付け足すのが light DOM です:

    <my-custom-element>
      <q>Hello World</q> <!-- part of my-custom-element's light DOM -->
    </my-custom-element>

`<my-custom-element>`の light DOM は通常のサブツリーとして、カスタムエレメントをタグとして記述するエンドユーザに見えます。エンドユーザはサブツリーについての情報を得るため、`.childNodes`, `.children`, `.innerHTML`やその他のプロパティやメソッドにアクセスできます。

**Shadow DOM**

`<my-custom-element>`にはshadow DOMが定義されているかもしれません。shadow DOMはshadow root をエレメントに追加します。

    #document-fragment
      <polymer-element name="my-custom-element" noscript>
        <template>
          <!-- Shadow DOM subtree -->
          <span>People say: </span>
            <content select="q"></content>
          <footer>sometimes</footer>
        </template>
      </polymer-element>

shadow DOMはエレメントの内側にあり、エンドユーザからは見えません。shadow DOMノードは`<my-custom-element>`の子要素ではありません。

**注意:** shadow rootはChromeのDevToolsでは`#document-fragment`と表示されます。
{: .alert .alert-info }

**Composed (表示される) DOM**

composed DOMはブラウザが実際に表示するものです。表示の際に、light DOMはshadow DOM内に配置され、composed DOMとなります。最終的な出力は次のようになります:

    <my-custom-element>
      <span>People say: <q>Hello World</q></span>
      <footer>sometimes</footer>
    </my-custom-element>

light DOMとshadow DOMのノードはそれぞれのツリー構造にあった親子・兄弟関係に収まります。ブラウザが表示するツリーには、それらの関係は存在しません。そのため、`<span>`が、表示されるツリー構造では`<my-custom-element>`の子要素であり、`<q>`の親要素であっても、実際には`<span>`はshadow rootの子要素であり、`<q>`は`<my-custome-element>`の子要素です。
この二つの要素は無関係ですが、shadow DOM はそれらが関係あるかのように表示します。ユーザは通常のDOMサブツリーのようにlight DOMやshadow DOMを操作することができます。その変化に応じて、表示されるツリーをシステムが変更します。

{% include other-resources.html %}
