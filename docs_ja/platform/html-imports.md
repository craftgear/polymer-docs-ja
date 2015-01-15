---
layout: default
title: HTML importsについて
type: start
shortname: Platform
subtitle: HTMLドキュメントを他のHTMLドキュメントで読み込む

feature:
  spec: https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/imports/index.html
  code: https://github.com/Polymer/webcomponentsjs
  summary: HTML Imports are a way to include and reuse HTML documents in other HTML documents.

links:
- "HTML5Rocks - HTML Imports: #include for the web": http://www.html5rocks.com/tutorials/webcomponents/imports/
---

{% include toc.html %}

## なぜHTML Importsが必要なのか?

HTML ImportsはHTMLドキュメントを他のHTMLドキュメントに含めて再利用できるようにします。
ちょうど`<script>`タグが外部JavaScriptファイルをページ内に読み込むのと同じです。
特に外部URLからカスタムエレメントを読み込むことができます。

## 基本的な使い方

HTML Importsを使うには、標準の`<link>`タグに`import`を指定します。例えば次のようになります:

    <link rel="import" href="my-custom-element.html">

{% include other-resources.html %}
