---
title: 現在のブランチ名を取得するコマンド
tags:
  - Git
private: false
updated_at: '2012-10-10T19:36:34+09:00'
id: 8aa6c3aed4b9c7dba5ca
organization_url_name: null
slide: false
ignorePublish: false
---

以下のコマンドで、現在のブランチ名を表示できる。

```sh
git branch --contains=HEAD
```

branch コマンドの contains オプションは、指定したコミットを含むブランチのみを表示する。

コミット指定を省略すると HEAD が指定されたとみなすので、以下でも上記コマンドと同じ意味になる。

```sh
git branch --contains
```
