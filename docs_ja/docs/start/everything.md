---
layout: default
type: start
shortname: Start
title: Polymerを理解する
subtitle: そのコンセプトと階層構造
---

{% include toc.html %}

{{site.project_title}} はあなたが使ったことのあるどのライブラリやフレームワークとも異なっています。Polymerを完全に理解するには、{{site.project_title}}の世界観を理解する必要があります。Polymerの世界を形作る3つの概念的な階層をたどる小旅行に参加して下さい。

## すべてがエレメントになる: {{site.project_title}} の世界観 {#everythingis}

"_すべてがエレメントになる_"という原理が{{site.project_title}}を理解する鍵になります。

Webの誕生からこの方、ブラウザはデフォルトのエレメントを備えて世に送り出されてきました。ほとんどのエレメントは、例えば`<div>`のように、大したことはしませんが、いくつかのエレメントはかなり多機能です。おなじみの`<select>`を思い出して下さい。このエレメントはあって当たり前のように思われますが、実際はかなりすごいことをやっているのです:

`<select>`エレメントは、

- **機能的です**. ブラウザは`<select>`エレメントをどう扱えばよいかすでにわかっています。ブラウザがHTML内に`<select>`エレメントを見つけると、ユーザが操作可能なコントロールを作成します。
- **再利用可能です**. `<select>`エレメントは再利用可能な機能の集合です。いちから`<select>`エレメントと同じものを作る必要はありません。 
- **相互運用が可能です**. すべてのJavaScriptライブラリはDOMエレメントの操作方法を知っています。
- **カプセル化されています**. 内部構造は隠されて見えません。そのため、この要素がページの他の部分を動かなくすることはありません。
- **設定可能です**. スクリプトを書くことなく、HTML属性を使って振る舞いを変更することができます。
- **プログラムで操作可能です**. DOMからこのエレメントを取得すると、HTMLでは意味のないメソッドやプロパティと言ったものも備えています。
- **イベントを発生させます**. なにか起こったことを知らせるイベントを発行します。
- **組み合わせ可能です**. 他のエレメント内部に`<select>`を入れるだけでなく、`<select>`内部に入れるものによって振る舞いを変更できます。

エレメントはかなり素晴らしいものです。エレメントはWebを構成する積み木です。残念なことに、Webアプリケーションが複雑になるにつれて、我々は皆ブラウザに備え付けの基本的なエレメントだけでは追いつかなくなってきました。その結果、たくさんのスクリプトでマークアップを置き換えることでしのいできました。この過程で、我々はエレメントの持つ優雅さを失ってしまったのです。

{{site.project_title}} は基本に立ち返ります。我々はたくさんのスクリプトではなく、より強力なエレメントを作ることが正しい方法だと考えます。Web Componentsという新しい技術セットがこれを可能にしました。

ここで思い出されるのが{{site.project_title}}の世界観である _すべてがエレメントになる_ です。

"エレメント"という言葉が意味するのは、デフォルトのエレメントの素晴らしいプロパティをすべて備えた本当のエレメントです。それに、考えてみて下さい、エレメントをユーザインターフェイスだけに限定する理由はありますか？エレメントのプロパティのうちいくつかはUIに特化したものですが、多くはそうではありません。エレメントは再利用可能な機能の普遍的なパッケージとして存在しうるのです。

この視点で見ると、世界は違って見えます。基本的なエレメントを組み合わせて、その複雑さを見せることなく、より大きく強力なエレメントを作れます。さらに自分で作ったエレメントを組み合わせてより大きくより強力なエレメントを作ることもできるのです。それとは気づかずに完全にカプセル化された再利用可能な _アプリケーション_ が完成していることでしょう。

今までの世界では、スクリプトが建物を立てる際のコンクリートの役目を果たしていました。たくさんのコンクリートを使うことが問題解決の方法でした。新しい世界ではエレメントがレンガの役割を果たします。スクリプトはモルタルのようなものです。必要な形に最も近いレンガを選び、少しのモルタルでそれらをつなぎ合わせす。それが、我々の言う、_すべてがエレメントになる_ の意味することなのです。


## {{site.project_title}}の階層構造

{{site.project_title}}には3つの概念的な階層構造があります:

1. **[Web components](/docs/start/platform.html)**: {{site.project_title}} is built on 
the Web Components standards. Not all browsers support these features yet, so 
the [web components polyfill layer](/docs/start/platform.html) fills the gaps, 
implementing the APIs in JavaScript. At runtime, {{site.project_title}} automatically 
picks the fastest path -- native implementation or JavaScript.

1. **[The {{site.project_title}} library](/docs/start/creatingelements.html)**: The {{site.project_title}}
library provides a declarative syntax that makes it simpler to define custom elements.  
And it adds features like two-way data binding, property observation, and gesture 
support help you build powerful, reusable elements.

1. **[Elements](/docs/start/usingelements.html)**: The Core and Paper element sets 
form a comprehensive set of UI and non-UI elements that you can use right out of the box. 
These elements depend on the {{site.project_title}} library, but are separate and optional.
You can use the elements without using {{site.project_title}} directly, or you can use
{{site.project_title}} to create your own elements and not use the Core or Paper elements at all.
You can mix and match the Core and Paper elements with other elements, including 
built-in elements and other custom elements.

## Next steps {#nextsteps}

Now that you've got the basic concepts of {{site.project_title}}, it's time to
start digging in a bit more. Continue on to:

<a href="/platform/custom-elements.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>About custom elements</paper-button>
</a>

Or jump straight to:

<a href="/docs/polymer/polymer.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>API developer guide</paper-button>
</a>
