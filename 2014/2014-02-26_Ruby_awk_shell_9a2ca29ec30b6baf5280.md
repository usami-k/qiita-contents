<!--
title:   テキストファイルの特定文字列の行から特定文字列の行までを抽出したい
tags:    Ruby,awk,shell
id:      9a2ca29ec30b6baf5280
private: false
-->
例えば、START という行から STOP という行までを抽出したいとき。

## awk を使う場合

シェルでこの手の作業をしようと思ったら、まず思いつくのが awk です。
実は簡単な記述法が用意されています。

```sh
cat file.txt | awk '/START/,/STOP/'
```

抽出した行をさらに加工したい場合は、awk で書くなら以下のような感じです。

```sh
cat file.txt | awk '/START/,/STOP/ { 何か加工処理 }'
```

しかし、sed と組み合わせるほうが自然な気がします。

```
cat file.txt | awk '/START/,/STOP/' | sed '何か加工処理'
```

## ruby を使う場合

上記のフィルタ処理を ruby で書くとこうなります。
こちらもわりと簡単な記述で書けます。

```sh
cat file.txt | ruby -ne 'print if /START/../STOP/'
```

抽出した行をさらに加工したい場合は、ruby スクリプトで書くなら以下のような感じです。

```ruby
File.open('file.txt').each do |line|
	next unless line =~ /START/ .. line =~ /STOP/
	何か加工処理
end
```