---
layout: default
type: start
shortname: Start
title: コードを入手する
---

<style>
#download-button {
  background: #4285f4;
  color: #fff;
  font-size: 18px;
  fill: #fff;
}
#download-button:hover {
  background: #2a56c6;
}
#download-button::shadow paper-ripple {
  color: #fff;
}
</style>


{% include toc.html %}

## {{site.project_title}}のインストール {#installing-polymer}

{{site.project_title}} をインストールする方法はいくつかあります。

*   Bowerを使う。 **推奨**. Bowerはコンポーネント間の依存関係を管理してくれるので、コンポーネントをインストールする際に必要となる物を一緒にインストールしてくれます。Bowerはまたインストールされたコンポーネントの更新も行います。より詳しい情報は、[Bowerを使ってインストールする](#using-bower)を参照して下さい。

*   ZIPファイルをダウンロードする。 ZIPファイルには必要なものが全て含まれているので、解凍してすぐに使いはじめることができます。この方法は特別なツールを必要としません。しかし、コンポーネントの更新を管理する方法はありません。より詳しい情報は、[ZIPファイルからインストールする](#using-zip)を参照して下さい。

*   GitHubからインストールする。 GitHubからコンポーネントをcloneした場合、コンポーネント間の依存関係は全て自分で解決する必要があります。{{site.project_title}}を自分でハックしたい、あるいはプルリクエストを送信したい場合は、[gitを使って{{site.project_title}}をセットアップする](/resources/tooling-strategy.html#git)を参照して下さい。

BowerかZIPで {{site.project_title}} をインストールした場合、[Web Components polyfill library](/docs/start/platform.html)がインストールされています。このライブラリを使うことで、Web Componentsの仕様をネイティブサポートしていないブラウザでも {{site.project_title}} を使うことが可能になります。

**注意:** CoreエレメントとPaperエレメントのインストールについての情報は、[エレメントを使う](/docs/start/using-elements.html)を参照して下さい。

**注意:** PolymerLabsのGitHubリポジトリは、たくさんのサポートされていないエレメントを含んでいます。それらは実験的なものであったり、廃止予定のものであったりします。特に、`polymer-elements` と `polymer-ui-elements` は以前の実装であり、現在はCoreエレメントとPaperエレメントに置き換えられました。

## Bowerをつかって {#using-bower}をインストールする

**{{site.project_title}} {{site.latest_version}}の推奨インストール方法はBowerを使うことです。
Bowerをインストールするには、[Bowerのウェブサイト](http://bower.io/)を参照して下さい。

エレメントを開発したり利用したりする際に、Bowerは依存関係を管理する手間を省きます。コンポーネントをインストールすると、Bowerが必要なコンポーネントもインストールします。

### プロジェクトのセットアップ

まだ`bower.json`ファイルを作っていなければ、プロジェクトディレクトリ直下で次のコマンドを実行して下さい:

    bower init

これで素の`bower.json`ファイルが作られます。途中で聞かれる質問のうちいくつか、たとえば、"What kind of modules do you expose," といった質問はEnterキーを押すことでスキップできます。

{{site.project_title}}をインストールする次のステップは以下のコマンドを実行することです:

    bower install --save Polymer/polymer

Bowerは`bower_components/` フォルダをプロジェクトディレクトリ直下に追加し、その中に{{site.project_title}}とその関連ファイルを配置します。

**ヒント:** `--save` オプションはインストールするコンポーネントを`bower.json`の`dependencies`に追加します。:
```
{
  "name": "my-project",
  "version": "0.0.0",
  "dependencies": {
    "polymer": "Polymer/polymer#~{{site.latest_version}}"
  }
}
```
{: .alert .alert-success }

#### パッケージの更新 {#updatebower}

新しいバージョンの{{site.project_title}}が利用可能になったら、`bower update`を実行することで、インストールされた{{site.project_title}}を更新できます。:

    bower update

このコマンドは`bower_components/`にあるすべてのパッケージを更新します。

## ZIPファイルからインストールする {#using-zip}

{{site.project_title}}をZIPファイルとしてダウンロードするには、下の **GET POLYMER** ボタンをクリックし、次に **Download ZIP**ボタンをクリックして下さい。

<component-download-button org="Polymer" component="polymer" label="GET POLYMER">
</component-download-button>

{{site.project_title}}をZIPファイルとしてダウンロードした場合、必要なファイルはすべてアーカイブ内に含まれています。他に何もツールが必要ないので、始めるには良い方法です。

`bower_components`フォルダを作るにはプロジェクトディレクトリ直下でZIPファイルを解凍して下さい。

![](/images/zip-file-contents.png)

複数のコンポーネントを別々のZIPファイルとしてダウンロードした場合、依存関係にあるファイルのコピーを複数持つことになります。その場合、ZIPファイルの中身をまとめる必要があります。

Bowerとは異なり、ZIPファイルでのインストールには依存関係にあるファイルを更新するツールは用意されていません。コンポーネントをZIPファイルでダウンロードし、手動で更新する必要があります。

## gitを使って{{site.project_title}}をセットアップする {#git}

たくさんの依存関係があるため、{{site.project_title}}のインストールにはgitではなくBowerをおすすめしています。{{site.project_title}}をハックしたい場合や、プルリクエストを送りたい場合は、[setting up {{site.project_title}} with git](/resources/tooling-strategy.html#git)にあるガイドを参照して下さい。

## 次のステップ {#nextsteps}

さて、{{site.project_title}}のインストールが完了しました。これで基本を学ぶ準備はできています。次のセクションでは{{site.project_title}}を使って、エレメントを作る方法をご紹介します。
引き続き、

<a href="/docs/start/creatingelements.html">
  <paper-button raised><core-icon icon="arrow-forward" ></core-icon>10分でわかるPolymer</paper-button>
</a>をお読み下さい。

さらに先に進みたい場合は、[tutorial](/docs/start/tutorial/intro.html)をご覧いただくか、[APIデベロッパーガイド](/docs/polymer/polymer.html)をご覧ください。
