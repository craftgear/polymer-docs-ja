---
layout: default
title: web animationについて
type: start
shortname: Platform
subtitle: polyfill

feature:
  spec: http://dev.w3.org/fxtf/web-animations/
  code: https://github.com/web-animations/web-animations-js
  summary: "Web Animations defines APIs for synchronizing several of the web's animation models with complex, scriptable animations."

---

<!-- TODO(ericbidelman): remove when Toolkit builds in Web Animations. -->
<!-- <script src="/toolkit/platform/web-animations-js/web-animations.js"></script> -->

{% include spec-header.html %}

{% include toc.html %}

## なぜWeb Animationsが必要なのか?

Web標準にはすでに4つのアニメーション関連の仕様があります。
[CSS Transitions](http://dev.w3.org/csswg/css-transitions/),
[CSS Animations](http://dev.w3.org/csswg/css-animations/), 
[SVG Animations](http://www.w3.org/TR/SVG/animate.html) / [SMIL](http://www.w3.org/TR/2001/REC-smil-animation-20010904/)
そして `requestAnimationFrame()`です。しかしながら:

- *CSS Transitions / CSS Animations は視覚的にわかりやすいとは言えません* - アニメーションを合成したり、つなげたり、ちゃんと同時に再生したりすることができません。さらにスクリプトからアニメーションを制御することもできません。
- *SVG Animations は視覚的に大変わかりやすいですが、同時に大変複雑でもあります。*. SVG Animations
はHTMLに使うことはできません。
- *`requestAnimationFrame()` は宣言的な方法ではありません。* - メインスレッドで処理を走らせる必要があるため、メインスレッドが忙しくなるとアニメーションがとたんに重くなります。

Web Animations はWebコンテンツをアニメーションさせるための新しい仕様で、CSSおよびSVGワーキンググループの一部としてW3C仕様の策定がおこわ慣れています。
Web Animationsの目的は先ほど挙げた4つの仕様に存在する欠点を解決することです。それと同時に、CSS Transition/CSS Animations/SVG Animations の実装を置き換え、次のことを可能にします:

- Webでアニメーションをサポートするために書くコードが減ります。
- 様々なアニメーション仕様が相互運用可能になります。
- 仕様と作る人達とブラウザを開発する人達が、未来のWebのためにアニメーションを良くする発明をする共通の場所を持つことになります。

## 基本的な使い方

次に`<div>`の不透明度と大きさを0.5秒かけて変更するアニメーションの例を示します。
このアニメーションはパルス効果の代わりになります。

    <div class="pulse" style="width:150px;">Hello world!</div>
    <script>
      var elem = document.querySelector('.pulse');
      var player = elem.animate([
          {opacity: "0.5", transform: "scale(0.5)"},
          {opacity: "1.0", transform: "scale(1)"}
        ],
        {
          direction: "alternate", duration: 500, iterations: Infinity
        });
    </script>

## アニメーションモデル

Web Animations モデルはウェブのアニメーションコンテンツのためのエンジンについての詳細です。
このエンジンはCSS Transitions/CSS Animations/SVG Animationsをサポートするのに十分強力です。
>The Web Animations model is a description of an engine for animation content on the web. The engine is sufficiently powerful to support CSS Transitions, CSS Animations and SVG Animations.

Web Animations はまたモデルに対してJavaScriptAPIを提供します。このAPIは数多くのインターフェイスをJavaScriptで利用可能にします。次にこのAPIのうち、いくつか重要なものをご紹介します: それらはAnimations, AnimationEffects, TimingDictionaries, TimingGroups, AnimationPlayersです。

`Animation`オブジェクトは一つの対象エレメントに対する一つのアニメーション効果を定義します。例えば:

    var animation = new Animation(targetElement,
        [{left: '0px'}, {left: '100px'}], 2000);

この例では、対象エレメントの"left"プロパティが滑らかに`0px`から`100px`まで2秒かけて変化します。

## アニメーション効果を指定する

`AnimationEffect`オブジェクトはアニメーションによって変更されるCSSプロパティやSVG属性と、それらの変化する値をコントロールします。AnimationEffectオブジェクトはまた効果が今までの値に追加されるのか置き換えられるのかもコントロールします。

3つの主要な効果があります: `KeyframeEffect`, `MotionPathEffect`, `EffectCallback`

### キーフレーム間のアニメーション

`KeyframeEffect`は一つ以上のプロパティまたは属性を、指定されたキーフレーム間で直線的に補間します。
通常KeyFrameEffectsは辞書型のキーフレームのオフセットとプロパティの値のペアとして定義されます:

    [
      {offset: 0.2, left: "35px"},
      {offset: 0.6, left: "50px"},
      {offset: 0.9, left: "70px"},
    ]

オフセットが指定されない場合、キーフレームは0から1の間に等しく配置されます。

    [{left: "35px"}, {left: "50px"}, {left: "70px"}]


キーフレームの配置についてとKeyframeEffectsがキーフレームのから外れた時にどう評価されるかの詳細は、[仕様](http://www.w3.org/TR/web-animations/#keyframe-animation-effects) を参照して下さい。

### パスに沿ったアニメーション

`MotionPathEffect`はエレメントがSVG形式のパスに沿ってアニメーションするようにします。例えば:

    <svg xmlns="http://www.w3.org/2000/svg" version="1.1">
      <defs>
        <path id=path d="M 100,100 a 75,75 0 1,0 150,0 a 75,75 0 1,0 -150,0"/>
      </defs>
    </svg>
    <script>
      var animFunc = new MotionPathEffect(document.querySelector('#path').pathSegList);
      var animation = new Animation(targetElement, animFunc, 2000);
    </script>

### 独自のアニメーション効果

`EffectCallback`は直接プロパティを変更するのではなく、JavaScriptへの呼び出しを行うことを可能にします。この機能についてより詳しくは[仕様](http://www.w3.org/TR/web-animations/#custom-effects)を参照して下さい。 

## アニメーションをつなげたり同期したりする

二つのタイミンググループ(`AnimationGroup`と`AnimationSequence`)はアニメーションを同期させたりつなげたりすることを可能にします。

アニメショーンを同時に実行するには次のようにします:

    var animationGroup = new AnimationGroup([new Animation(...), new Animation(...)]);

アニメーションを続けて実行するには次のようにします:

    var animationSequence = new AnimationSequence([new Animation(...), new Animation(...)]);

`Animation`, `AnimationGroup`, `AnimationSequence`はすべてTimedItemsなので、これらは入れ子にできます:

    var animationGroup = new AnimationGroup([
      new AnimationSequence([
        new Animation(...),
        new Animation(...),
      ]),
      new Animation(...)
    ]);

グループはまたはオプションとしてTimingDictionaryパラメタをとります（次の例を見て下さい)。中でも繰り返しとタイミングをグループレベルで適用可能にします。
Groups also take an optional TimingDictionary parameter (see below), which among other things allows iteration and timing functions to apply at the group level:

    var animationGroup = new AnimationGroup([new Animation(...), new Animation(...)], {iterations: 4});

## アニメーションのタイミングを制御する

TimingDictionariesはアニメーション内部のタイミングをコントロールするのに使われます。(playersはアニメーションの進行度合いをドキュメントタイムの相対時間でコントロールします)。TimingDictionariesは変更可能ないくつかのプロパティを持ちます:

- **duration**: アニメーションが1階じっこうされるのにかかる持続時間です。
- **iterations**: アニメーションの再生回数です(分数を指定可能)。
- **iterationStart**: 最初のアニメーションが開始される場所のオフセットです。
- **fill**: アニメーションが最初の実行前と、最後の実行後に効果を持つかどうかを指定します。
- **delay**: アニメーション開始とアニメーションの最初の効果が始まるまでの時間です。
- **playbackRate**: 外部時間に対するアニメーションの進む速度の比率です。
- **direction**: アニメーションの再生順序です。
- **easing**: アニメーション全体の持続時間に対して、外部時間がどう影響を与えるかの細かな制御を行います。

TimingDictionaries内の値はアニメーションの階層と組み合わされ、アニメーションの逆向き再生、アニメーションの通常再生、およびアニメーションのループの具体的な開始と終了の値を計算します。
いくつかの単純なルールがこれを管理します:

- アニメーションは親の繰り返しの初期値と終了値を越えて実行されることはありません。
- アニメーションは以下の場合にのみ親の繰り返しを越えて実行されます:
    - 相対的なfill値がアニメーションに指定されている場合
    - 相対的なfill値が親に指定されている場合
    - 親のループの最初か、最後である場合
- TimingGroupに`duration`値がなく、子アニメーションの持続時間に基づいて計算される場合

次の例がこれらルールをよく表しています:

    var animationGroup = new AnimationGroup([
      new AnimationSequence([
        new Animation(..., {duration: 3000}),
        new Animation(..., {duration: 5000, fill: 'both'})
      ], {duration: 6000, delay: 3000, fill: 'none'}),
      new Animation(..., {duration: 8000, fill: 'forwards'})
    ], {iterations: 2, fill: 'forwards'});

この例では:

- `AnimationSequence` には6秒の`duration`が指定されています。そのため、二番目の子要素は5秒のうち最初の3秒だけが実行されます。
- `AnimationGroup` は指定された持続時間はありません。したがって、その持続時間は子要素の(`duration` + `delay`)の値のうち最大のものになります。この例では9秒間です。
- しかしながら、 `fill: "both"`が、`AnimationSequence`内の2番めの`Animation`に指定されており、`AnimationSequence`自体は`fill`が"none"指定されているため、アニメーションの終わりは`AnimationSequence`の終わりにと同じになり、`AnimationSequence`の区切りまでアニメーションは逆向きに再生されます。(例：`AnimationGroup`の再生後3秒間)
- `AnimationGroup`内の `Animation`と`AnimationGroup`には両方共 `fill: "forwards"`の指定があるので、アニメーションは二つの場所で進行します:
    - `AnimationGroup`が始まった8秒後から、`AnimationGroup`の二回目の繰り返しが始まるまで(例：1秒)
    - `AnimationGroup`が始まってから17秒後から、無制限に再生


## アニメーションの再生

`Animation`や`TimingGroup`を再生するには、`AnimationPlayer`が必要です:

    var player = document.timeline.play(myAnimation);

AnimationPlayerは開始時間と再生中のアニメーションの現在の場所を完全にコントロールします。しかしアニメーションの内部構造を変更することはできません。


AnimationPlayerは一時停止、移動、逆再生、再生速度の変更に使用できます。

`document.timeline.currentTime` はタイムラインのグローバルタイムです。ドキュメントのloadイベントが怒ってからの秒数を示します。

{% include other-resources.html %}
