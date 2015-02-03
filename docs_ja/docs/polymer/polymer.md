---
layout: default
type: guide
shortname: Docs
title: APIデベロッパーガイド 
subtitle: Guide
---

{% include toc.html %}

{{site.project_title}} _core_ はWebComponentsの上に薄いAPIのレイヤを提供します。
このAPIレイヤは{{site.project_title}}の流儀をはっきり示すとともに、すべての{{site.project_title}}エレメントが使う追加機能を提供し、WebComponentsの開発をより容易にすることを意図しています。

## エレメントの宣言

{{site.project_title}}の心臓部分は [Custom Elements](/platform/custom-elements.html) です。したがって、標準のカスタムエレメントを定義するのと同じようなやり方で{{site.project_title}}のエレメントを定義するのは何ら不思議ではありません。大きな違いは{{site.project_title}}エレメントは`<polymer-element>`タグを使って宣言的に作られるということです。

    <polymer-element name="tag-name" constructor="TagName">
      <template>
        <!-- shadow DOM here -->
      </template>
      <script>
        Polymer({
          // properties and methods here
        });
      </script>
    </polymer-element>

エレメントの宣言には以下のものが含まれます:

-   `name`属性には新しいカスタムエレメントの名前を指定します。
-   `<template>`エレメントは、カスタムエレメントの各インスタンスにShadowDOMとしてコピーされるHTMLを定義します。`<template>`エレメントは必須ではありません。
-   インラインスクリプトは`Polymer`メソッドを呼ぶことでカスタムエレメントを _登録_ し、エレメントのprototypeを渡します。カスタムエレメントを登録すると、そのエレメントはブラウザからタグとして認識されるようになります。

**注意:** `<polymer-element>`の宣言が、`<template>`と`<script>`エレメントの両方を含んでいるときは、`<template>`エレメントが **先に来なければなりません**
{: .alert .alert-info }

### 属性

{{site.project_title}}は特別な属性を`<polymer-element>`で使うために予約しています:

<table class="table responsive-table attributes-table">
  <tr>
    <th>属性名</th><th>必須かどうか</th><th>説明</th>
  </tr>
  <tr>
    <td><code>name</code></td><td><b>必須</b></td><td>カスタムエレメントの名前、"-"を含む必要がある。</td>
  </tr>
  <tr>
    <td><code>attributes</code></td><td>オプション</td><td><a href="#published-properties">公開プロパティ</a>を設定するのに使う。</td>
  </tr>
  <tr>
    <td><code>extends</code></td><td>オプション</td><td><a href="#extending-other-elements">他のエレメントを拡張する</a>のに使う。</td>
  </tr>
  <tr>
    <td><code>noscript</code></td><td>オプション</td><td><code>Polymer()</code>を呼ぶ必要のない簡単なエレメントの場合に指定する。<a href="#altregistration">エレメントの登録</a>を参照のこと。</td>
  </tr>
  <tr>
    <td><code>constructor</code></td><td>オプション</td><td>グローバルオブジェクトに登録されるコンストラクタの名前。ユーザが<code>new</code>を使ってカスタムエレメントを作成できるようにする。 (例：<code>var tagName = new TagName()</code>).</td>
  </tr>

</table>

#### デフォルトの属性 {#defaultattrs}

`<polymer-element>`に記述したその他の属性は自動的にエレメントの各インスタンスに含まれます。例えば:

    <polymer-element name="tag-name" class="active" mycustomattr>
      <template>...</template>
      <script>Polymer();</script>
    </polymer-element>

`<tag-name>`のインスタンスが作られると、`class="active" mycustomattr`という二つの属性がデフォルトで含まれています:

    <tag-name class="active" mycustomattr></tag-name>

#### 属性の大文字小文字 {#attrcase}

HTMLパーサは属性の名前の *大文字小文字を区別しない* ことは気に留めておきましょう。しかし、JavaScriptにおけるプロパティ名は *大文字小文字を区別します* 。

つまり、属性は好きなように名前をつけられるものの、エレメントの属性リストを見ると属性名は常に小文字になっているということです。Polymerはこのことに配慮して、属性とプロパティを注意深く対応させようとします。例えば、次の例では:

    <name-tag nameColor="blue" name="Blue Name"></name-tag>


`nameColor`が実はDOMにおいてすべて小文字であるという事実は一般的に無視して構いません。

これはまた、以下のどの例も同じように動くことを意味します:

    <name-tag NaMeCoLoR="blue" name="Blue Name"></name-tag>
    <name-tag NAMECOLOR="red" name="Red Name"></name-tag>
    <name-tag NAMEcolor="green" name="Green Name"></name-tag>



### エレメントの登録 {#altregistration}

エレメントを登録するには`Polymer`メソッドを使います:

<pre>
Polymer([ <em class="nocode">tag-name</em>, ] [<em class="nocode">prototype</em>]);
</pre>

引数の意味は次のとおりです:

*   _tag-name_ は`<polymer-element>`タグの`name`属性と一致します。** _tag-name_ は必須ではありませんが、`Polymer`メソッドを呼び出す`<script>`タグが`<polymer-element>`タグの外側にある場合は必須です。**

