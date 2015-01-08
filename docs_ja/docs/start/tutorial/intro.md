---
layout: default
type: start
shortname: Start
title: プロジェクトを始めよう
subtitle: はじめてのPolymerアプリケーション
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
.unquote-link {
  max-width: 360px;
}
.unquote-image {
  background-image: url(/images/tutorial/finished.png);
  background-size: cover;
  background-position: top;
  width: 360px;
  height: 320px;
  border: 1px solid black;
}
</style>

このチュートリアルでは小さな{{site.project_title}}アプリケーションを作ります。基本的な機能を備えたSNSサービスのクライアントです。完成版は以下のようになります。

<figure layout vertical center>
  <a href="/apps/polymer-tutorial/finished/" layout horizontal class="unquote-link">
    <div class="unquote-image"></div>
  </a>
  <figcaption>
    Click screenshot for demo
  </figcaption>
</figure>

このプロジェクトを通じて、{{site.project_title}}を使う上で重要なコンセプトのほとんどをご紹介します。一度にすべてを理解できなくとも心配しないで下さい。ここで紹介するコンセプトは{{site.project_title}}のドキュメントで詳しく解説されています。

## 始める前に：プロジェクトのひな形をダウンロードする

まずはプロジェクトのひな形をダウンロードしましょう。ひな形にはこれから必要になる全ての{{site.project_title}}ライブラリと依存関係にあるライブラリが含まれています。

<p layout horizontal center-justified>
  <a href="https://github.com/Polymer/polymer-tutorial/archive/master.zip">
    <paper-button id="download-button" raised onclick="downloadStarter()"><core-icon icon="file-download"></core-icon>プロジェクトのひな形をダウンロードする</paper-button>
  </a>
</p>

ダウンロードしたファイルを解凍します。

ひな形には初期バージョンと、段階に応じたバージョンのプロジェクトが含まれています。作業を進めるに従って各バージョンと比較することができるようになっています。

チュートリアルを進めるにはページを表示するための基本的なHTTPサーバが必要になります。Pythonがインストールされていれば、プロジェクトのルートで以下のコマンドを実行して下さい。

Python 2.x:

    python -m SimpleHTTPServer

Python 3.x:

    python -m http.server

最終バージョンをみるには、次のようにします。

-  [http://localhost:8000/finished/](http://localhost:8000/finished/)

このチュートリアルではローカルサーバが8000番ポートで動いているものとします。別のポート番号でサーバを動かしている場合は適宜読み替えて下さい。

*注意:** WindowsではPythonのsimple HTTP サーバがSVG画像に対して適切なMIMEタイプを付加しないかもしれません。画像が表示されない場合は別のHTTPサーバを試して下さい。
{: .alert .alert-info }


<div horizontal layout end-justified class="stepnav">
<a href="/docs/start/tutorial/step-1.html">
  <paper-button raised><core-icon icon="arrow-forward"></core-icon>Step 1: アプリケーションの構造を作る</paper-button>
</a>
</div>

