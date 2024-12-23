---
title: SweetPadを使ってVisual Studio CodeやCursorでiOSアプリ開発を行う
tags:
  - iOS
  - Xcode
  - VisualStudioCode
  - cursor
private: false
updated_at: ''
id: null
organization_url_name: yumemi
slide: false
ignorePublish: false
---

SweetPadは、Visual Studio CodeやCursorでのiOSアプリ開発をサポートする拡張機能です。

https://sweetpad.hyzyla.dev/

一般には、iOS向けのネイティブアプリ開発はXcodeで行います。今回取り上げるSweetPadは、Visual Studio CodeやCursor上でXcodeプロジェクトをビルドして実行できるようにします。これはXcode CLIや各種の外部ツールを利用して実現されています。

以下、Visual Studio CodeをVSCodeと表記します。なお、この記事でVSCodeとして記載している箇所は、Cursorに置き換えても同様に利用できます。

## 前提条件

SweetPadはMac上でのみ使用できます。SweetPadはVSCodeの拡張機能ではありますが、LinuxやWindowsでiOSアプリ開発ができるようになるわけではありませんのでご注意ください。

また、Xcodeをインストールしておく必要があります。ほかにも、使いたい機能に応じて各種の外部ツールをインストールする必要があります。どの外部ツールが必要かは機能ごとに後述します。

各種の外部ツールをインストールするために、SweetPadではHomebrewによる外部ツールのインストール機能を提供しています。ただ、Homebrewは必須というわけではありません。

## SweetPadのインストール

SweetPad拡張機能をMarketplaceからVSCodeにインストールします。

https://marketplace.visualstudio.com/items?itemName=sweetpad.sweetpad

このとき、SweetPadからの依存関係でCodeLLDB拡張機能もインストールされます。なお、VSCodeのMarketplaceにはSwift拡張機能も存在しています。SweetPadからの依存関係ではSwift拡張機能はインストールされませんが、後述の自動補完の機能では必要になります。

SweetPad拡張機能のインストールは、VSCodeの左側のサイドバーにSweetPadが表示されていれば無事にできています。SweetPadのパネルは、「Build」「Destinations」「Tools」の3つに分かれています。

![SweetPadのパネル](https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/2024-vscode-sweetpad/panels.jpg)

## 外部ツールのインストール

SweetPadの「Tools」パネルでは、各種の外部ツールが簡単にインストールできるようになっています。インストールボタンを押すとHomebrewでインストールしてくれます。

https://sweetpad.hyzyla.dev/docs/tools

外部ツールのインストールにHomebrewを使いたくない場合は、この「Tools」パネルを使わずに自分でインストールしても構いません（たとえばMintでインストールしても良いです）。ただしその場合は、VSCodeから見える場所にツールを置くか、SweetPadの設定でツールの場所を指定するか、何かしらの対応が必要になってきます。

## Xcodeプロジェクトの作成

Xcodeプロジェクトの作成は、SweetPad上ではなくXcodeで行います。

Xcode 16以降では、Xcodeプロジェクトの形式が改善されてXcodeプロジェクト内のファイルがフォルダ管理になっています。そのため、プロジェクトに対するファイルの追加や削除をSweetPad上で問題なく行えます。

逆にいえばXcode 15まではフォルダ管理ではなかったため、古いプロジェクト形式だと、ファイルの追加や削除をSweetPad上で行ったときXcodeプロジェクトに正しく反映されません。Xcode 16以降の新しいプロジェクト形式を使うことをすすめます。

別の選択肢として、Xcodeを使う代わりに外部ツールでプロジェクト生成する方法もあります。XcodeGenとTuistがSweetPadでサポートされています。好みに応じて利用すると良いでしょう。`xcodegen` や `tuist` はSweetPadの「Tools」パネルでインストール可能です。XcodeGenやTuistの設定ファイルは別途作成する必要がありますが、準備しておけばSweetPad上でXcodeプロジェクトの生成が行えます。

https://sweetpad.hyzyla.dev/docs/tuist

## ビルドと実行

まず、XcodeプロジェクトがあるルートフォルダをVSCodeで開きます。

SweetPadの「Build」パネルでビルドと実行が行えます。この機能にはXcode CLIの `xcodebuild` を利用しています。Xcodeがインストールしてあれば問題ありません。

