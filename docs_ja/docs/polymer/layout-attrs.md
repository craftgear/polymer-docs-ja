---
layout: default
type: guide
shortname: Docs
title: Layout attributes
subtitle: Guide
---

<style>
.demo {
  background-color: #ccc;
  padding: 4px;
  margin: 12px;
}

.demo div {
  background-color: white;
  padding: 12px;
  margin: 4px;
}

.tall {
  height: 124px;
}

.demo[vertical] {
  height: 250px;
}

demo-tabs::shadow #results {
  width: 40%;
  max-width: initial;
}
</style>

{% include experimental.html %}

{% include toc.html %}

{{site.project_title}}には[CSS Flexbox](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Flexible_boxes)をベースにして作られた宣言的レイアウト機能が備わっています。CSS Flexboxの機能はエレメントの**属性**として利用可能です。

レイアウト属性は、[layout.html](https://github.com/Polymer/polymer/blob/master/layout.html)
にて実装されています。このファイルは{{site.project_title}}をインポートすると自動でロードされます。レイアウト属性はShadowDOMの[`/deep/` rules](/articles/styling-elements.html#cat)を使うため、ShadowDOMの境界に関係なく効果を発揮します。{{site.project_title}}を読み込む限り、自作エレメントのどの部分でも自由にこの属性を利用できます。

## 水平レイアウトと垂直レイアウト

他の要素を内側に持つ要素に`layout`属性が指定されていると、その要素はフレックスコンテナになります。`horizontal`か`vertical`を指定することで、要素の並ぶ方向を指定できます:

    <div horizontal layout>
      <div>One</div>
      <div>Two</div>
      <div>Three</div>
    </div>

<div horizontal layout class="demo">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
</div>

### フレキシブルな子要素

`layout`属性を持つ要素の子要素では、`flex`属性を用いてサイズをコントロールできます。例えば:

    <div horizontal layout>
      <div>Alpha</div>
      <div flex>Beta (flex)</div>
      <div>Gamma</div>
    </div>

<div horizontal layout class="demo">
  <div>Alpha</div>
  <div flex>Beta (flex)</div>
  <div>Gamma</div>
</div>

`vertical`エレメントにも同様のことが出来ます:

    <div vertical layout style="height:250px">
      <div>Alpha</div>
      <div flex>Beta (flex)</div>
      <div>Gamma</div>
    </div>

<div vertical layout class="demo">
  <div>Alpha</div>
  <div flex>Beta (flex)</div>
  <div>Gamma</div>
</div>

**注意**: 垂直レイアウトでは、子要素のフレックス属性を正しく動かすために、コンテナには高さを指定する必要があります。

子要素では、`flex`属性と合わせて"flex ratio"を指定することで、幅や高さを指定することが出来ます。flex ratio は1から12までの数値をあわらす文字列で指定されます:

例えば、"Beta"に対して、"Gamma"を倍の幅に、"Alpha"を三倍の幅にしたいなら、それぞれに`flex two`と`flex three`を指定します:

    <div horizontal layout>
      <div flex three>Alpha</div>
      <div flex>Beta</div>
      <div flex two>Gamma</div>
    </div>

<div horizontal layout class="demo">
  <div flex three>Alpha</div>
  <div flex>Beta</div>
  <div flex two>Gamma</div>
</div>

### Auto-vertical 属性

垂直レイアウトでは、`auto-vertical`属性を子要素に指定することで、その要素の配置方法をフレックスレイアウトに応じて自動で切り替えるようにすることが出来ます。
ディスプレイが広い場合は要素を水平に配置し、狭い場合は垂直に配置したいなら、この属性を使って下さい。

次に示すコードは、`core-media-query`を使ってスクリーンサイズを取得し、640ピクセルよりも狭い場合にレイアウトをフレックスにし、要素を垂直に配置します。それ以外の場合はレイアウトは水平になり、エレメントは横方向に配置されます。

{% raw %}
    <core-media-query query="max-width: 640px"
                      queryMatches="{{phoneScreen}}"></core-media-query>
    <div layout vertical?="{{phoneScreen}}"
         horizontal?="{{!phoneScreen}}">
      <div auto-vertical>Alpha</div>
      <div auto-vertical>Beta</div>
      <div auto-vertical>Gamma</div>
    </div>
{% endraw %}

<div vertical layout class="demo" style="height:170px">
  <div auto-vertical>Alpha</div>
  <div auto-vertical>Beta</div>
  <div auto-vertical>Gamma</div>
</div>

### 交差軸方向への配置

デフォルトでは、子要素は交差軸方向へ引き伸ばされて配置されます。(例: _水平_レイアウトでは、_垂直_方向へ引き伸ばされます)。

    <div horizontal layout>
      <div>Stretch Fill</div>
    </div>

<div horizontal layout class="demo tall">
  <div>Stretch Fill</div>
</div>

交差軸の中心に配置したい場合(例: _水平_レイアウトで、_垂直_センタリング)、`center`を追加して下さい。

    <div horizontal layout center>
      <div>Center</div>
    </div>

<div horizontal layout center class="demo tall">
  <div>Center</div>
</div>

同様に、`start`と`end`をつかうことで上付き・下付き(`vertical`レイアウトでは左寄せ・右寄せ)にも配置可能です。

    <div horizontal layout start>
      <div>start</div>
    </div>

<div horizontal layout start class="demo tall">
  <div>start</div>
</div>

    <div horizontal layout end>
      <div>end</div>
    </div>

<div horizontal layout end class="demo tall">
  <div>end</div>
</div>

## 行揃え

コンテンツの位置を親要素に沿って揃えるには、`*-justified`属性を使います。

**例** - 左揃え

    <div horizontal start-justified layout>
      <div>start-justified</div>
    </div>

<div horizontal start-justified layout class="demo">
  <div>start-justified</div>
</div>

**例** - 中央揃え

    <div horizontal center-justified layout>
      <div>center-justified</div>
    </div>

<div horizontal center-justified layout class="demo">
  <div>center-justified</div>
</div>

**例** - 右揃え

    <div horizontal end-justified layout>
      <div>end-justified</div>
    </div>

<div horizontal end-justified layout class="demo">
  <div>end-justified</div>
</div>

**例** - 等間隔配置

    <div horizontal justified layout>
      <div>justified</div>
      <div>justified</div>
      <div>justified</div>
    </div>

<div horizontal justified layout class="demo">
  <div>justified</div>
  <div>justified</div>
  <div>justified</div>
</div>

**例** - 各要素ごとに等間隔の余白をつける

    <div horizontal around-justified layout>
      <div>around-justified</div>
      <div>around-justified</div>
    </div>

<div horizontal around-justified layout class="demo">
  <div>around-justified</div>
  <div>around-justified</div>
</div>

## 子要素に配置を指定する

配置は小要素ごとに指定することも可能です(レイアウトコンテナに指定する代わりに):

    <div horizontal layout>
      <div flex self-start>Alpha</div>
      <div flex self-center>Beta</div>
      <div flex self-end>Gamma</div>
      <div flex self-stretch>Delta</div>
    </div>

<div horizontal layout class="demo tall">
  <div flex self-start>Alpha</div>
  <div flex self-center>Beta</div>
  <div flex self-end>Gamma</div>
  <div flex self-stretch>Delta</div>
</div>

## 折り返し

`wrap`属性を使うと子要素を自動で折り返し出来ます。

    <div horizontal layout wrap style="width: 220px">
      <div>Alpha</div>
      <div>Beta</div>
      <div>Gamma</div>
      <div>Delta</div>
    </div>

<div horizontal layout wrap style="width: 220px" class="demo">
  <div>Alpha</div>
  <div>Beta</div>
  <div>Gamma</div>
  <div>Delta</div>
</div>

配置方向を逆にするには`reverse`属性が使えます。

    <div horizontal layout reverse>
      <div>Alpha</div>
      <div>Beta</div>
      <div>Gamma</div>
      <div>Delta</div>
    </div>

<div horizontal layout reverse class="demo">
  <div>Alpha</div>
  <div>Beta</div>
  <div>Gamma</div>
  <div>Delta</div>
</div>

## body要素のFull bleed指定

`<body>`がビューポート全体を占めるようにしたいというのはよくある要求です。デフォルト状態では、{{site.project_title}}のレイアウト機能はこの`<body>`をビューポート全体に表示するようにはなっていません。
`<body>`がビューポート全体を占めるようにするには、`fullbleed`属性を使います:

    <body fullbleed vertical layout>
      <div flex>Fitting a fullbleed body.</div>
    </body>

<iframe src="/samples/layout-attr.html" style="width: 100%; height: 150px;border:1px solid black"></iframe>

この指定をするとbody要素のマージンはなくなり、高さと幅が最大化されます。

## 様々な属性

{{site.project_title}}には、要素の配置に使える様々な属性があります:

Attribute | Result
|-
`block`|  `display: block`を指定します
`hidden` |  `display: none`を指定します
`relative` |  `position: relative`を指定します
`fit` |  `position: absolute`と `top:0;right:0;bottom:0;left:0;` (別名 "trbl fitting")を指定します。 <br><br>**注意:** `fit`属性を使う場合は、その要素を含むコンテナが指定されたサイズを持ち、`position: relative`レイアウトでなければなりません。これは子要素がどこに配置されるかを知る必要があるためです。

**例**

    <div>Before <span>[A Span]</span> After</div>

    <div>Before <span block>A Block Span</span> After</div>
    <div>Hidden: <span hidden>Not displayed</span></div>
    <div relative style="height: 100px;">
      <div fit style="background-color: #000;color: white">Fit</div>
    </div>

<div class="demo">Before <span>[A Span]</span> After</div>
<div class="demo">Before <span block>[A Block Span]</span> After</div>
<div class="demo">Hidden: <span hidden>Not displayed</span></div>
<div relative style="height: 100px;" class="demo">
  <div fit style="background-color: #000;color: white">Fit</div>
</div>
