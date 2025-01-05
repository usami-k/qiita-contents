---
title: AIをプログラミングに活用するためのIDEやツール
tags:
  - Xcode
  - JetBrains
  - AI
  - VisualStudioCode
  - cursor
private: false
updated_at: '2025-01-05T09:52:26+09:00'
id: 68518c4f023d49d317af
organization_url_name: yumemi
slide: false
ignorePublish: false
---

プログラミングのときにAIアシスタントの助けを借りることは、有益な場合が多いです。この記事では、現時点（2025年1月5日）で私が実際に使っているAIアシスタントを紹介します。あくまで現時点での情報ですので、今後変化しうるという点にはご注意ください。

## Cursor

https://www.cursor.com/

Cursorは、あらかじめAIアシスタントが統合されている汎用IDEです。Visual Studio Codeがベースになっているため、さまざまな拡張機能が利用できます。

CursorのAIアシスタントはとても多機能です。Composer（対話的な編集機能）が便利で、実装コードの追加や変更をいろいろと指示できます。さらに、それらの作業を連続して行うAgentという機能もあります。AIに追加で学習してもらうDocs参照の機能も有益です。それ以外にも、チャット機能、コミットメッセージ生成機能、Cmd KやTabによる編集、などがあります。

Cursorは無料でも利用可能です。ただし、リクエスト数に制限があります。その制限を超えて使いたい場合はサブスクリプション購入が必要です。Proプランが年額192ドルです。

## JetBrains AI

https://www.jetbrains.com/ja-jp/ai/

JetBrains製の各種IDEに、AIアシスタントを統合できます。

JetBrains AIアシスタントも多機能です。対話的な編集機能、チャット機能、コミットメッセージ生成機能、入力補完機能、などがあります。JetBrainsは言語ごとにIDEがわかれていますが、そのぶん言語ごとの環境が整えやすく、AIアシスタントがその言語のことを分かっている感じがあります。

JetBrains AIは、JetBrains IDE自体とは別にサブスクリプション購入が必要です。日本向けの価格設定があり年額14,000円（約90ドル）です。なお、米国向けの価格設定は年額100ドルです。

## GitHub Copilot + Visual Studio Code

https://docs.github.com/ja/copilot/quickstart?tool=vscode

Visual Studio CodeにはさまざまなAIアシスタントを追加でき、GitHub Copilotもそのひとつです。

この組み合わせは、登場当初に比べて機能が増えてきています。対話的な編集機能、チャット機能、コミットメッセージ生成機能、入力補完機能、などがあります。Visual Studio Code自体が拡張による機能追加を得意とするIDEで、AIアシスタントも同じような感覚で追加できます。

GitHub Copilotはサブスクリプション購入が必要です。Proプランが年額100ドルです。

## ChatGPT + Xcode

https://help.openai.com/en/articles/10119604-work-with-apps-on-macos

ChatGPTのMacアプリ版は、Xcodeのコンテンツを読み取れます。これを利用して、ChatGPTをXcodeの簡易的なAIアシスタントのように利用できます。

この利用パターンでは、IDEに統合されている他のAIアシスタントに比べると、できることが限定的にはなります。それでもチャットは便利で、開いているXcodeプロジェクト内のファイルを読んで回答をくれます。

ChatGPTのサービス自体は無料でも利用可能ですが、Macアプリ版を使ってXcodeとの連携をするにはサブスクリプション購入が必要です。Plusプランが月額20ドルです（年額プランはありません）。

## サブスクリプション料金

それぞれのAIアシスタントのサブスクリプション料金を、年額で支払う想定としてまとめました。なお、個人向けのプランで記載しているので、業務向けではまた異なってきます。

| サービス | プラン | 年額 |
|:----|:----|----:|
| Cursor | Pro | 192ドル |
| JetBrains AI | Pro | 14,000円（約90ドル） |
| GitHub Copilot | Pro | 100ドル |
| ChatGPT | Plus | 240ドル |

## 個人的な利用状況

私の本業であるiOSアプリ開発ではXcodeを使っています。また、Cursorも業務で使用しています。私用のプログラミングでは、XcodeまたはJetBrains IDEを活用しています。JetBrainsがサポートしていない範囲ではVisual Studio Codeを使っています。

そんなわけで、機能が重複しているので4つ全部は不要とも思えますが、結局この記事で取り上げた4つのAIアシスタントは常用しています。Cursorは会社ライセンスを、それ以外は個人ライセンスを使っていて、個人では年額で約430ドル（約67,000円）のコストがかかっています。これをどう考えるかは人によるでしょうが、私は利便性を考えれば問題ない範囲のコストかなと考えています。

なお、ChatGPTにはPlusプランより高機能なProプランもあります。ただ、個人的にこの用途ではChatGPT o1やo1 proを使う必要を感じないため、Plusプランを使っています。

また、GitHub Copilot for Xcodeも存在しています。ただ、現状は機能が少なくて、個人的に導入するメリットが薄いと感じるため、現時点では使っていません。

ちなみに、これ以外のAI関連のツールとしては、Perplexity、Arc、Warpも使っています。

## 今後

使っているというほど使えていないので紹介しまませんでしたが、Gemini + Android Studioの組み合わせも良さそうです。

iOSアプリ開発者としてSwift Assistに期待していますが、現時点ではリリースされていないようです（2024年中と言っていたはずだけど）。リリースされたら試したいですね。
