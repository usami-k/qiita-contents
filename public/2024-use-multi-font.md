---
title: IDEやテキストエディターで複数のフォントを使う
tags:
  - 開発環境
  - font
  - エディタ
private: false
updated_at: ''
id: null
organization_url_name: yumemi
slide: false
ignorePublish: false
---
# 複数のフォントの活用

IDE（統合開発環境）やテキストエディターのなかには、複数のフォントを使えるものがあります。この機能によって、問題を解決したり快適な環境を作ったりできます。その活用事例を紹介します。

# Xcodeでのフォントの設定

[Xcode](https://developer.apple.com/xcode/)は、iOSアプリやmacOSアプリを開発するための、macOS用のIDEです。

Xcodeはエディターで日本語を使うと少し問題が起きます。日本語に対応していないフォントを使っていると、日本語が含まれない行と含まれる行で行の高さが異なってしまうのです。実際、デフォルトの設定では日本語に対応していないフォントになっており、次のようになります。

![](https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/2024-use-multi-font/xcode-before.png)

日本語が含まれる箇所だけ行の高さが変わるため、行によって高さが変わってしまっています。これを避けるためには、日本語に対応したフォントを使う必要があります。

では、日本語に対応していないフォントを使いたい場合はこの問題には対処できないのでしょうか。実は対処法があります。

Xcodeはシンタックスハイライトの設定で、単に色やスタイルを変更するだけでなく、フォント自体を変更できます。実際デフォルトの設定では、Plain Textのフォントには「SF Mono」、Documentation Markupには「Helvetica Neue」と異なるフォントが設定されています。

![](https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/2024-use-multi-font/xcode-font.png)

この機能を利用して、日本語がよく使われる箇所だけ日本語に対応したフォントを使うように設定できます。

日本語がよく使われる箇所は、Comments、Documentation Markup、Stringsなどが考えられます。ここでは、Plain Textなどの箇所のフォントは日本語非対応の「0xProto」、Commentsなどの箇所のフォントは日本語対応の「UDEV Gothic 35JPDOC」を使うことにします。その設定をすると、次のようになります。

![](https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/2024-use-multi-font/xcode-after.png)

どの行もほとんど同じ高さになりました。この対処法で、好きなフォントを使いながら、日本語の表示も改善できます。

# CotEditorでのフォントの設定

[CotEditor](https://coteditor.com)はmacOS用のプレーンテキストエディターです。

CotEditorでは、ファイルの種類によって使うフォントを変える設定ができます。この機能を活用するとテキスト編集がより快適になります。

![](https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/2024-use-multi-font/coteditor-font.png)

「優先するフォント」の設定を「自動」にすると、文書のシンタックスの種類が「標準」の場合（Markdownなど）は標準フォントが使われ、種類が「コード」の場合は等幅フォントが使われます。

この機能を利用して、Markdownなどの文書を開く場合は日本語が読みやすいフォントを使い、設定ファイルやソースコードを開く場合はコードが読みやすいフォントを使うように設定できます。

なお、CotEditorでは、Xcodeのような行の高さの問題は起こりません。そのため、日本語に対応していないフォントを使って日本語を表示しても問題は起こりません。

# 参考：フォントの紹介

この記事で使用したフォント「0xProto」「UDEV Gothic」については、次の記事に紹介があります。

https://qiita.com/usamik26/items/2ab0c4b88bf7c00845dd

https://qiita.com/macneko/items/c2e0400f537af83ca4a9

# 情報募集

この記事では、次のIDEやテキストエディターを取り上げました。

* [Xcode](https://developer.apple.com/xcode/) : シンタックスハイライトの設定で複数のフォントが使える
* [CotEditor](https://coteditor.com) : ファイルの種類によって異なるフォントが使える

ほかに複数のフォントが使えるIDEやテキストエディターがあれば、ぜひ教えてください。
