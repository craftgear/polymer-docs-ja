---
layout: default
title: カスタムエレメントとはなにか 
type: start
shortname: Platform
subtitle: 新しいDOM要素を定義し、利用する

feature:
  spec: https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html
  code: https://github.com/Polymer/webcomponentsjs
  summary: 新しいDOM要素を定義し、利用できるようになります 

links:
- "Custom Elements - defining new elements in HTML": http://www.html5rocks.com/en/tutorials/webcomponents/customelements/
---

{% include toc.html %}

## はじめに

もしHTMLが明日再発明されたとしたら、より多くの機能を備え、できることが飛躍的に多くなっているでしょう。
例えば、HTMLに`<camera>`,`<carousel>`,`<tabs>`といったエレメントがあったら、証明写真アプリを作るのがどれほど簡単になるか、想像してみて下さい。

幸いなことに、カスタムエレメントは{{site.project_title}}の"[すべてがエレメントになる](/docs/start/everything.html#everythingis)"という考えを可能にします。この考え方を理解すると、ウェブアプリケーションははっきり定義され、再利用可能なコンポーネントの集合になります。

## カスタムエレメントとはなにか?

カスタムエレメントはカスタムタグ名を持つ新しいエレメントを定義することを可能にします。
JavaScriptとカスタムタグを組み合わせて、それらを標準のタグと同じように使います。
例えば、`super-button`という特殊なボタンを登録したあとで、次のようにしてそのボタンを利用します:

    <super-button></super-button>

他のエレメントと同様に、カスタムエレメントもJavaScript内でインスタンス化することができます。

    var s = document.createElement('super-button');
    s.innerHTML = "I'm super!";

**カスタムエレメントは本物のエレメントです**
`div`や`span`と同様に、カスタムエレメントでは、標準のDOMメソッドを使い、プロパティにアクセスし、イベントリスナを登録し、CSSでスタイルを装飾することができます。

### なぜカスタムエレメントなのか?

カスタムエレメントには次に上げるようなたくさんの利点があります:

- コードを書く量を減らします
- コードが何をするのかはっきりと示します
- 内部構造を隠蔽します
- エレメントの型ごとにAPIを実装します
- エレメントを再利用することで生産性を高めます
- 他のタグを継承した新しいエレメントタグを作ります

## {{site.project_title}}エレメントとはなにか?

{{site.project_title}}はカスタムエレメントを宣言的に作ることを可能にし、双方向データバインディングや、宣言的イベントハンドリング、宣言的継承などの特別な機能を追加します。


## カスタムエレメントの定義と登録

カスタムエレメント仕様には、`document.registerElement`を用いてプログラム的にカスタムエレメントを定義する方法がさだめられています。

    var myTag = document.registerElement('my-tag');

しかし、Polymerを使えば、`registerElement` を直接呼び出すことはありません。

`<polymer-element>`タグを使い、{{site.project_title}}エレメントを定義します。

    <polymer-element name="hello-tag">
      <template>
        <div> Hello </div>
      </template>
      <script>
        Polymer();
      </script>
    </polymer-element> 

`<template>`タグはエレメントのUIを定義します。
そして`script`タグ内の`Polymer()`メソッドでこのエレメントを登録します。

**注意: カスタムエレメントの名前にはダッシュ(-)を含める必要があります**

