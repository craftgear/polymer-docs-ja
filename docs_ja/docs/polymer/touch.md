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

これらの相違点のために、アプリケーションでは、デスクトップのマウスと、モバイルのタッチスクリーンという異なった二つのイベントセットに対応しなければなりません。

**操作体系がこのように二つに分かれていることは厄介で、メンテナンスが大変です。**

両方のイベントシステムを扱うオーバーヘッドを軽減するため、polymer-gesturesではイベントの発生元にかかわらず、同じ振る舞いをする標準化したイベントセットを提供します。

**注意:** Pointerイベント（訳者注：マイクロソフトの技術仕様を指すと思われる）はこの2つの異なったイベントシステムを統合する素晴らしい方法ですが、パフォーマンスを損なわずにpolyfillを行うことが難しい可能性があります。polymer-gesturesライブラリはWeb標準が根本的な解決法を提供するまでのギャップを埋める役割を果たします。{: .alert }

## 基本的な使い方

デフォルトでは、polymer-gesturesイベントは無効になっています。これによってブラウザは最大限スクロールをスムーズにし、おかしな挙動を避けることが出来ます。polymer-gesturesイベントを受け取りたい場合は、エレメントの`touch-action`プロパティをセットする必要があります。詳しくは[guide to `touch-action`](https://developers.google.com/web/fundamentals/input/touch/touchevents/#control-gestures-using-touch-actions)を参照して下さい。いかにいくつか例を示します:

1. `touch-action`属性を使ってイベントを発行するエレメントを作ります
  - `<div id="not-a-scroller" touch-action="none"></div>`
      - 常にイベントを発行し、スクロールをしません。
  - `<div id="horizontal-scroller" touch-action="pan-x">`
      - y軸方向のイベントを発行し、x軸方向にはスクロールをします。
  - `<div id="vertical-scroller" touch-action="pan-y">`
      - x軸方向のイベントを発行し、y軸方向にはスクロールします。
  - `<div id="all-axis-scroller" touch-action="pan-x pan-y">`
      - タップされた時のみイベントを発行し、それ以外はスクロールします。
      -  `pan-y pan-x` あるいは `scroll`を指定することも出来ます。

2. 希望するイベントをlistenする
  - `down`: 指が触れたか、マウスのボタンが押された
  - `up`: 指が離れたか、マウスのボタンが離れた。`down`と同じターゲットで、指の下にあるエレメントに`relatedTarget`プロパティをあたえます。
  - `tap`: 指が素早く触れて離れた。有効化を意味するのに使われます。
  - `trackstart`: トラックイベントの開始を意味します。`down`と同じ`target`になります。
  - `track`: 全ての指の動きがトラックされている時に起こります
  - `trackend`: トラックイベントの終了を意味します。`down`と同じ`target`になります。指の下にあるエレメントに`relatedTarget`プロパティをあたえます。
  - `hold`: 指が200ミリ秒押されていることを意味します。
  - `holdpulse`: 指が押されているあいだじゅうずっと200ミリ秒ごとに発行されます。
  - `release`: 指が離れたか移動した時に発行されます。


1. エレメントが行ったり来たりするたびに、あるいは`touch-action`属性が変更されるたびに、対応するpolymer-gesturesイベントが発行されます。

## 例

- [Simple Event Example](http://polymer.github.io/polymer-gestures/samples/simple/index.html)

## polymer-gesturesライブラリの制限

### touch-action


[touch-action](https://dvcs.w3.org/hg/pointerevents/raw-file/tip/pointerEvents.html#the-touch-action-css-property)の仕様によると、CSSのプロパティが、エレメントがスクロールのような"デフォルトの振る舞い"をするか、あるいは一連のイベントを受け取るかを決定する、とあります。

新しいCSSプロパティを補完するという難しい性質を備えているために、このライブラリではtouch-action*属性*を代わりに用いています。さらに、実行時の`touch-action`属性の変更も可能な限り監視されます。

タッチ操作は、適切な`touch-action`属性が定義されたエリアの外ではイベントを発生させません。これによって、スクロールに最適化された画面合成を可能な限り維持します。

**注意:** Opera 12-14では`touch-action`属性の変更、追加、削除はサポートされません。
{: .alert }
