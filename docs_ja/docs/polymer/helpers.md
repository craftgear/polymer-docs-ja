---
layout: default
type: guide
shortname: Docs
title: Polymer ヘルパメソッド
subtitle: Guide
---

{% include toc.html %}

{{site.project_title}} ライブラリでは`Polymer`オブジェクトにヘルパメソッド一式を備えています。このヘルパメソッドは個々の{{site.project_title}}エレメントに限定されない追加の機能を提供します。例えば、動的なファイルのインポート、mixins、エレメントの登録制御などです。

## 動的なHTMLインポートを使う

`Polymer.import` メソッドはHTMLファイルを動的にインポートするのに使えます:


<pre>
Polymer.import(<var>urls</var>, <var>callback</var>);
</pre>

<var>urls</var> はインポートするHTMLファイルのURLを格納した配列です。HTMLのロードは非同期で行われます。{{site.project_title}}は全てのインポートが完了し、インポートされたカスタムエレメントがアップグレードされると<var>callback</var>を呼び出します。callbackは引数を取りません。

例えば、次のようなエレメントがあったとします:

`dynamic-element.html`:

    <link rel="import" href="components/polymer/polymer.html">
    <polymer-element name="dynamic-element" attributes="description">
      <template>
        <p>I'm {%raw%}{{description}}{%endraw%}, now!</p>
      </template>
      <script>
        Polymer({
          description: 'a custom element'
        });
      </script>
    </polymer-element>

次のようにしてこの要素を動的にロードできます:

`index.html`:

    <html>
      <head>
        <script src="components/webcomponentsjs/webcomponents.js"></script>
        <link rel="import" href="components/polymer/polymer.html">
      </head>
      <body>
        <dynamic-element>
          I'm just an unknown element.
        </dynamic-element>

        <button>Import</button>

        <script>
          var button = document.querySelector('button');
          button.addEventListener('click', function() {
            Polymer.import(['dynamic-element.html'], function() {
              document.querySelector('dynamic-element').description = 'a dynamic import';
            });
          });
        </script>
      </body>
    </html>

`index.html`内の`<dynamic-element>`は通常の`HTMLElement`としてパースされます。ボタンをクリックすると、インポートが実行され、`<dynamic-element>`はカスタムエレメントに変わります。 

`import`のコールバック関数はページロードの際に実行される`polymer-ready`イベントと同等のものとみなすことが出来ます。
コールバック関数が呼び出されると、新しくインポートされた全てのエレメントは利用可能になっています。

最初のページロードの際に、対応する`Polymer`の呼び出しがないエレメントが一つでもあると、_全ての_エレメントの登録を阻害します。その結果コールバック関数は呼び出されません。
エレメント登録時の問題分析については、[登録されていないエレメントを突き止める](/docs/polymer/debugging.html#unregistered) を参照して下さい。

**注意:** これに関連した機能として、{{site.project_title}} には`HTMLImports.whenReady(callback)`があります。
このコールバック関数は全てのインポートが終了した時点で呼び出されます。(`whenReady`が呼び出された_あと_にドキュメントに追加されたインポートは含みません)
{: .alert .alert-info }

## mixinsを使う

`Polymer.mixin` メソッドは一つまたは複数の_mixin_オブジェクトから、_target_へプロパティをコピーします:

<pre>
Polymer.mixin(<var>target</var>, <var>obj1</var> [, <var>obj2</var>, ..., <var>objN</var> ] )
</pre>

再利用可能な _mixin_ オブジェクトを作ることで、`mixin`を使って複数のカスタムエレメント間で機能を共有することが出来ます。

    var myMixin = {
      sharedMethod: function() {
        // ...
      }
    }

    <polymer-element name="my-element">
    <script>
      Polymer(Polymer.mixin({
        // my-element prototype
      }, myMixin));
    </script>
    </polymer-element>

`mixin`メソッドは、mixin対象オブジェクトの浅いコピーを作ります。ネストしたオブジェクトはマージしません。

mixin対象オブジェクトは通常のJavaScriptオブジェクトであるため、オブジェクトに対して可能なあらゆる操作を行うことが出来ます。例えば、別々のHTMLインポートで読み込まれるカスタムエレメントの間で機能を共有するには、mixinをHTMLインポート内に置きグローバル変数にアサインします:

`shared-mixin.html`:

    <script>
      window.sharedMixin = {
        // shared code
      };
    </script>

`client-element.html`:

    <link rel="import" href="shared-mixin.html">

    <polymer-element name="client-element">
    <script>
      Polymer(Polymer.mixin({
        // local prototype
      }, window.sharedMixin);
    </script>
    </polymer-element>

## エレメントの登録を強制的に行う

デフォルトでは、全てのDOM要素が読み込まれまで{{site.project_title}}はカスタムエレメントの登録とカスタムエレメントへの変換を行いません。
`Polymer`コンストラクタの呼び出しがなく、しかも`noscript`の指定もない`<polymer-element>`タグが一つでもあると、全てのカスタムエレメントの登録がブロックされます。

`Polymer.waitingFor`ヘルパは登録を妨げている全ての`<polymer-element>`タグの一覧を返します。

`Polymer.forceReady`は不完全なエレメントを無視して、全ての利用可能なエレメントを即座に登録するように{{site.project_title}}に指示します。

詳細と利用例については、[未登録のエレメントを突き止める](/docs/polymer/debugging.html#unregistered) を参照して下さい。
