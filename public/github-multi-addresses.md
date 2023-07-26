---
title: GitHubで複数のコミットメールアドレスを使い分ける
tags:
  - Git
  - GitHub
  - gpg
  - GnuPG
private: false
updated_at: '2023-07-26T10:24:37+09:00'
id: f7a69baccd6a41734a6f
organization_url_name: yumemi
slide: false
---
GitHubでは、個人用と仕事用とで同じGitHubアカウントを使うことが推奨されています。たとえば、「[複数の個人アカウントのマージ - GitHub Docs](https://docs.github.com/ja/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-your-personal-account/merging-multiple-personal-accounts)」にヒントとして書かれています。

一方、Gitのコミットにはメールアドレスが記録されます。そのため、個人用のコミットには個人用のメールアドレスを使い、仕事用のコミットには仕事用のメールアドレスを使いたくなります。そこで、GitHubで複数のメールアドレスを使い分ける方法を紹介します。

:::note
なお、これらの内容はGitHubのドキュメントに書いてあります。この記事内からもリンクを張っています。
:::

# GitHubへのメールアドレスの追加

GitHubアカウントには、複数のメールアドレスを登録できます。

アカウントの「Settings」→「Emails」にある「Add email address」から、メールアドレスを追加できます。これは「[GitHub アカウントへのメールアドレスの追加 - GitHub Docs](https://docs.github.com/ja/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/adding-an-email-address-to-your-github-account)」に書いてあります。

こうしてメールアドレスを登録しておけば、そのメールアドレスを使ったコミットがGitHubのWeb上でアカウントと関連付けられます。

:::note
＜余談＞
なお、ひとつのメールアドレスはひとつのGitHubアカウントにのみ登録できます。メールアドレスを別のGitHubアカウントと関連付けたい場合は、そのメールアドレスをいったんGitHubアカウントから登録解除する必要があります。
:::

# Web操作時のコミットメールアドレスの設定

複数のメールアドレスを登録しておくと、GitHubのWebベースでのGitコミットの際に、どのメールアドレスを使って操作するかを選択できます。

ただしこのためには、「Settings」→「Emails」にある「Keep my email address private」をオフに設定しておく必要があります。

:::note
＜余談＞
なお、「Keep my email address private」は、登録したメールアドレスを直接使わないようにする設定です。これをオンにした場合、WebベースでのGit操作に対して「`ID+USERNAME@users.noreply.github.com`」という形式のメールアドレスが使われます。
:::

# Gitクライアントのコミットメールアドレスの設定

WebでなくローカルのGitクライアントからコミットする場合に、どのメールアドレスを使うかを設定できます。

そのパソコンでのGitコミットに使うデフォルトのメールアドレスを設定するには、次のようにします。この設定は、`~/.gitconfig`に保存されます。

```
git config --global user.email "YOUR_EMAIL"
```

また、特定のリポジトリでのGitコミットに使うメールアドレスを設定するには、次のようにします。この設定は、そのリポジトリの `.git/config`に保存されます。

```
git config user.email "YOUR_EMAIL"
```

これは「[コミットメールアドレスを設定する - GitHub Docs](https://docs.github.com/ja/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address)」に書いてあります。

:::note
リポジトリごとに設定を切り替える方法として、git configの `includeIf` を使う方法もあります。この機能はgit configのドキュメントの「[Conditional includes](https://git-scm.com/docs/git-config#_conditional_includes)」に記載されています。たとえば、特定のリモートリポジトリのURLにマッチしたら別の設定ファイルを読み込むように設定できます。

```
[includeIf "hasconfig:remote.*.url:git@github.com:mycompany/**"]
  path = ~/.gitconfig_mycompany
```
:::

# GnuPG署名の設定

GitコミットにはGnuPGによるデジタル署名ができます。これは「[GitのコミットにGnuPGで署名する - Qiita](https://qiita.com/usamik26/items/6b816db27b7661611d59)」という記事で紹介しています。このGnuPG署名もコミットメールアドレスと関連付けされています。

そのため、複数のメールアドレスを使い分ける場合にはそれらのメールアドレスに紐づいたGnuPGキーを用意する必要があります。対応方法は2通りあります。

- 複数のメールアドレスをひとつのGnuPGキーに紐づける
- 複数のメールアドレスに対してそれぞれGnuPGキーを作成する

前者のほうが、複数のGnuPGキーを扱わずにすむため管理しやすくなります。

## ひとつのGnuPGキーへの複数のメールアドレスの関連付け

複数のメールアドレスをひとつのGnuPGキーに紐づけるには、まずひとつのメールアドレスに対してGnuPGキーを作成します。次に、そのGnuPGキーに対してメールアドレスの関連付けを追加します。

関連付けの追加は `gpg --edit-key` コマンドで行います。この詳細は「[GPG キーとメールの関連付け - GitHub Docs](https://docs.github.com/ja/authentication/managing-commit-signature-verification/associating-an-email-with-your-gpg-key?platform=mac)」に書いてあります。

それ以外は「[GitのコミットにGnuPGで署名する - Qiita](https://qiita.com/usamik26/items/6b816db27b7661611d59)」で記載しているとおりです。

## 複数のGnuPGキーを扱う場合の設定

基本的には前述の方法が管理しやすいためオススメですが、複数のGnuPGキーを扱うパターンも紹介しておきます。

複数のメールアドレスに対してそれぞれGnuPGキーを作成するには、GnuPGキーの作成手順を必要な数だけ繰り返します。ひとつのGitHubアカウントには複数のGnuPGキーが登録できます。

そのパソコンでのGitコミットに使うデフォルトのGnuPG署名キーを設定するには、次のようにします。この設定は、`~/.gitconfig`に保存されます。

```
git config --global user.signingkey "YOUR_GPG_KEY_ID"
```

また、特定のリポジトリでのGitコミットに使うGnuPG署名キーを設定するには、次のようにします。この設定は、そのリポジトリの `.git/config`に保存されます。

```
git config user.signingkey "YOUR_GPG_KEY_ID"
```

これは「[Git へ署名キーを伝える - GitHub Docs](https://docs.github.com/ja/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)」に書いてあります。

# まとめ

以上の内容を設定すれば、GitHubで複数のメールアドレスを使い分けることができます。

これを利用して、個人用のコミットには個人用のメールアドレスを使い、仕事用のコミットには仕事用のメールアドレスを使うことができます。ぜひ活用してください。
