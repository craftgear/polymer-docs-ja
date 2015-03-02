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

For example, to make "Gamma" 2x larger than "Beta" and "Alpha" 3x larger, use `flex two` and `flex three`, respectively:

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

### Auto-vertical

For vertical layouts, you can use the `auto-vertical` attribute
on a child element to set an automatic flex basis on that element.
Use this attribute for responsive designs
if you want elements laid out horizontally when the display is wide
or vertically when narrow.

The following code uses `core-media-query` to get the screen size.
If it's smaller than 640 pixels,
the layout becomes vertical and the elements layout on a flex basis.
Otherwise, the layout becomes horizontal and the elements are laid out
normally.

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

### Cross-axis alignment

By default, children stretch to fit the cross-axis (e.g. _vertical_ stretching in a _horizontal_ layout).

    <div horizontal layout>
      <div>Stretch Fill</div>
    </div>

<div horizontal layout class="demo tall">
  <div>Stretch Fill</div>
</div>

Center _across_ the main axis (e.g. _vertical_ centering elements in a _horizontal_ layout)
by adding `center`.

    <div horizontal layout center>
      <div>Center</div>
    </div>

<div horizontal layout center class="demo tall">
  <div>Center</div>
</div>

You can also position at the top/bottom (or left/right in `vertical` layouts) using `start` or `end`.

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

## Justification

Justifying content is done with the `*-justified` attributes.

**Example** - start justified

    <div horizontal start-justified layout>
      <div>start-justified</div>
    </div>

<div horizontal start-justified layout class="demo">
  <div>start-justified</div>
</div>

**Example** - center justified

    <div horizontal center-justified layout>
      <div>center-justified</div>
    </div>

<div horizontal center-justified layout class="demo">
  <div>center-justified</div>
</div>

**Example** - end justified

    <div horizontal end-justified layout>
      <div>end-justified</div>
    </div>

<div horizontal end-justified layout class="demo">
  <div>end-justified</div>
</div>

**Example** - equal space between each element

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

**Example** - equal space around each element

    <div horizontal around-justified layout>
      <div>around-justified</div>
      <div>around-justified</div>
    </div>

<div horizontal around-justified layout class="demo">
  <div>around-justified</div>
  <div>around-justified</div>
</div>

## Self alignment

Alignment can also be set per-child (instead of using the layout containers rules):

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

## Wrapping

Wrapped layouts can be enabled with the `wrap` attribute.

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

Layout direction can be mirrored with the `reverse` attribute.

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

## Full bleed &lt;body>

It's common to want the entire `<body>` to fit to the viewport. By themselves, {{site.project_title}}'s layout features on `<body>` don't achieve the result. You can make `<body>` take
up the entire viewport by adding the `fullbleed` attribute:

    <body fullbleed vertical layout>
      <div flex>Fitting a fullbleed body.</div>
    </body>

<iframe src="/samples/layout-attr.html" style="width: 100%; height: 150px;border:1px solid black"></iframe>

This removes its margins and maximizes its height to the viewport.

## General purpose attributes

{{site.project_title}} also includes other general purpose attributes for basic positioning:

Attribute | Result
|-
`block`| Assigns `display: block`
`hidden` | Assigns `display: none`
`relative` | Assigns `position: relative`
`fit` | Sets `position: absolute` and sets `top:0;right:0;bottom:0;left:0;` (aka "trbl fitting"). <br><br>**Note:** When using the `fit` attribute, there must be a container with fixed size and has `position: relative` layout. This is so the children know where to fit to.

**Examples**

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
