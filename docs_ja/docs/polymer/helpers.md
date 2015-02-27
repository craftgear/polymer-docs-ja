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

You can use `mixin` to share functionality between custom elements, by creating reusable _mixin_ objects.

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

The `mixin` method makes a shallow copy of the mixin objects, and doesn't attempt to merge nested objects.

Since mixin objects are ordinary JavaScript objects, you can do anything with them that you'd do with an
ordinary object. For example, to share a mixin across multiple custom elements in separate HTML imports,
place the mixin in an HTML import and assign the mixin to a global variable:

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

## Forcing element registration

By default, {{site.project_title}} waits until all elements are ready before registering and upgrading them.
If one `<polymer-element>` tag is missing its corresponding `Polymer` call (and doesn't have the `noscript` attribute),
it blocks all elements from registering.

The `Polymer.waitingFor` helper returns a list of `<polymer-element>` tags that are blocking registration.

`Polymer.forceReady` notifies {{site.project_title}} to register all available elements immediately, ignoring any
incomplete elements.

For more details and example usage, see [Hunting down unregistered elements](/docs/polymer/debugging.html#unregistered).
