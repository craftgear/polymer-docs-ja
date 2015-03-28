---
layout: default
type: guide
shortname: Docs
title: タッチとジェスチャ
subtitle: Guide
---

{% include toc.html %}

## なぜpolymer-gesturesが必要か？

今日のブラウザにおいて、マウスイベントとタッチイベントは根本的に異なった存在です。このことがクロスプラットフォームアプリケーションの作成を難しくしています。

例えば、単純なお絵かきアプリがマウス操作とタッチ操作の両方できちんと動くようにするにはたくさんやることがあります:

- マウスイベントはタッチシークエンスが終わったあとだけ発行されます
- マウスイベントはクリックイベントハンドラを持たないエレメントでは発行されません。クリックイベントハンドラはエレメントの“onclick”に、デフォルトで、あるいは直接割り当てられる必要があります。
- MouseMoveかMouseOverイベントによってページの内容が変更されている場合、クリックイベントは発行されません。
- クリックイベントはタッチシークエンスが終わったあと、300ミリ秒後に発行されます。
- より詳しい情報は: [Apple Developer Documentation](http://developer.apple.com/library/safari/#documentation/appleapplications/reference/safariwebcontent/HandlingEvents/HandlingEvents.html)を参照して下さい。

タッチイベントを実装している現在のプラットフォームでは、後方互換性のためにマウスイベントも実装されています。しかし、実装されているのはマウスイベントの一部で、動作も変更されています。

さらに、タッチイベントはエレメントがtouchstartを受け取った時のみ発行されます。これはマウスイベントとは根本的に異なる振る舞いです。マウスイベントでは、マウスカーソルの下にあるエレメントにたいしてイベントが発行されます。タッチとマウスで同様の挙動を実現するには、タッチイベントは`document.elementFromPoint`を使ってターゲットの再設定をする必要があります。

これらの相違点
These incompatibilities lead to applications having to listen to 2 sets of events, mouse on
desktop and touch on mobile.

**This forked interaction experience is cumbersome and hard to maintain.**

To reduce the overhead involved in managing both event systems, polymer-gestures provides a set of normalized events, which behave the same no matter what the source.

**Note:** Although Pointer Events provide a wonderful model to unify these different event systems, they can be difficult to polyfill in a performant fashion. The polymer-gestures library is designed to fill the gap until the web platform provides a native solution.
{: .alert }

## Basic Usage

By default, no polymer-gesture events are sent from an element. This maximizes the possibility that a browser can deliver smooth scrolling and jank-free gestures. If you want to receive events, you must set the `touch-action` property of that element. See the Web Fundamentals [guide to `touch-action`](https://developers.google.com/web/fundamentals/input/touch/touchevents/#control-gestures-using-touch-actions) for a primer. Below are a few examples:

1. Set up some elements to create events with the `touch-action` attribute.
  - `<div id="not-a-scroller" touch-action="none"></div>`
      - Generates events all the time, will not scroll
  - `<div id="horizontal-scroller" touch-action="pan-x">`
      - Generates events in the y axis, scrolls in the x axis
  - `<div id="vertical-scroller" touch-action="pan-y">`
      - Generates events in the x axis, scrolls in the y axis
  - `<div id="all-axis-scroller" touch-action="pan-x pan-y">`
      - Generates events only when tapping, scrolls otherwise
      - Can also have the value `pan-y pan-x` or `scroll`

2. Listen for the desired events
  - `down`: a pointer is activated, or a device button held.
  - `up`: a pointer is deactivated, or a device button released. Same target as `down`, provides the element under the pointer with the `relatedTarget` property
  - `tap`: a pointer has quickly gone down and up. Used to denote activation.
  - `trackstart`: denotes the beginning of a series of tracking events. Same `target` as `down`.
  - `track`: fires for all pointer movement being tracked.
  - `trackend`: denotes the end of a series of tracking events. Same `target` as `down`. Provides the element under the pointer with the `relatedTarget` property.
  - `hold`: a pointer is held down for 200ms.
  - `holdpulse`: fired every 200ms while a pointer is held down.
  - `release`: a pointer is released or moved.


1. As elements come and go, or have their `touch-action` attribute changed, they will send the proper set of polymer-gesture events.

## Examples

- [Simple Event Example](http://polymer.github.io/polymer-gestures/samples/simple/index.html)

## Library Limitations

### touch-action

According to the spec, the
[touch-action](https://dvcs.w3.org/hg/pointerevents/raw-file/tip/pointerEvents.html#the-touch-action-css-property)
css property controls whether an element will perform a "default action" such as scrolling, or receive a continuous stream of events.

Due to the difficult nature of polyfilling new CSS properties, this library will
use a touch-action *attribute* instead. In addition, run time changes involving
the `touch-action` attribute will be monitored for maximum flexibility.

Touches will not generate events unless inside of an area that has a valid `touch-action` attribute defined.
This is to maintain compositiong scrolling optimizations where possible.

**Note:** Opera 12-14, does not support changes to `touch-action` attribute, nor added or removed elements
{: .alert }
