---
layout: default
type: start
shortname: Concepts
title: Web components polyfills
subtitle: あたらしいWeb技術を今サポートする
---

{% include toc.html %}

## はじめに

{{site.project_title}} はいずれ使えるようになるWebComponentsテクノロジの上に構築されています。
WebComponentsはまだすべてのブラウザで使えるようにはなっていません。

しかし、`webcomponents.js`というWebComponentsサポートライブラリを使うことで、どの最新のブラウザでも{{site.project_title}}を動かすことができます。
`webcomponents.js`はライブラリの集合（"polyfills"と呼ばれます）であり、すべてのブラウザでまだ使えるようになっていない新しい技術をサポートします。WebComponents polyfillsは開発者がすべてのモダンブラウザでこの新しい技術を使えるようにします。
将来、この技術がブラウザに実装されるにつれて、polyfillsの役割は少なくなっていき、ブラウザのネイティブ実装を使うようになっていくでしょう。

`webcomponents.js` は自動的にブラウザのサポート状況を検出し、ネイティブ実装が利用できるときはそちらを利用します。その場合カスタムエレメントはネイティブ実装によってより高速に実行されます。

従来WebComponents polyfills はPolymer開発陣によって管理され、`platform.js`としてリリースされていました。polyfillsは[WebComponents.org](http://webcomponents.org) に移管され、`WebComponents.js`と改名されました。後方互換性のために`platform.js`は今後何回かのリリースの間は`WebComponents.js`のコピーとして維持されます。

**注意**: WebComponents polyfillsは、Chrome 36+のようなWebComponents APIを完全に実装したブラウザではもはや必要ありません。この場合、Polymerを使うのに必要なJavaScriptファイルのデータ量は圧縮状態で32kbまで少なくなります。
{: .alert alert-info}

## WebComponents polyfills の中身はなにか？ {#bundle}

`webcomponents.min.js` は以下のライブラリを含んでいます:

- Web Components関連のライブラリ:
  - [Shadow DOM](/platform/shadow-dom.html). DOMサブツリーと関連するCSSをカプセル化します。
  - [HTML Imports](/platform/html-imports.html). エレメントの定義とそのリソースを宣言的に読み込みます。
  - [Custom Elements](/platform/custom-elements.html) . HTMLで使える新しいエレメントを定義します。
- Web Components polyfillsで必要な依存ライブラリ:
  - [WeakMap](https://github.com/Polymer/WeakMap). ES6 Weakmap type用の互換性ライブラリ。
  - [Mutation Observers](https://github.com/Polymer/MutationObservers). DOMの変更を効率的に難視する。

## インストールと利用 {#setup}

{{site.project_title}}をbowerを使ってインストールするか、ZIPファイルから展開した場合は、`webcomponents.js`がすでに含まれています。

bowerを使って`webcomponents.js`を手動でインストールすこともできます:

    bower install --save webcomponentsjs

**注意:** bowerのより詳しい使い方については
[Getting the code](/docs/start/getting-the-code.html)を参照して下さい。
{: .alert .alert-info } 

次に、`webcomponents.js`を他のスクリプトファイルと同じように読み込みます:

    <script src="bower_components/webcomponentsjs/webcomponents.min.js"></script>

**注意**: polyfillsの性質上、他のライブラリとの互換性を最大化するために、`webcomponents.js`を`<head>`で最初に読み込むようにして下さい。
{: .alert alert-info}

一旦 `webcomponents.js` が読み込まれると、[HTML Imports](/platform/html-imports.html),
[Custom Elements](/platform/custom-elements.html), [Shadow DOM](/platform/shadow-dom.html)を始め、最新の標準技術が使えるようになります。例えば、{{site.project_title}}エレメントを使うには、次のようにHTML Importを使います:

    <link rel="import"
          href="bower_components/paper-tabs/paper-tabs.html">

これで`<paper-tabs>`を他のHTMLタグと同じように使えます。

## より詳しく

WebComponents polyfills についてより詳しく知りたい、ライブラリに貢献したい場合は[WebComponents.org](http://webcomponents.org)を参照して下さい。

## 次のステップ {#nextsteps}

`webcomponents.min.js`は、複数のブラウザでWebComponentsを使うための素晴らしい土台です。
独自のエレメントを作る準備ができていて、{{site.project_title}}が提供する追加機能についてより詳しく知りたい場合は、[Polymer in 10 minutes](/docs/start/creatingelements.html) に進んで下さい。
最初の{{site.project_title}}アプリケーションを作るには[the tutorial](/docs/start/tutorial/intro.html)へ進んで下さい。

あるいは:

<a href="/docs/polymer/polymer.html">
  <paper-button raised><core-icon icon="arrow-forward" ></core-icon>APIデベロッパーガイド</paper-button>
</a>

に進んで下さい。
