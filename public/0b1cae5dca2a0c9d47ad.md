---
title: Swift＋WebAssembly
tags:
  - Swift
  - WebAssembly
  - wasm
private: false
updated_at: '2022-12-20T14:14:11+09:00'
id: 0b1cae5dca2a0c9d47ad
organization_url_name: yumemi
slide: false
ignorePublish: false
---
Swift言語は、アプリケーションやコマンドラインツールでの活用がよく知られており、サーバサイドでの活用もできます。実は、WebAssemblyを使うとWebフロンドエンドでの活用も可能になります。

# WebAssembly

[WebAssembly](https://webassembly.org/) は、Webブラウザ上で実行できる実行バイナリ形式です。

この実行バイナリは、Webブラウザに組み込まれた仮想マシン上で実行されます。WebAssemblyはモダンな主要Webブラウザ各種でサポートされています。WebAssemblyの活用事例として、Google Meet、Google Earth、Figma、eBay、などが知られています。

Webブラウザ上で動作するプログラムにはすでにJavaScriptがあります。しかしWebAssemblyは、JavaScriptのような特定のプログラミング言語ではなく汎用的な実行バイナリ形式であるため、さまざまな言語で実行バイナリが作成できます。

WebAssemblyバイナリの生成方法でよく知られているものは以下があります。

* WebAssemblyテキストを記述して生成する
* C/C++ソースコードからEmscriptenで生成する
* Rustソースコードからwasm-packで生成する
* AssemblyScriptソースコードから生成する

# SwiftWasm

SwiftでもWebAssemblyバイナリを生成できます。そのためには、[SwiftWasm](https://swiftwasm.org/) を使います。

SwiftWasmは、Swiftコンパイラに改変を加えてWebAssemblyバイナリが生成できるようにしています。将来的に、本家のSwiftコンパイラに取り込まれることが目標となっています。

SwiftWasmが提供するSwiftツールチェインをインストールすると、WebAssemblyバイナリが生成できます。公式ページをたどってツールチェインのインストールパッケージを入手しても良いのですが、実は次に述べるcartonを使ってインストールするのが簡単です。

# carton

[carton](https://carton.dev/) は、SwiftWasmによる開発をサポートするコマンドラインツールです。

Macの場合、cartonはHomebrewでインストールできます。

いくつかの機能を持つツールですが、たとえば以下の機能があります（バージョン0.17.0現在）。

* `carton sdk` : SwiftWasmのツールチェインをインストールします。
* `carton init` : Swiftパッケージを新規作成します。
* `carton dev` : SwiftパッケージをビルドしてWebAssemblyバイナリを生成します。そして、HTTPサーバを起動します。
* `carton bundle` : SwiftパッケージをビルドしてWebAssemblyバイナリを生成します。そして、デプロイに必要なリソースをまとめます。

ビルドコマンドが2種類ありますが、`carton dev` が開発用途のビルド、`carton bundle` がリリース用途のビルドです。

# 簡単なWebAssemblyバイナリの作成と実行

試しに、Swiftパッケージを使わずに簡単なWebAssemblyバイナリを作成してみます。

そのためにまず、cartonでSwiftWasmのツールチェインをインストールします。

```sh
carton sdk install
```

このときのインストール先は `$HOME/Library/Developer/Toolchains` 以下となります。

インストールしたツールチェインを使ってWebAssemblyバイナリを生成します。

```sh
echo 'print("Hello, world!")' > hello.swift
~/Library/Developer/Toolchains/swift-latest.xctoolchain/usr/bin/swiftc -target wasm32-unknown-wasi hello.swift -o hello.wasm
```

WebAssemblyバイナリを実行します。通常はWebブラウザの仮想マシン上で実行する必要がありますが、ここでは簡単な実行手段として [wasmer](https://wasmer.io/) を使います。cartonインストール時にwasmerも一緒にインストールされています。

ターミナルで以下を実行すると、`Hello, world!` が標準出力に表示されます。

```sh
wasmer hello.wasm
```

# cartonによるWebAssemblyバイナリの作成

WebAssemblyバイナリを作成するためのSwiftパッケージを、cartonを使って作成してみます。

```sh
mkdir hello-carton
cd hello-carton
carton init
```

次のようなファイルが生成されます。通常のSwiftパッケージと同様です。

```
.
├── Package.swift
├── README.md
├── Sources
│   └── hello-carton
│       └── hello_carton.swift
└── Tests
    └── hello-cartonTests
        └── hello_cartonTests.swift
```

`hello_carton.swift` の内容は次のようになっています。

```swift
@main
public struct hello_carton {
    public private(set) var text = "Hello, World!"

    public static func main() {
        print(hello_carton().text)
    }
}
```

ビルドしてみます。なお、まだSwiftWasmのツールチェインをインストールしていなければ、`carton dev` がツールチェインのインストールも実行してくれます。このため、前述の `carton sdk install` は実行していなくても大丈夫です。

```sh
carton dev
```

`carton dev` は、SwiftパッケージをビルドしてWebAssemblyバイナリを生成します。そして、HTTPサーバを起動し、そのサーバにアクセスするWebブラウザを起動します。

Webブラウザの開発コンソールを見ると、`Hello, world!` が出力されていることがわかります。

また、cartonはSwiftソースコードの変更を監視しており、ソースコードに変更があるとすぐにリビルドされてHTTPサーバに反映されます。

# JavaScriptの呼び出し

WebAssemblyからJavaScriptの機能を呼び出すことができます。

SwiftWasmの場合、SwiftコードでJavaScriptKitを使います。前述のSwiftパッケージでは、すでにJavaScriptKitがdependenciesに追加されており、すぐに使えるようになっています。`hello_carton.swift` を書き換えてみます。

```swift
import JavaScriptKit // <- 追加

@main
public struct hello_carton {
    public private(set) var text = "Hello, World!"

    public static func main() {
        let alert = JSObject.global.alert! // <- 追加
        _ = alert(hello_carton().text) // <- 変更
    }
}
```

`import JavaScriptKit` を追加して、`main` 関数でテキストを `print` する代わりにテキストを `alert` で表示するようにしました。

`carton dev` を実行したままになっていれば、前述のように、ソースコードを変更するとすぐにリビルドされてHTTPサーバに反映されます。

無事に動作すれば、Webブラウザ上でアラートダイアログに　`Hello, world!` が表示されます。

# TokamakによるUIの作成

SwiftWasmでは、[Tokamak](https://tokamak.dev/) というUIフレームワークが利用できます。これにより、WebのUIもSwiftで書けます。

Tokamakを使ったWebフロントエンドアプリのSwiftパッケージが、cartonを使って作成できます。

```sh
mkdir TokamakApp
cd TokamakApp
carton init --template tokamak
```

生成された `App.swift` の内容は次のようになっています。

```swift
import TokamakDOM

@main
struct TokamakApp: App {
    var body: some Scene {
        WindowGroup("Tokamak App") {
            ContentView()
        }
    }
}

struct ContentView: View {
    var body: some View {
        Text("Hello, world!")
    }
}
```

TokamakはSwiftUIと同様の記法で書けます。

`carton dev` を実行すると、Webブラウザ上に `Hello, world!` が表示されます。

# さらなる情報

SwiftWasmについて簡単に紹介しました。より詳しい情報は、以下を参照してください。

* SwiftWasmの [Documentation](https://book.swiftwasm.org/)
* [swiftwasm/awesome-swiftwasm](https://github.com/swiftwasm/awesome-swiftwasm)
