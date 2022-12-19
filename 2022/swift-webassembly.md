<!--
title:   Swift＋WebAssembly
tags:    Swift,WebAssembly,wasm
id:      0b1cae5dca2a0c9d47ad
private: true
-->


Swift言語は、アプリケーションやコマンドラインツールでの活用がよく知られており、サーバサイドでの活用もあります。実は、WebAssemblyを使うとWebフロンドエンドでの活用も可能になります。

# WebAssemblyとは

WebAssemblyは、Webブラウザー上で実行できるバイナリ形式です。ブラウザーに組み込まれた仮想マシン上で実行されます。

ブラウザー上で動作するプログラムにはすでにJavaScriptがありますが、WebAssemblyはより高速に動作します。また、WebAssemblyは汎用的なバイナリ形式であるため、さまざまな言語で実行バイナリが作成できます。

現在のWebAssemblyはJavaScriptでできることすべてをカバーしているわけではないので、JavaScriptと併用して活用するのが一般的なやり方です。JavaScriptからWebAssemblyの関数を呼び出したり、WebAssemblyからJavaScriptの関数を呼び出したりできます。

WebAssemblyの活用事例として、Google Meet、Google Earth、Figma、eBay、などが知られています。

WebAssemblyバイナリの生成方法でよく知られているものは以下があります。

* WebAssemblyテキストを記述して生成する
* C/C++ソースコードからEmscriptenで生成する
* Rustソースコードからwasm-packで生成する
* AssemblyScriptソースコードから生成する