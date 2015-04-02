---
layout: default
type: guide
shortname: Docs
title: デバッグのコツ
subtitle: Guide
---

{% include toc.html %}

<style>
.bookmarklet {
  background: #657CFE;
  color: white;
  border-radius: 6px;
  padding: 4px 8px 4px 4px;
}
.bookmarklet core-icon {
  margin-bottom: 2px;
}
</style>

Webコンポーネント標準は比較的新しく、全てのブラウザで実装されているわけではないため、PolymerエレメントのようなWebコンポーネントをデバッグするのには困難を伴うことがあります。

Chrome36以降では、Webコンポーネントのネイティブサポートが組み込まれ、Chrome Devtoolsでのデバッグサポートが改善されました。Opera23以降でもWebコンポーネントのネイティブサポートが組み込まれています。

ここで紹介するテクニックの多くは、少し手を加えることでWebコンポート念とをネイティブサポートしていないブラウザでも利用可能です。詳しくは[polyfill環境下でのデバッギング](#polyfills)を参照して下さい。

## HTMLインポートを調査する

**Elements**パネルでHTMLインポートの中身を見るには、DOMツリービューの`<link>`タグを開きます。インポートされた内容は入れ子になった`#document`ノードに表示されます。
To see the contents of an HTML import in the **Elements** panel, expand the `<link>` tag in the DOM tree view.

![HTML import link expanded in DevTools Elements panel](/images/debugging/html-import.png)

**Sources**パネルにインポートされたファイルを開くには、リンクのURLをクリックします。

![HTML import link with URL highlighted](/images/debugging/html-import-link.png)

スクリプトがファイルに分離されているエレメントでは、スクリプトファイルが**Sources**パネルのHTMLファイルと一緒に表示されます。

HTMLインポートされたインラインスクリプトにブレイクポイントを設定できます。

**注意:** 実際の運用環境では、HTMLインポートは[vulcanize](/articles/concatenating-web-components.html)を使って結合されるのが普通です。しかし、JavaScriptやCSSと違って、HTMLファイルにはソースマップ書式がありません。そのため、結合されたファイルを、結合前のファイルに対応づける方法がありません。結果として、デバッグ時には結合されていないファイルを扱うほうがはるかに用意です。

## カスタムエレメントを調査する

多くのカスタムエレメントにはShadowDOMツリーが含まれます。Polymerエレメントでは、`<polymer-element>`タグ内の最初の`<template>`タグに追加された全ての要素がエレメントのShadowDOMにコピーされます。DevToolsでは、Shadowツリーのルートが`#shadow-root`という名前のノードで表示されます。例えば、次のエレメントでは:

    <polymer-element name="my-element" noscript>
      <template>
        <div>This is my shadow DOM!</div>
      </template>
    </polymer-element>

    <my-element></my-element>

ChromeではDOMツリーは次のようになります:

![DevTools showing DOM for custom element](/images/debugging/custom-element.png)

テンプレートの中身は`#shadow-root`以下のShadowツリー内に表示されます。

ShadowDOMをサポートしていないブラウザでは、`#shadow-root`ノードはありません。Shadowツリーとして表示されるべきノードはカスタムエレメントの直接の子要素として表示されます。

カスタムエレメントの調査では、コンソールからプロパティやメソッドに直接アクセスできます。

    $0.fire('my-event');
    $0.myproperty


`$0`は[console variable](https://developer.chrome.com/devtools/docs/commandline-api#0-4)と呼ばれ、現在選択されているエレメントを指します。これはほとんどのブラウザでサポートされています。

ShadowDOMのないブラウザでは、エレメントのメソッドやプロパティにアクセスするのに`wrap`関数を使います:

    wrap($0).fire('my-event');
    wrap($0).myproperty

より詳しくは[Shadow DOM polyfill](#shadowdom)を参照して下さい。

## 未登録のエレメントを見つけ出す {#unregistered}

Polymerアプリケーションのデバッグ時にもっともよくある問題の一つが未登録のエレメントです。未登録のエレメントを引き起こすよくある問題は2つあります:

-   カスタムエレメントでHTMLインポートが間違っているか指定されていない。この場合、カスタムエレメントはDOMにShadowDOMを持たない通常のエレメントとして表示されます。エレメントは表示されるかもしれませんが、カスタムエレメントとしてのスタイル指定や振る舞いはありません。ページの他の部分は通常通り表示されます。

-   `Polymer`コンストラクタがないか、`Polymer`メソッドの呼び出しで間違ったタグ名を使っている。Polymerは全てのエレメントの定義が終わるのを待ってから、エレメントの登録を開始する設計になっています。_たとえ`Polymer`メソッドの呼び出しが非同期スクリプトを含んでいても_、これによって全てのエレメントは`polymer-ready`イベントが発行される前に登録済みであることが確実になります。しかし、`Polymer`メソッドの呼び出しがある一つのエレメントで見つからないと、全てのPolymerエレメントが登録されないことになります。`polymer-ready`イベントは発行されず、Polymerエレメントが全く表示されないため、多くの場合画面が真っ白になります。


例として、次に示すエレメントでのタグ名の不一致はPolymerのエレメント登録を妨げます:

    <polymer-element name="broken-element">
        <template>
           <p>Always do the right thing.</p>
        </template>
        <script>
           Polymer('wrong-name', { … });
         </script>
     </polymer-element>

未登録のエレメントを探す時間を減らす方法がいくつかあります。

### タグ名の不一致を避ける

可能な限りいつでも、`Polymer`コンストラクタからタグ名を省略しましょう。Polymer0.4.0以降では`<polymer-element>`タグ内で`<script>`タグから`Polymer`メソッドを呼び出す場合、タグ名を省略できます。タグ名の繰り返しをなくすことで、エラーの可能性を減らせます。

### 未登録エレメント発見用ブックマークレットを使う

エレメントが登録されているかどうかを素早くチェックするために、[Aleks Totic](https://twitter.com/atotic) と [Eric Bidelman](https://twitter.com/ebidel))によって書かれたこのブックマークレットを利用できます:

<a class="bookmarklet" href="javascript:(function(){function isUnregisteredCustomElement(el){if(el.constructor==HTMLElement){console.error('Found unregistered custom element:',el);return true;}return false;}function isCustomEl(el){return el.localName.indexOf('-')!=-1||el.getAttribute('is');}var allCustomElements=document.querySelectorAll('html /deep/ *');allCustomElements=Array.prototype.slice.call(allCustomElements).filter(function(el){return isCustomEl(el);});var foundSome=false;for(var i=0,el;el=allCustomElements[i];++i){if(isUnregisteredCustomElement(el)){foundSome=true;}}if(foundSome){alert('Oops: found one or more unregistered custom elements in use! Check the console.');}else{alert('Good: All custom elements are registered :)');}})();"><core-icon icon="bookmark"></core-icon> Unregistered Elements Bookmarklet</a>

このブックマークレットはカスタムエレメントと思われるものの、`HTMLElement`コンストラクタを持つエレメントを探しだします。エレメントの名前にダッシュが含まれているか、`is`属性を持っている場合、カスタムエレメントらしいと判断されます:

    <paper-button>Button</paper-button>
    <form is="ajax-form"></form>

この方法はPolymerAPIを利用しないので、Polymerを含むどのカスタムエレメントでも使えます。

ブックマークレットをブラウザに追加するには、この<a class="bookmarklet" href="javascript:(function(){function isUnregisteredCustomElement(el){if(el.constructor==HTMLElement){console.error('Found unregistered custom element:',el);return true;}return false;}function isCustomEl(el){return el.localName.indexOf('-')!=-1||el.getAttribute('is');}var allCustomElements=document.querySelectorAll('html /deep/ *');allCustomElements=Array.prototype.slice.call(allCustomElements).filter(function(el){return isCustomEl(el);});var foundSome=false;for(var i=0,el;el=allCustomElements[i];++i){if(isUnregisteredCustomElement(el)){foundSome=true;}}if(foundSome){alert('Oops: found one or more unregistered custom elements in use! Check the console.');}else{alert('Good: All custom elements are registered :)');}})();"><core-icon icon="bookmark"></core-icon> Unregistered Elements Bookmarklet</a>リンクをツールバーかお気に入りにドラッグ・アンド・ドロップしてください。


ブックマークレットのソースコードは
[https://gist.github.com/ebidel/cea24a0c4fdcda8f8af2](https://gist.github.com/ebidel/cea24a0c4fdcda8f8af2)にあります。


表示されているページのみ登録エレメントをチェックするにはブックマークレットをクリックします。ブックマークレットはページステータスを示すアラートを表示します。HTMLインポートがない場合にはより詳しい情報がコンソールに表示されます。

-   HTMLインポートがない場合には、ブックマークレットは該当エレメントをリスト表示します。

-   `Polymer`コンストラクタがない場合は、ブックマークレットは_全ての_Polymerエレメントを表示します。なぜなら全てのエレメントが未登録だからです。(次のセクションでは、`Polymer.waitingFor`という新しいメソッドをつかて問題を起こしているエレメントをピンポイントで特定する方法を紹介します)

**注意:** このブックマークはAngularディレクティブのように、名前にダッシュを含むものの、カスタムエレメントではないタグがあると、誤判定を起こします。
{: .alert .alert-info }

### waitingForメソッドとforceReadyメソッド

Polymer0.4.1で二つの新しいメソッドが追加されました。`Polymer.waitingFor`と`Polymer.forceReady`です。この二つのメソッドはエレメントの未登録問題を解決するのに役立ちます。`waitingFor`メソッドは対応する`Polymer`コンストラクタを持たない`<polymer-element>`タグのリストを返します。画面が真っ白にになる場合には、コンソールからこのメソッドを実行できます:

![DevTools console showing Polymer.waitingFor call and output](/images/debugging/waitingfor.png)

`waitingFor`がエレメント名ではなく、_エレメントのリスト_を返すことに注して下さい。

The `waitingFor` method does not report elements that are missing HTML imports, or misspelled tags.

`Polymer.forceReady` causes Polymer to stop waiting and immediately register any elements that are ready to register.

When debugging, you could add a script that logs unregistered elements, then forces the ready state, like this:

    window.logAndContinue = function() {
        var missing = Polymer.waitingFor();
        if (missing.length) {
          missing.forEach(function(el) {
            console.warn('Waiting for: ' + el.getAttribute('name'));
          });
          console.warn('Forcing element registration.');
          Polymer.forceReady();
        }
      }

If page loading stalls, you can invoke this from the console:

    logAndContinue();

      "Waiting for: broken-element"

      "Forcing element registration."

## Inspecting data-bound nodes

When working with [data binding](/docs/polymer/databinding.html), a lot of data is available if you know where to look. The examples in this section show DevTools, but with minor modifications, will work in Firefox,

DOM nodes generated by data binding appear immediately after the generating template.

In the case of nested templates, copies of the inner templates appear in the DOM before their generated content. For example, consider the following element:

    <polymer-element name="binding-test">
      <template>
        <template repeat="{%raw%}{{item in list}}{%endraw%}">
          <p>{{item.name}}:</p>
          <ul>
            <template repeat="{%raw%}{{field in item.fields}}{%endraw%}">
              <li>{{field}}</li>
            </template>
          </ul>
        </template>
      </template>
      <script>
        Polymer({
          created: function() {
            this.list = [
              {name: 'hits', fields: [1, 2, 3]},
              {name: 'misses', fields: [7, 0, 10]}
            ];
          }
        });
      </script>
    </polymer-element>

    <binding-test></binding-test>

This generates a DOM structure like the following:

![DevTools showing DOM structure](/images/debugging/bound-dom.png)

The numbers in the diagram identify elements of the DOM structure:

1.  Root of the element's shadow DOM tree.
2.  Outer `<template repeat>`.
3.  DOM nodes generated by the outer `<template repeat>`. Highlighted box shows the nodes representing the first item.
4.  Inner `<template repeat>`. Note that one copy appears in the DOM for each item in the outer `<template repeat>`.
5.  Generated DOM nodes from the inner `<template repeat>`.

To inspect the data bound to a template:

1.  In the **Elements** panel, select the template tag in the element's shadow root.
2.  In the **Console**, type:

        $0.model

    On browsers without native shadow DOM, add the `wrap` function:

        wrap($0).model

    For a discussion of the `wrap` function, see [Shadow DOM polyfill](#shadowdom).

![DevTools console showing $0.model command and output](/images/debugging/scope-object.png)

The `model` property returns the template's scope object, which contains all the identifiers that are in scope for the current template. For the outermost template, this is the custom element itself.

To inspect the data used to **create** a generated node:

1.  In the **Elements** panel, select the generated node.
2.  In the **Console**, type:

        $0.templateInstance.model


    On browsers without native shadow DOM, add the `wrap` function:

        wrap($0).templateInstance.model

![DevTools console](/images/debugging/templateinstance-model.png)

`templateInstance.model` returns the data used to generate the DOM nodes. In this case, it includes the `item` object corresponding to the first item in the list.


## Debugging under the polyfills

The following sections provide some hints for debugging Polymer elements on browsers that don't support web components natively.

### HTML Imports polyfill

The HTML Imports polyfill loads imports using `XMLHttpRequest`. External scripts inside an import are loaded normally. Inline scripts inside an import are transformed into data URLs. Each debugger displays these data URLs differently. For example, in Safari Web Inspector, look in the **Resources** tab for URLs starting with `data:text/javascript;`.

To debug a custom element from an HTML import, find and open the corresponding script file or data URL. You can then set breakpoints, watch expressions, and so forth.

### Shadow DOM polyfill {#shadowdom}

The Shadow DOM polyfill wraps each DOM node with a wrapper object. The wrapper exposes the common DOM methods and properties while emulating the mechanics of a browser that supports shadow DOM. All of a Polymer element's methods and properties are defined on the wrapper node. This leads to two potential issues:

The debugger typically returns references to the un-wrapped native DOM nodes, so you cannot invoke the Polymer methods directly from the Console.
Some rarely-used native DOM methods and properties aren't exposed on the wrapped object.

The Shadow DOM polyfill provides `wrap` and `unwrap` functions to transform between wrapped and unwrapped nodes.

For example, when examining a Polymer element, you can use `wrap` in the console to access Polymer properties and methods:

    wrap($0).fire('my-event');
    wrap($0).myproperty

Likewise, if you encounter a native DOM property or method that's not available on the wrapped node, you can access the native node using `unwrap`.