{{site.project_title}}を使ったカスタムエレメントの作成についてさらに詳しく学ぶには
[エレメントの宣言](https://www.polymer-project.org/docs/polymer/polymer.html#element-declaration) を参照して下さい。

##  {{site.project_title}} エレメントのインスタンスを作る

登録が終わったら、`document.createElement`を使ってプログラム的にエレメントを作るか、次のようにして宣言的にエレメントを作ることができます:

    <hello-tag></hello-tag>

もし既存のDOMエレメント(`HTMLElement`以外の)から派生した{{site.project_title}}エレメントを作るのに`extends`を使っていたら、`is` 書式を使います:

    <button is="count-button"></button>

## 既存のエレメントを拡張する

{{site.project_title}}エレメントは、定義で`extends`属性を使うことで他のエレメントを拡張できます。
親のプロパティとメソッドは子に引き継がれ、データバインドされます。
子エレメントは親エレメントのメソッドをオーバーライドすることができます。

カスタムエレメントだけでなく、`button`や`div`といった標準のDOMエレメントを拡張できます。
次にボタンを拡張する例を示します:

    <polymer-element name="count-button" extends="button"
                     on-click="increment">
      <template>
        Increment: {{counter}}
      </template>

      <script>
        Polymer({
          counter: 0,
          increment: function(e, detail, sender) { this.counter++ }
        })
      </script>
    </polymer-element>

より詳しい内容と例については、[他のエレメントを拡張する](https://www.polymer-project.org/docs/polymer/polymer.html#extending-other-elements) を参照して下さい。

## エレメントのタイプ {#elementtypes}

エレメントはその用途と振る舞いによって二つのカテゴリに分けられます。

- UIエレメントはUIをスクリーンに表示します
- 非UIエレメントはそれ以外の機能を提供します

{{site.project_title}}を使ってUIエレメントも非UIエレメントも作ることができます。

###  UIエレメント {#uielements}

`<select>`や`<core-selectgor>`といったエレメントは _UIエレメント_　です。
これらのエレメントはUIを表示するので、ページ上で確認できます。
Elements like `<select>` and `<core-selector>` are _UI elements_.
They render UI and are visible on the page.
他には [`<core-collapse>`](/components/core-docs/index.html#core-collapse),
[`<core-toolbar>`](/components/core-docs/index.html#core-toolbar),
, [`<paper-tabs>`](/components/paper-docs/index.html#paper-tabs)といった例があります:

    <paper-tabs selected="0">
      <paper-tab>One</paper-tab>
      <paper-tab>Two</paper-tab>
      <paper-tab>Three</paper-tab>
    </paper-tabs>

<!-- 
<iframe src="/components/paper-tabs/demo.html" style="border:none;height:80px;width:100%;"></iframe> -->

### 非UIエレメント {#nonuielements}

非UIエレメントはスクリーン上に _**何も表示しません**_
これは奇妙に感じられるかもしれませんが、すでにたくさんの例がHTMLにあります:
2,3例を上げると`<script>`, `<style>`, `<meta>` などがそうです。
これらのエレメントはUIを表示することなくその役割を果たしています。

非UIエレメントは縁の下の力持ちです。
例えば、`<core-ajax>`タグはマークアップからXHRリクエストを実行可能にします。
次のように、属性にいくつかの設定をし、結果を受け取ります:

    <core-ajax url="http://gdata.youtube.com/feeds/api/videos/" auto
               params='{"alt":"json", "q":"chrome"}' handleAs="json"></core-ajax>
    <script>
      var ajax = document.querySelector('core-ajax');
      ajax.addEventListener('core-response', function(e) {
        console.log(this.response);
      });
    </script>

このように非UIエレメントはお決まりのコードを書く量を減らしてくれます。`XMLHttpRequest`のような、複雑なAPIの呼び出しを隠し、見えないところで仕事をこなしてくれるのです。

## リソース

- [Custom Elements](http://www.html5rocks.com/en/tutorials/webcomponents/customelements/)
  &mdash; HTML5 RocksにあるEric Bidelmanによるカスタムエレメントの解説記事
- [Webcomponents.org](http://webcomponents.org/)
  &mdash; カスタムエレメントを含むWeb Componentsについて議論するための専用ウェブサイト
- [W3C Specification](http://w3c.github.io/webcomponents/spec/custom/) &mdash;Web Components 仕様
- [Custom Elements](http://customelements.io/) &mdash; Web Componentsのギャラリー
- [GitHub repo](https://github.com/Polymer/webcomponentsjs) &mdash; カスタムエレメントのリポジトリ

## 次のステップ {#nextsteps}

Polymerライブラリのもうひとつの構成要素であるshadow DOMについて、あるいはPolymerコアAPIについて、より詳しく学びましょう

<a href="/platform/shadow-dom.html">
   <paper-button raised><core-icon icon="arrow-forward"></core-icon>Shadow DOM</paper-button>
<a href="/docs/polymer/polymer.html">
   <paper-button raised><core-icon icon="arrow-forward"></core-icon>APIデベロッパーガイド</paper-button>
