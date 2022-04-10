<!--
title:   evernote-editor のエディタに CotEditor を指定する
tags:    CotEditor,Evernote,Mac
id:      3cc1207d086b95f32914
private: false
-->
コマンドラインで Evernote のノート作成や編集が行える [evernote-editor](https://github.com/hpoydar/evernote-editor) というツールがあります。

導入は `gem install evernote-editor` で行うことができます。

ノート編集用のテキストエディタは、デフォルトでは vim になっていますが、CotEditor を使うようにできます。そのためには、テキストエディタを次のように指定します。

```
'open -W -n -F -a CotEditor'
```

初回起動時にエディタを聞かれるのでそこで指定するか、`.evned` ファイルに記載すればよいです。