必須ではありませんが、`xcbeautify` ツールをインストールしておくとビルドログが見やすくなって便利です。`xcbeautify` は「Tools」パネルでインストール可能です。

なお、VSCodeのタスク機能（`tasks.json`）でもビルドを実行できます。また、SweetPadの設定で追加のビルド設定も可能です。

https://sweetpad.hyzyla.dev/docs/build

また、テストの実行も可能です。

https://sweetpad.hyzyla.dev/docs/tests

## アプリ実行端末

SweetPadの「Destinations」パネルでアプリを実行する端末を指定できます。シミュレータと実機の両方が扱えます。この機能にはXcode CLIの `xcrun` を利用しています。Xcodeがインストールしてあれば問題ありません。

VSCodeの下側のツールバーでも、実行端末の指定を変更できます。

https://sweetpad.hyzyla.dev/docs/destinations

## デバッグ

デバッグは、VSCodeのサイドバーにある「実行とデバッグ」で行えます。この機能にはSweetPadからの依存関係でインストールされているCodeLLDB拡張を利用しています。

準備として、VSCodeの「実行とデバッグ」サイドバーの中にある「launch.jsonファイルを作成」をクリックすると、SweetPadが `launch.json` を生成してくれます。

その準備をしたら、「実行とデバッグ」の中でデバッグ実行が行えます。ブレークポイントなども利用できます。

https://sweetpad.hyzyla.dev/docs/debug

## コードの自動補完

Xcodeと連携してSwiftコードの自動補完をするには、次の2つが必要です。

- [Swift拡張機能](https://marketplace.visualstudio.com/items?itemName=sswg.swift-lang)
- [xcode-build-server](https://github.com/SolaWing/xcode-build-server)

Swift拡張機能はMarketplaceからVSCodeにインストールします。`xcode-build-server` はSweetPadの「Tools」パネルでインストール可能です。

これらの準備をしたら、VSCodeのコマンドパレットから「SweetPad: Generate Build Server Config」を実行して、`buildServer.json` を生成します。その後Xcodeプロジェクトをビルドすると、コードの自動補完が動くようになります。

https://sweetpad.hyzyla.dev/docs/autocomplete

補足として、仕組みについて簡単に述べます。VSCodeの自動補完機能にはLSP（Language Server Protocol）が利用されます。Swift向けのLSPの実装はSourceKit-LSPで、Swift拡張機能はSourceKit-LSPを使ったLSPクライアントとして動作します。SourceKit-LSPはXcodeプロジェクトを直接はサポートしていませんが、xcode-build-serverがXcodeとSourceKit-LSPを連携させるLSPサーバーとなります。そのおかげで、Xcodeプロジェクトでも自動補完機能が使用可能になります。

## コードの整形

Swiftコードのフォーマット機能は、SweetPad拡張とSwift拡張の両方が提供しています。そのため、両方の拡張がインストールされている場合にはどちらを使用するかをVSCodeの設定で指定する必要があります。SweetPad拡張のフォーマット機能を有効にするには、VSCodeで次のように設定します。

```json
{
    "[swift]": {
        "editor.defaultFormatter": "sweetpad.sweetpad"
    }
}
```

SweetPadのフォーマット機能は `swift-format` を利用しています。`swift-format` はSweetPadの「Tools」パネルでインストール可能です。

これらの準備ができたら、コードの整形が動くようになります。VSCodeのFormat DocumentコマンドやFormat On Save機能が使えます。

https://sweetpad.hyzyla.dev/docs/format

なお、Xcode 16以降では `swift-format` がXcodeに内蔵されていますので、それを使っても良いです。その場合は、SweetPadの設定でパスを指定する必要があります。

```json
{
    "sweetpad.format.path": "/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-format"
}
```

## おわりに

SweetPad拡張機能は既にある程度iOSアプリ開発ができるだけの機能を備えています。一方で、まだまだ開発中でもあり、頻繁にバージョンアップしています。

「Tools」に含まれているSwiftLintやios-deployは、単にインストール可能なだけでSweetPadの中で利用する機能はまだ用意されていません。しかし、いずれサポートされるのではないかと期待しています。