*   _prototype_ は新しいエレメントのプロトタイプです。
 	詳しくは [公開メソッドとプロパティを追加する](#propertiesmethods)を参照して下さい。
    _prototype_ は必須ではありません。

`Polymer`メソッドを呼び出す最も簡単な方法は`<polymer-element>`タグの中でインラインスクリプトとして配置することです:

    <polymer-element name="simple-tag">
      <template> ... </template>
      <script>Polymer();</script>
    </polymer-element>

**注意:** `<polymer-element>`が`<template>`と`<script>`の両方を含む場合は、`<template>`が**必ず先でなくてはなりません。**
{: .alert .alert-info }

インラインスクリプトでエレメントを登録する方法には他にも何種類かあります:

-   [スクリプトをマークアップから分離する](#separatescript).
-   JavaScriptを使って[プログラムでエレメントを登録する](#imperativeregister)

#### スクリプトをマークアップから分離する {#separatescript}

スクリプトとマークアップを分離することで、スクリプトをインラインで書く必要はなくなります。
{{site.project_title}}エレメントは`Polymer`を呼び出す外部スクリプトを参照することでも作成できます:

    <!-- 1. Script referenced inside the element definition. -->
    <polymer-element name="tag-name">
      <template>...</template>
      <script src="path/to/tagname.js"></script>
    </polymer-element>

    <!-- 2. Script comes before the element definition. -->
    <script src="path/to/tagname.js"></script>
    <polymer-element name="tag-name">
      <template>...</template>
    </polymer-element>

#2の例ではスクリプトが`<polymer-element>`タグの前で呼び出されているため、`Polymer`メソッドの引数に**タグ名を必ず渡す必要があります**:

    // tagname.js
    Polymer('tag-name', ... );

独自のプロパティやメソッドを必要としないエレメントでは、`noscript`属性を指定出来ます:

    <!-- 3. No script -->
    <polymer-element name="tag-name" constructor="TagName" noscript>
      <template>
        <!-- shadow DOM here -->
      </template>
    </polymer-element>

`noscript`属性は以下の処理と同じ意味になります:

#### プログラムでエレメントを登録する {#imperativeregister}

エレメントはJavaScriptのみで次のようにして登録することも出来ます:

    <script>
      Polymer('name-tag', {nameColor: 'red'});
      var el = document.createElement('div');
      el.innerHTML = '\
        <polymer-element name="name-tag" attributes="name">\
          <template>\
            Hello <span style="color:{%raw%}{{nameColor}}{%endraw%}">{%raw%}{{name}}{%endraw%}</span>\
          </template>\
        </polymer-element>';
      // The custom elements polyfill can't see the <polymer-element>
      // unless you put it in the DOM.
      document.body.appendChild(el);
    </script>

    <name-tag name="John"></name-tag>

カスタムエレメントpolyfillが認識できるようにするため、`<polymer-element>`をドキュメントに追加する必要があります。

**重要:** この例では`Polymer`メソッドの呼び出しが`<polymer-element>`の外側のため、タグ名を引数に渡す必要があります。
{: .alert .alert-error }

### 公開プロパティとメソッドを追加する {#propertiesmethods}

自前のエレメントにメソッドとプロパティを定義するには、プロトタイプオブジェクトを`Polymer()`メソッドに渡します:

<pre>
Polymer([ <em class="nocode">tag-name</em>, ] <em class="nocode">prototype</em>);
</pre>

次の例は`message`プロパティと、ES5のgetterを使った`greeting`プロパティ、それに`foo`メソッドを定義しています:

    <polymer-element name="tag-name">
      <template>{{greeting}}</template>
      <script>
        Polymer({
          message: "Hello!",
          get greeting() {
            return this.message + ' there!';
          },
          foo: function() {...}
        });
      </script>
    </polymer-element>

**注意:** `this`が指し示すのは{{site.project_title}}エレメント内にあるカスタムエレメントそのものです。例えば `this.localName`は`tag-name`と等しくなります。
{: .alert .alert-info }

#### カスタムエレメントのプロトタイプチェーン

{{site.project_title}} はカスタムエレメントのプロトタイプチェーンを組み立てます。これには次のものが含まれます:

-   `Polymer`メソッドに渡されたプロタイプオブジェクト
-   組み込みメソッドやプロパティを追加する{{site.project_title}}のベースプロトタイプ。([Built-in element methods](#builtin)参照のこと)
-   カスタムエレメントが継承したネイティブDOMエレメントのプロトタイプオブジェクト。(デフォルトでは `HTMLElement`)

`id`や`children`, `focus`, `title`, `hidden`といったネイティブDOMの持つプロパティやメソッドと同じ名前のプロパティやメソッドを定義するのは避けて下さい。
どのような結果になるか予想がつきません。

### プライベート変数やスタティック変数を追加する {#static}

エレメント内部で状態を保持する必要があるなら、自己呼び出しの無名関数といった標準的なテクニックを使ってスクリプトをラップして下さい:

    <polymer-element name="tag-name">
      <template>...</template>
      <script>
        (function() {
          // Run once. Private and static to the element.
          var foo_ = new Foo();

          // Run for every instance of the element that's created.
          Polymer({
            get foo() { return foo_; }
          });
        })();
      </script>
    </polymer-element>



### グローバル変数をサポートする {#global}

すべてのエレメントから参照できるグローバルプロパティをアプリケーションで定義したいと思うことがあるかもしれません。例えば:

- すべてのアニメーションで使うイージングカーブ
- 現在ログインしているユーザーの情報

これを実現するには、[MonoState Pattern](http://c2.com/cgi/wiki?MonostatePattern)を利用できます。{{site.project_title}}エレメントを定義する際に、グローバルにしたい変数を含むクロージャーを定義し、オブジェクトのプロトタイプにアクセッサメソッドを定義するか、`ready`コールバック関数を使ってこのクロージャを個々のインスタンスにコピーします。

    <polymer-element name="app-globals">
      <script>
      (function() {
        // these variables are shared by all instances of app-globals
        var firstName = 'John';
        var lastName = 'Smith';

        Polymer({
           ready: function() {
             // copy global values into instance properties
             this.firstName = firstName;
             this.lastName = lastName;
           }
        });
      })();
      </script>
    </polymer-element>

次に`<app-globals>`エレメントを他のエレメント共に使います。すると、{{site.project_title}}のデータバインディングを使うか、JavaScriptを使って、`<app-global>`エレメントのプロパティにアクセスできるようになります:

    <polymer-element name="my-component">
      <template>
        <app-globals id="globals"></app-globals>
        <div id="firstname">{%raw%}{{$.globals.firstName}}{%endraw%}</div>
        <div id="lastname">{%raw%}{{$.globals.lastName}}{%endraw%}</div>
      </template>
      <script>
        Polymer({
          ready: function() {
            console.log('Last name: ' + this.$.globals.lastName);
          }
        });
      </script>
    </polymer-element>

この方法にちょっとした工夫をすることで、グローバル変数の値を外部で設定できるようになります:

    <polymer-element name="app-globals" attributes="values">
      <script>
        (function() {
          var values = {};
          
          Polymer({
             ready: function() {
               this.values = values;
               for (var i = 0; i < this.attributes.length; ++i) {
                 var attr = this.attributes[i];
                 values[attr.nodeName] = attr.value;
               }
             }
          });
        })();
      </script>
    </polymer-element>


次のようにこのエレメントの呼び出し時に属性を渡すことでグローバル変数の値を設定できます:

    <app-globals firstname="Addy" lastname="Osmani"></app-globals>

`app-globals`のこの2つ目の例は最初の例とは少し違ったAPIになっています。グローバル変数は`app-globals`の直接のプロパティではなく、`values`オブジェクトのプロパティになっています。
属性を使って値を設定する方法には二つの制限があります:値は文字列でなくてはならず、変数名は小文字でなければなりません。
(詳しくは[属性名の大文字小文字](#attrcase)を参照して下さい。)

先ほどの`<my-component>`とこの`<app-globals>`を一緒に使うには、グローバル変数の読み出し方法を変更する必要があります。
(例えば、`$.globls.lastName`ではなく、`$.globals.values.lastname` のように)

### エレメントのライフサイクルメソッド {#lifecyclemethods}

{{site.project_title}} はエレメントの各ライフサイクルで呼び出されるコールバックを第一級要素としてサポートしています。しかしながらより便利にするために、それらを短い名前で実装しています。 

すべてのライフサイクルコールバックは必須ではありません:

    Polymer('tag-name', {
      created: function() { ... },
      ready: function() { ... },
      attached: function () { ... },
      domReady: function() { ... },
      detached: function() { ... },
      attributeChanged: function(attrName, oldVal, newVal) {
        //var newVal = this.getAttribute(attrName);
        console.log(attrName, 'old: ' + oldVal, 'new:', newVal);
      },
    });

以下にカスタムエレメントのライフサイクルメソッド一覧の、[WebComponentsの仕様](https://dvcs.w3.org/hg/webcomponents/raw-file/tip/spec/custom/index.html#custom-element-lifecycle) と{{site.project_title}}の対比を示します。

仕様 | {{site.project_title}} | 呼び出されるタイミング
|-
createdCallback | created | エレメントのインスタンス作成時
- | ready | `<polymer-element>`の準備が出来た時(例えば、shadowDOMが作られ、プロパティの監視が始まり、イベントリスナが登録された、など)
attachedCallback | attached | エレメントのインスタンスがDOMに追加された時
- | domReady | エレメントの初期子要素が用意出来た時。このメソッドはlightDOMの子要素や親要素を操作するのに最適なタイミングです。他の使い方としては、同じDOM階層にあるAとBの二つのカスタムエレメント(例えば同じ `.innerHTML` 内にある場合など)のうち、AからBのメソッドやプロパティを呼び出すにはBの準備ができている必要がありますが、`domReady`コールバックは両方のエレメントが存在することを保証します。
detachedCallback | detached | インスタンスがDOMからとりのぞかれた時
attributeChangedCallback | attributeChanged | 属性が追加、削除、変更された時 **注意**: [Changed watchers](#change-watchers)は大抵の場合より簡単に使うことが出来、通常のプロパティに加え、[公開プロパティ](#published-properties)の変更も監視できます。
{: .table .responsive-table .lifecycle-table }

### polymer-ready イベント {#polymer-ready}

{{site.project_title}} は要素定義を解析し、その要素を _非同期に_ polymer-elementへと変換します。エレメントが変換される前に、DOMからそのエレメントを取得すると、カスタムエレメントではなくふつうの`HTMLElement`が返されます。{{site.project_title}}エレメントはまたロードする必要のあるインラインリソース(たとえばスタイルシート)をサポートしています。エレメントが表示される前にこれらのリソースが読み込まれていないと、[FOUC](http://en.wikipedia.org/wiki/Flash_of_unstyled_content)問題(スタイル適用前の要素がちらつく問題)を引き起こすことがあります。FOUCを避けるため、{{site.project_title}}はスタイルシートが完全に読み込まれるまでエレメントの登録を遅らせます。

いつエレメントが登録／変換完了し、操作ができるようになったかを知るには、`polymer-ready` イベントを使います。

    <head>
      <link rel="import" href="path/to/x-foo.html">
    </head>
    <body>
      <x-foo></x-foo>
      <script>
        window.addEventListener('polymer-ready', function(e) {
          var xFoo = document.querySelector('x-foo');
          xFoo.barProperty = 'baz';
        });
      </script>
    </body>

## 特徴 {#features}

### 公開プロパティ

プロパティを _公開_ すると、そのプロパティはエレメントの"公開API"の一部となります。プロパティの公開は以下の特徴を持ちます:

*   宣言的な双方向データバインディングをサポートする

*   一致する名前を持つHTML属性を使った宣言的な初期化

*   オプションとして、プロパティの現在値が、一致する名前を持つHTML属性に_反映_される

**注意:** プロパティ名は大文字小文字を区別します。しかしHTML属性名は区別しません。
[属性の大文字小文字](#attrcase)で解説したように、{{site.project_title}}は各プロパティ名を対応する属性名に一致させようとします。つまり、同じ綴りを持った、大文字小文字の違いしか無いプロパティを二つ同時に使うことは出来ません。(たとえば `name` と `NAME`など)
{: .alert .alert-info }

プロパティを公開するには二つの方法があります:

*   **推奨** - 公開したいプロパティ名を`<polymer-element>`の`attributes`属性に含めること

*   公開したいプロパティ名をプロトタイプの `publish` オブジェクトに含めること

以下は、`attributes`属性を使って`foo` `bar` `baz`の3つのプロパティを公開する例をあげます:

    <polymer-element name="x-foo" attributes="foo bar baz">
      <script>
        Polymer();
      </script>
    </polymer-element>

次に `publish` オブジェクトを使った例です:

    <polymer-element name="x-foo">
      <script>
        Polymer({
          publish: {
            foo: 'I am foo!',
            bar: 5,
            baz: {
              value: false,
              reflect: true
            }
          }
        });
      </script>
    </polymer-element>

[属性の反映](#attrreflection)を有効にするために、`baz`プロパティは違ったフォーマットになっていることに注目して下さい。

大体において`attributes`属性を使うことが好ましい方法です。なぜならそのほうが宣言的なやり方であり、アクセス可能なプロパティをエレメントの一番最初で俯瞰できるからです。

しかし、以下のいずれかに当てはまる場合は、オブジェクトを`publish`する方法を選ぶべきです:

*   エレメントに大量のプロパティがあり、全てを一行に書くのは見苦しい場合
*   プロパティのデフォルト値を設定する必要があり、DRY原則にのっとって一か所で処理を済ませたい場合
*   プロパティの変更を対応する属性に反映する必要がある場合

#### プロパティのデフォルト値

デフォルトでは`attributes`で定義されたプロパティは`undefined`で初期化されます:

    <polymer-element name="x-foo" attributes="foo">
      <script>
        // x-foo has a foo property with default value of undefined.
        Polymer();
      </script>
    </polymer-element>


具体的に言うと、{{site.project_title}}は`undefined`という値を持ったプロパティ`foo`をエレメントのプロトタイプに追加します。

**注意:** {{site.project_title}} 0.3.5 まではプロパティは'null'で初期化されていました。
{: .alert .alert-info }

エレメントの`prototype`でデフォルト値を明示的に指定することで、独自のデフォルト値を設定できます
:

    <polymer-element name="x-foo" attributes="bar">
      <script>
        Polymer({
          // x-foo has a bar property with default value false.
          bar: false
        });
      </script>
    </polymer-element>

あるいは、`publish`プロパティを使って同じことが出来ます:

    <polymer-element name="x-foo">
      <script>
        Polymer({
          publish: {
            bar: false
          }
        });
      </script>
    </polymer-element>

プロパティの値がオブジェクトであったり配列であったりする場合、デフォルト値を設定するのは`created`コールバック内で行う必要があります。これによって、各インスタンスごとに異なったオブジェクトが確実に割り当てられるようになります:

    <polymer-element name="x-default" attributes="settings">
      <script>
        Polymer({
          created: function() {
            // create a default settings object for this instance
            this.settings = {
              textColor: 'blue'
            };
          }
        });
      </script>
    </polymer-element>


#### 属性を通じてエレメントを設定する

属性はユーザーがエレメントを宣言的に設定する素晴らしい方法です。
公開プロパティに対応する属性名に初期値を設定することで、プロパティの値を変更できます:

    <x-foo name="Bob"></x-foo>

プロパティの値が文字列でない場合、{{site.project_title}}は属性値を適切な方に変換しようと試みます。

属性からプロパティへの接続は__一方通行です__。[attribute reflection](#attrreflection)が有効になっていない限り、プロパティの値を変更しても、属性の値は**変更されません**。

**注意**: エレメントを属性値で設定することを[データバインディング](databinding.html)と混同しないで下さい。プロパティへのデータバインディングは参照です。つまり、値は文字列に変換されません。
{: .alert .alert-info}

##### プロパティの型ヒンティング {#attrhinting}

属性値がプロパティの値に変換される際に、{{site.project_title}}はプロパティのデフォルト値に応じて値を適切な型に変換しようと試みます。

例えば、`x-hint`エレメントに`count`プロパティがあり、そのデフォルト値が`0`であったとしましょう。

    <x-hint count="7"></x-hint>

`count`はNubmer型なので、{{site.project_title}}は文字列型の"7"をNumber型に変換します。

プロパティがオブジェクトか配列の場合、二重引用符を使ったJSON文字列で設定できます。例えば:

    <x-name fullname='{ "first": "Bob", "last": "Dobbs" }'></x-name>

この例はJavaScriptでエレメントの`fullname`プロパティをオブジェクトリテラルで設定するのと同じです:

    xname.fullname = { first: 'Bob', last: 'Dobbs' };

デフォルト値はプロトタイプに設定することが出来ます。その場合は`publish`オブジェクトを使うか、`created`コールバック内で行います。
次の例は3つのパターンを全て含んだ珍しい例です:

    <polymer-element name="hint-element" attributes="isReady items">
      <script>
        Polymer({

          // hint that isReady is a Boolean
          isReady: false,

          publish: {
            // hint that count is a Number
            count: 0
          },

          created: function() {
            // hint that items is an array
            this.items = [];
          }
        });
      </script>
    </polymer-element>

**重要ポイント:** オブジェクトか配列のプロパティは常に`created`コールバックで初期化すべきです。デフォルト値を`prototype`に自家に設定したり、`publish`オブジェクトに設定したりすると、異なったインスタンス間で意図しない状態の共有が起こることがあります。
{: .alert .alert-error }

    // Good!
    Polymer('x-foo', {
      created: function() {
        this.list = []; // Initialize and hint type to be array.
        this.person = {}; // Initialize and hint type to an object.
      }
    });

    // Bad.
    Polymer('x-foo', {
      list: [], // Don't initialize array or objects on the prototype.
      person: {}
    });

#### プロパティから属性への値の反映 {#attrreflection}

通常プロパティの値は対応する属性に__反映されることありません__。
例えば、`name`プロパティで値の反映が有効になっていると、エレメント内で`this.name = "Joe"`とすることは、`this.setAttribute('name', 'Joe')`という呼び出しと等しくなります。
エレメントは次のようにDOMを更新します:

    <x-foo name="Joe"></x-foo>

プロパティの値の反映は、デフォルトの動作から外れたいくつかの場合に便利です。
大抵の場合、属性値によってエレメントのスタイルを変えたい場合にのみ、プロパティの値の反映が必要になります。

値の反映を有効にするには、プロパティを`publish`オブジェクト内で定義します。
次のようにするのではなく:

<pre>
<var>propertyName</var>: <var>defaultValue</var>
</pre>

このようにプロパティを指定して下さい：

<pre>
<var>propertyName</var>: {
  <b>value:</b> <var>defaultValue</var>,
  <b>reflect:</b> <b>true</b>
}
</pre>

プロパティの値はその型に応じて文字列に変換されます。いくつかの型は特別な扱いを受けます:

*   プロパティの値がオブジェクト、配列、または関数の場合、その値は`reflect`が`true`に設定されていても、**決して反映されません**。

*   プロパティの値がブーリアンの場合、属性は通常のブーリアン属性のように振る舞います: 値が真の場合のみ属性が存在する状態になります。

また、プロパティの初期値は属性に**反映されない**ことに留意して下さい。したがって、値を変更するまで属性はDOM上にはしません。

例えば:

    <polymer-element name="disappearing-element">
      <script>
        Polymer({
          publish: {
            hidden: {
              value: false,
              reflect: true
            }
          }
        });
      </script>
    </polymer-element>

`hidden = true`に設定することで、`<disappearing-element>`の`hidden`属性がDOMに存在する状態になり、次のようになります:

    <disappearing-element hidden>Now you see me...</disappearing-element>

属性の_反映_はデータバインディングとは別のものです。双方向データバインディングは公開プロパティが反映されるかどうかに関係なく有効になります。次の例を見て下さい:

    <my-element name="{%raw%}{{someName}}{%endraw%}"></my-element>

`name`プロパティが値を反映_しない_ように設定されていても、`name`属性は常にDOM上に`name="{%raw%}{{someName}}{%endraw%}"`と表示されます。もし`name`が値を反映_する_ように設定されたいたら、DOM上の属性は`someName`に設定された値になります。

### データバインディングと公開プロパティ

公開プロパティは{{site.project_title}}エレメント内でデータバインドされ、`{%raw%}{{}}{%endraw%}`を使ってアクセス可能になります。このバインドは参照であり、双方向です。

例えば、`name`と`nameColor`という二つの公開プロパティを持つ`name-tag`というエレメントを定義したとします。

    <polymer-element name="name-tag" attributes="name nameColor">
      <template>
        Hello! My name is <span style="color:{%raw%}{{nameColor}}{%endraw%}">{%raw%}{{name}}{%endraw%}</span>
      </template>
      <script>
        Polymer({
          nameColor: "orange"
        });
      </script>
    </polymer-element>

この例では公開プロパティ`name`の初期値は`undefined`で、`nameColor`の初期値は"orange"です。したがって、`<span>`のテキスト色はオレンジになります。

より詳しくは、[データバインディングの概要](databinding.html)を参照して下さい。

### 算出プロパティ

算出プロパティは他のプロパティの値に基づいて動的に値が変わるプロパティです。
算出プロパティも、その値を公開して外部エレメントにバインドすることが出来ます。

算出プロパティはエレメントのプロトタイプ内の`computed`オブジェクト内で定義されます。

<pre class="nocode">
<b>computed: {</b>
  <var>property-name</var><b>: '</b><var>expression</var><b>'
}</b>
</pre>

各算出プロパティはプロパティ名と[Polymer式](/docs/polymer/expressions.html)で定義されます。
算出プロパティの値は計算式のどれかが変更されると動的に更新されます。

次の例では、`num`という入力値が変更されると、算出プロパティの`square`が自動的に更新されます。

{% include samples/computed-property.html %}

算出プロパティは読み出し専用です: 例えば、`square`プロパティに値を設定してもなんの効果もありません。

他のプロパティ同様に、`attributes`か`publish`オブジェクトに追加することで、算出プロパティを公開できます。
`publis`オブジェクトに設定されたデフォルト値は無視されます。

**制限あり**: {{site.project_title}}のバージョン0.4.0以前では、算出プロパティは公開できませんでした。
例えば次のようにして`square`プロパティをバインドすることは出来ません。
 `<square-element square="{%raw%}{{value}}{%endraw%}>`.
{: .alert .alert-warning }

### 宣言的なイベントマッピング

{{site.project_title}}はコンポーネントにおける宣言的なイベントとメソッドのマッピングをサポートしています。
マッピングされた動作を呼び出すには特別な<code>on-<em>event</em></code>書式を使います。

    <polymer-element name="g-cool" on-keypress="{% raw %}{{keypressHandler}}{% endraw %}">
      <template>
        <button on-click="{% raw %}{{buttonClick}}{% endraw %}"></button>
      </template>
      <script>
        Polymer({
          keypressHandler: function(event, detail, sender) { ...},
          buttonClick: function(event, detail, sender) { ... }
        });
      </script>
    </polymer-element>

この例では、`on-keypress`宣言がDOM標準の`"keypress"`イベントを、エレメント内の`keypressHandler`に結びつけています。同様にエレメント内部ののボタンでは`on-click`ハンドラがクリックイベントに応じて`buttonClick`メソッドを呼び出します。これらは一切の追加プログラムなしに実現されます。

いくつかの注意事項があります:

* イベントハンドラ属性の値はコンポーネントのメソッド名を文字列で表現したものです。
* イベントハンドラには以下の引数が渡されます:
  * `inEvent` は [standard event object](http://www.w3.org/TR/DOM-Level-3-Events/#interface-Event)です。
  * `inDetail`: `inEvent.detail`の便利な形です
  * `inSender`: ハンドラを宣言したノードへの参照です。多くの場合、`inEvent.target`(イベントを受け取る最下層のノード)や`inEvent.currentTarget`(イベントを処理しているコンポーネント)とは異なります。そのため、{{site.project_title}}はこの値を直接提供します。

#### プログラムによるイベントマッピング

もうひとつの方法として、プログラムで{{site.project_title}}エレメントにイベントハンドラを追加できます。

**注意:** 一般的に宣言的なやり方のほうが好ましい方法です。
{: .alert .alert-info}

    <polymer-element name="g-button">
      <template>
        <button>Click Me!</button>
      </template>
      <script>
        Polymer({
          eventDelegates: {
            up: 'onTap',
            down: 'onTap'
          },
          onTap: function(event, detail, sender) {
            ...
          }
        });
      </script>
    </polymer-element>

この例では`g-button`という名前の{{site.project_title}}エレメントの`up`と`down`イベントにイベントリスナを追加ししています。イベントリスナはテンプレート内の子要素ではなく、エレメントそのものに追加します。
イベントリスナはエレメントのイベントを処理し、内から外へイベントを伝播させます。このコードは`<polymer-element>`に直に<code>on-<em>event</em></code>ハンドラを追加するのと同じです。

<code>on-<em>event</em></code>属性と`eventDelegates`オブジェクトの関係は、`attributes`属性と`publish`オブジェクトの関係に似ています。

`eventDelegates`オブジェクト内のキーは処理するイベント名で、値はコールバック関数です。`onTap`イベントハンドラは宣言的に定義したときと同様の引数を受け取ります。

### 自動ノード検索

{{site.project_title}}のもうひとつの便利な昨日は自動ノード検索です。
コンポーネントのshadow DOM内にあるノードのうち、`id`属性を持つものは、自動的にコンポーネントの`this.$`ハッシュに登録され、参照可能になります。

**注意:** データバインディングによって動的に作られるノードは`this.$`ハッシュには_追加されません_。ハッシュにはインスタンス化時のshadow DOMノードだけが含まれます。(つまり、一番外側のテンプレートに含まれる要素のみということです)
{: .alert .alert-warning }

例えば、次の例では`id`が`nameInput`という`<input>`を含んだテンプレートを定義しています。このコンポーネントは`this.$.nameInput`という書式で`<input>`の
For example, the following defines a component whose template contains an `<input>` element whose `id` attribute is `nameInput`. The component can refer to that element with the expression `this.$.nameInput`.

    <polymer-element name="x-form">
      <template>
        <input type="text" id="nameInput">
      </template>
      <script>
        Polymer({
          logNameValue: function() {
            console.log(this.$.nameInput.value);
          }
        });
      </script>
    </polymer-element>

To locate other nodes inside the element's shadow DOM, you can create a
container element with a known ID and use `querySelector` to retrieve
descendants. For example, if your element's template looks like this:

    <template>
      <div id="container">
        <template if="{%raw%}{{some_condition}}{%endraw%}">
          <div id="inner">
           This content is created by data binding.
          </div>
        </template>
      </div>
    </template>

You can locate the inner container using:

    this.$.container.querySelector('#inner');

### Observing properties {#observeprops}

#### Changed watchers {#change-watchers}

The simplest way to observe property changes on your element is to use a changed watcher.
All properties on {{site.project_title}} elements can be watched for changes by implementing a <code><em>propertyName</em>Changed</code> handler. When the value of a watched property changes, the appropriate change handler is automatically invoked.

    <polymer-element name="g-cool" attributes="better best">
      <script>
        Polymer({
          better: '',
          best: '',
          betterChanged: function(oldValue, newValue) {
            ...
          },
          bestChanged: function(oldValue, newValue) {
            ...
          }
        });
      </script>
    </polymer-element>

In this example, there are two watched properties, `better` and `best`. The `betterChanged` and `bestChanged` function will be called whenever `better` or `best` are modified, respectively.

#### Custom property observers &mdash; the `observe` object {#observeblock}

Sometimes a [changed watcher](#change-watchers) is not enough. For more control over
property observation, {{site.project_title}} provides the `observe` object.

An `observe` object defines a custom property/observer mapping for one or more properties.
It can be used to watch for changes to nested objects or share the same callback
for several properties.

**Example:** sharing a single observer

    Polymer('x-element', {
      foo: '',
      bar: '',
      observe: {
        foo: 'validate',
        bar: 'validate'
      },
      ready: function() {
        this.foo = 'bar';
        this.bar = 'foo';
      },
      validate: function(oldValue, newValue) {
        ...
      },
    });

In the example, `validate()` is called whenever `foo` or `bar` changes.

**Example:** using automatic node finding in an `observe` object

When an element has an ID, you can use the `this.$` hash in the `observe` object to watch
a property on that element. 

    <template>
      <x-foo id="foo"></x-foo>
    </template>
    ...
    Polymer('x-element', {
      observe: {
        '$.foo.someProperty': 'fooPropertyChanged'
      },
      fooPropertyChanged: function(oldValue, newValue) {
        ...
      }
    });

All property names in the `observe` object are relative to `this`, so `$.foo.someProperty` 
refers to a property on the `<x-foo>` element. See the section on 
[automatic node finding](#automatic-node-finding) for more infomration on the `this.$`
hash and its limitations.

**Example:** watching for changes to a nested object path

    Polymer('x-element', {
      observe: {
        'a.b.c': 'validateSubPath'
      },
      ready: function() {
        this.a = {
          b: {
            c: 'exists'
          }
        };
      },
      validateSubPath: function(oldValue, newValue) {
        var value = Path.get('a.b.c').getValueFrom(this);
        // oldValue == undefined
        // newValue == value == this.a.b.c === 'exists'
      }
    });

It's important to note that **{{site.project_title}} does not call the <code><em>propertyName</em>Changed</code> callback for properties included in the `observe` object**. Instead, the defined observer gets called.

    Polymer('x-element', {
      bar: '',
      observe: {
        bar: 'validate'
      },
      barChanged: function(oldValue, newValue) {
        console.log("I'm not called");
      },
      validate: function(oldValue, newValue) {
        console.log("I'm called instead");
      }
    });


### Firing custom events {#fire}

{{site.project_title}} core provides a convenient `fire()` method for
sending custom events. Essentially, it's a wrapper around your standard `node.dispatchEvent(new CustomEvent(...))`. In cases where you need to fire an event after microtasks have completed,
use the asynchronous version: `asyncFire()`.

Example:

{% raw %}
    <polymer-element name="ouch-button">
      <template>
        <button on-click="{{onClick}}">Send hurt</button>
      </template>
      <script>
        Polymer({
          onClick: function() {
            this.fire('ouch', {msg: 'That hurt!'}); // fire(type, detail, targetNode, bubbles?, cancelable?)
          }
        });
      </script>
    </polymer-element>

    <ouch-button></ouch-button>

    <script>
      document.querySelector('ouch-button').addEventListener('ouch', function(e) {
        console.log(e.type, e.detail.msg); // "ouch" "That hurt!"
      });
    </script>
{% endraw %}

**Tip:** If your element is within another {{site.project_title}} element, you can
use the special [`on-*`](#declarative-event-mapping) handlers to deal with the event: `<ouch-button on-ouch="{% raw %}{{myMethod}}{% endraw %}"></ouch-button>`
{: .alert .alert-success }

### Extending other elements

A {{site.project_title}} element can extend another element by using the `extends`
attribute. The parent's properties and methods are inherited by the child element
and data-bound.

    <polymer-element name="polymer-cool">
      <template>
        You are {%raw%}{{praise}}{%endraw%} <content></content>!
      </template>
      <script>
        Polymer({
          praise: 'cool'
        });
      </script>
    </polymer-element>

    <polymer-element name="polymer-cooler" extends="polymer-cool">
      <template>
        <!-- A shadow element renders the extended
             element's shadow DOM here. -->
        <shadow></shadow> <!-- "You are cool Matt" -->
      </template>
      <script>
        Polymer();
      </script>
    </polymer-element>

    <polymer-cooler>Matt</polymer-cooler>

#### Overriding a parent's methods

When you override an inherited method, you can call the parent's method with `this.super()`, and optionally pass it a list of arguments (e.g. `this.super([arg1, arg2])`). The reason the parameter is an array is so you can write `this.super(arguments)`.

{% raw %}
    <polymer-element name="polymer-cool">
      <template>
        You are {{praise}} <content></content>!
      </template>
      <script>
        Polymer({
          praise: 'cool',
          makeCoolest: function() {
            this.praise = 'the coolest';
          }
        });
      </script>
    </polymer-element>

    <polymer-element name="polymer-cooler" extends="polymer-cool"
                     on-click="{{makeCoolest}}">
      <template>
        <shadow></shadow>
      </template>
      <script>
        Polymer({
          praise: 'cool',
          makeCoolest: function() {
            this.super(); // calls polymer-cool's makeCoolest()
          }
        });
      </script>
    </polymer-element>

    <polymer-cooler>Matt</polymer-cooler>
{% endraw %}

In this example, when the user clicks on a `<polymer-cooler>` element, its
`makeCoolest()` method is called, which in turn calls the parent's version
using `this.super()`. The `praise` property (inherited from `<polymer-cool>`) is set
to "coolest".

## Built-in element methods {#builtin}

{{site.project_title}} includes a few handy methods on your element's prototype.
A few of the utility methods are documented here:

* [`onMutation`](#onMutation) can be used to set up a callback when the element's children change.
* [`async`](#asyncmethod) schedules a task to be executed after a configurable timeout, and is 
  synchronized with `requestAnimationFrame`.
* [`job`](#job) can be used to debounce event handlers, ensuring that a task is executed only
  once during a given time window.

An additional instance method, [`injectBoundHTML`](databinding-advanced.html#boundhtml), 
is documented in the data-binding section.

### Observing changes to light DOM children {#onMutation}

To know when light DOM children change, you can setup a Mutation Observer to
be notified when nodes are added or removed. To make this more convenient, {{site.project_title}} adds an `onMutation()` callback to every element. Its first argument is the DOM element to
observe. The second argument is a callback which is passed the `MutationObserver` and the mutation records:

    this.onMutation(domElement, someCallback);

**Example** - Observe changes to (light DOM) children elements:

    ready: function() {
      // Observe a single add/remove.
      this.onMutation(this, this.childrenUpdated);
    },
    childrenUpdated: function(observer, mutations) {
      // getDistributedNodes() has new stuff.

      // Monitor again.
      this.onMutation(this, this.childrenUpdated);
    }

### Dealing with asynchronous tasks {#asyncmethod}

Many things in {{site.project_title}} happen asynchronously. Changes are gathered up
and executed all at once, instead of executing right away. Batching
changes creates an optimization that (a) prevents duplicated work and (b) reduces unwanted [FOUC](http://en.wikipedia.org/wiki/Flash_of_unstyled_content).

[Change watchers](#change-watchers) and situations that rely on data-bindings
are examples that fit under this async behavior. For example, conditional templates may not immediately render after setting properties because changes to those renderings are saved up and performed all at once after you return from JavaScript.

To do work after changes have been processed, {{site.project_title}} provides `async()`.
It's similar to `window.setTimeout()`, but it automatically binds `this` to the correct value and is timed to `requestAnimationFrame`:

    // async(inMethod, inArgs, inTimeout)
    this.async(function() {
      this.foo = 3;
    }, null, 1000);

    // Roughly equivalent to:
    //setTimeout(function() {
    //  this.foo = 3;
    //}.bind(this), 1000);

The first argument is a function or string name for the method to call asynchronously.
The second argument, `inArgs`, is an optional object or array of arguments to
pass to the callback.

In the case of property changes that result in DOM modifications, follow this pattern:

    Polymer('my-element', {
      propChanged: function() {
        // If "prop" changing results in our DOM changing,
        // schedule an update after the new microtask.
        this.async(this.updateValues);
      },
      updateValues: function() {...}
    });

### Delaying work {#job}

Sometimes it's useful to delay a task after an event, property change, or user interaction. A common way to do this is with `window.setTimeout()`:

    this.responseChanged = function() {
      if (this.timeout1) {
        clearTimeout(this.toastTimeout1);
      }
      this.timeout1 = window.setTimeout(function() {
        ...
      }, 500);
    }

However, this is such a common pattern that {{site.project_title}} provides the `job()` utility for doing the same thing:

    this.responseChanged = function() {
      this.job('job1', function() { // first arg is the name of the "job"
        this.fire('done');
      }, 500);
    }

`job()` can be called repeatedly before the timeout but it only results in a single side-effect. In other words, if `responseChanged()` is immediately executed 250ms later, the `done` event won't fire until 750ms.

## Advanced topics {#additional-utilities}

### Life of an element's bindings {#bindings}

When you remove an element from the DOM, {{site.project_title}} asynchronously
deactivates its {%raw%}`{{}}`{%endraw%} bindings and `*Changed` methods. This helps prevent
memory leaks, ensuring the element will be garbage collected.

If you want the element to "remain active" when it's not in the `document`,
call `cancelUnbindAll()` right after you remove it. The [lifecycle methods](#lifecyclemethods)
are a good place for this:

    Polymer('my-element', {
      detached: function() {
        // Keep bindings active when this element is removed
        this.cancelUnbindAll();
      }
    });

If you explicitly call `cancelUnbindAll()`, {{site.project_title}} won't manage
the bindings automatically. It's your responsibility to manage the element's
bindings by eventually doing one of the following:

-   Adding the element back into the DOM.
-   Explicitly unbinding the element by calling the `unbindAll` or
    `asyncUnbindAll` method.

    var el = document.querySelector('my-element');
    el.parentNode.removeChild(el);

     ...
    // finished with this element, not going to reinsert it.
    el.unbindAll();

If you fail to unbind or reinsert an element, your application may leak memory.

#### Using preventDispose {#preventdispose}

To force bindings from being removed in all cases, set `.preventDispose`:

    Polymer('my-element', {
      preventDispose: true
    });

### How data changes are propagated {#flush}

Data changes in {{site.project_title}} happen almost immediately (at end of a microtask)
when `Object.observe()` is available. When it's not supported, {{site.project_title}} uses a polyfill ([observe-js](https://github.com/Polymer/observe-js)) to poll and periodically propagate data-changes throughout the system. This is done through a method called `Platform.flush()`.

#### What is `Platform.flush()`?

`Platform.flush()` is part of {{site.project_title}}'s data observation polyfill, [observe-js](https://github.com/Polymer/observe-js). It dirty check's all objects that have been observed and ensures notification callbacks are dispatched. {{site.project_title}} automatically calls `Platform.flush()` periodically, and this should be sufficient for most application workflows. However, there are times when you'll want to call `Platform.flush()` in application code.

**Note:** on platforms that support `Object.observe()` natively, `Platform.flush()` does nothing.
{: .alert .alert-info }

#### When should I call `Platform.flush()`?

Instead of waiting for the next poll interval, one can manually schedule an update by calling `Platform.flush()`. **There are very few cases where you need to call `Platform.flush()` directly.**

If it's important that a data change propagates before the next screen paint, you may
need to manually call `Platform.flush()`. Here are specific examples:

1. A property change results in a CSS class being added to a node. Often, this works out fine, but sometimes, it's important to make sure the node does not display without the styling from the added class applied to it. To ensure this, call `Platform.flush()` in the property change handler after adding the CSS class.
2. The author of a slider element wants to ensure that data can propagate from it as the user slides the slider. A user of the element, might, for example, bind the slider's value to an input and expect to see the input change while the slider is moving. To achieve this, the element author calls `Platform.flush()` after setting the element's value in the `ontrack` event handler.

**Note:** {{site.project_title}} is designed such that change notifications are asynchronous. Both `Platform.flush()` and `Object.observe()` (after which it's modeled) are asynchronous. Therefore, **`Platform.flush()` should not be used to try to enforce synchronous data notifications**. Instead, always use [change watchers](#change-watchers) to be informed about state.
{: .alert .alert-info }

### How {{site.project_title}} elements prepare themselves {#prepare}

For performance reasons, `<polymer-element>`s avoid the expense of preparing ShadowDOM, event listeners, and property observers if they're created outside the main document.
This behavior is similar to how native elements such as `<img>` and `<video>` behave.
They remain in a semi-inert state when created outside the main document (e.g. an `<img>` avoids the expense of loading its `src`).

{{site.project_title}} elements prepare themselves automatically in the following cases:

1. when they're created in a `document` that has a `defaultView` (the main document)
2. when they receive the `attached` callback
3. when they're created in the `shadowRoot` of another element that is preparing itself

In addition, if the `.alwaysPrepare` property is set to `true`, {{site.project_title}} elements
prepare themselves even when they do not satisfy the above rules.

    Polymer('my-element', {
      alwaysPrepare: true
    });

**Note:** an element's [`ready()` lifecycle callback](#lifecyclemethods) is called after an element has been prepared. Use `ready()` to know when an element is done initializing itself.
{: .alert .alert-success }

### Resolving paths of sibling elements {#resolvepath}

For the general case of element re-use and sharing, URLs in HTML Imports are meant to be relative to the location of the import. The majority of the time, the browser takes care of this for you.

However, JavaScript doesn't have a notion of a local import. Therefore, {{site.project_title}} provides a `resolvePath()` utility for converting paths relative to the import to paths relative to the document.

For example: If you know your import is in a folder containing a resource (e.g `x-foo.png`), you can get a path to `x-foo.png` which will work relative to the main document by calling `this.resolvePath('x-foo.png')`.

Visually, this might look like the following:

    index.html
    components/x-foo/
      x-foo.html
      x-foo.png

At an element level, where `this` refers to an instance of an `x-foo` created by `index.html`, `this.resolvePath('x-foo.png') === 'components/x-foo/x-foo.png'`.
