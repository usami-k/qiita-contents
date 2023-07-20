---
title: Stack を使って Haskell をインストールする
tags:
  - Haskell
  - ghc
  - stack
private: false
updated_at: '2016-07-16T00:53:59+09:00'
id: fa7c1f14c5ce2a0bd9fc
organization_url_name: null
slide: false
---
Haskell 開発環境のインストールは、以前は Haskell Platform を使うのが定番でしたが、現在は [Stack](http://haskellstack.org/) を使うのが定番となっています。

この記事では、Stack をインストールした後で（Stack 自体のインストールはプラットフォームごとに異なりますので Stack のページを参照してください）、実際に Haskell 開発環境をどのように整えるか書きます。

# Stack を使って Haskell 開発環境をインストールする操作手順

Stack のインストール直後はまだ GHC が使えません。Stack で GHC のインストールを行うには次のようにします。

## 方法 1 : プロジェクトを新規作成して GHC を使う

Stack のページを見ると、まず `stack new` でプロジェクトを作成し、そのディレクトリに入って `stack setup` を実行せよ、と書いてあります。

その通りに実行すると、GHC がまだダウンロードされていなければダウンロードされ、インストールが行われます。インストールが終わると、プロジェクトディレクトリの内側で、`stack ghc` コマンドで GHC を使うことができるようになります。

## 方法 2 : プロジェクトを新規作成せずに GHC を使う

前述の方法ではプロジェクトを作成することが前提となっています。しかし、プロジェクトを作成せずに GHC を使うことも可能です。そのためには、プロジェクトディレクトリの外側で `stack setup` を実行します。

すると、同様に GHC のインストールが行われます。これで、どのディレクトリにいても `stack ghc` で GHC を使うことができるようになります。

# Haskell 開発環境がインストールされる場所

上記の方法で、どちらも `ghc` というコマンドが PATH が通った場所にインストールされたわけではありません。実際に GHC がインストールされるのは、`~/.stack` ディレクトリ以下です。これを `stack ghc` と、`stack` コマンド経由で実行します。

# プロジェクトを作成する場合としない場合の違い

`stack new` でプロジェクトを作成すると、そのプロジェクトで使う Haskell 開発環境のバージョンなどの情報が書かれた `stack.yaml` ファイルがプロジェクト内に生成されます。`stack setup` は、`stack.yaml` に記述された内容をもとに GHC のインストールを行います。

プロジェクトの外側で `stack setup` を実行した場合は、`~/.stack/global-project/stack.yaml` に記述された内容をもとに GHC のインストールを行います。つまり、個々のプロジェクトの設定の代わりに、グローバルプロジェクトという特殊なプロジェクトの設定が使われることになります。

# Haskell 開発環境のバージョン

最初に `stack.yaml` ファイルが生成されるときに、Haskell 開発環境のバージョンが `stack.yaml` に書かれます。`resolver: lts-5.18` などと書かれている行がそれです。`stack setup` は、そこに記載されたバージョンの Haskell 開発環境を使えるようにします。

Haskell 開発環境のバージョンを変更したい場合は、`stack.yaml` の `resolver` の行を書き換えてから `stack setup` を実行します。

グローバルプロジェクトを使う場合、最初に生成された `~/.stack/global-project/stack.yaml` の設定を使い続けることになります。そのため、グローバルプロジェクトで新しい Haskell 開発環境に更新したい場合は `~/.stack/global-project/stack.yaml` の `resolver` の行を書き換えてから `stack setup` を実行します。

`resolver` の行に記載するバージョンですが、最新バージョンは [LTS Haskell](https://www.stackage.org/lts) のページを見ると分かります。また、使用可能なバージョンの一覧は [lts-haskell: LTS Haskell build plans](https://github.com/fpco/lts-haskell) のページを見ると分かります。

# もっと簡単に GHC を呼び出したい

毎回 `stack ghc` というコマンドで GHC を使うのは少々面倒です。シェルでエイリアスを定義しておくのが楽かと思います。bash や zsh の場合は、`~/.bashrc` や `~/.zshrc` に以下のように定義しておくと良いです。

```
alias ghc="stack ghc --"
alias ghci="stack ghc -- --interactive"
alias runghc="stack runghc --"
```

# まとめ

Haskell 開発環境を整えるには Stack を使えばいいという話はあるものの、実際にどのように整えるのかがどうもはっきりしないと感じていたのでまとめました。ツッコミ歓迎です。

