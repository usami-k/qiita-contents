<!--
title:   Swift Package Managerのプラグイン機能
tags:    Swift,SwiftPM,SwiftPackageManager,Xcode
id:      1c2cec0903fea2e03344
private: false
-->

2022年3月にSwift 5.6がリリースされました。Swift 5.6で、Swift Package Manager（以下、SwiftPMと呼びます）に対してプラグイン機能が追加されました。このプラグイン機能について解説します。

# SwiftPMのプラグイン機能

SwiftPMにはこれまでプラグインの機能はありませんでした。つまり、今回のSwift 5.6で新規に追加されたものです。

プラグイン機能について、SwiftPM 5.6の [Release Notes](https://github.com/apple/swift-package-manager/blob/main/Documentation/ReleaseNotes/5.6.md) に簡単な説明が書かれています。プラグインには2種類あります。

- ビルドツールプラグイン（[SE-0303](https://github.com/apple/swift-evolution/blob/main/proposals/0303-swiftpm-extensible-build-tools.md)）
- コマンドプラグイン（[SE-0332](https://github.com/apple/swift-evolution/blob/main/proposals/0332-swiftpm-command-plugins.md)）

これまでのSwiftPMはビルド時の動作をカスタマイズできませんでしたが、このプラグイン機能で可能になりました。

# ビルドツールプラグイン

ビルドツールプラグインは、SwiftPMのビルド時に外部ツールを実行するようにします。

先ほどのRelease Notesやプロポーザルの中で想定されている用途は、ビルド時のソースコードの自動生成です。たとえば [SwiftGen](https://github.com/SwiftGen/SwiftGen) を使ってソースコードを生成してから、ビルドを実行できます。

ただし、必ずしもそれだけの用途ではなく、ビルド時に好きな外部ツールを実行できます。

## 例：XcodeプロジェクトでSwiftLintを実行する

最近、Xcodeプロジェクトで実装コードをSwiftPMで管理するプロジェクト構成を見かけることが増えてきました。その構成のサンプルを紹介します。

サンプルリポジトリ：[usami-k/XcodeSwiftPMSample](https://github.com/usami-k/XcodeSwiftPMSample)

- アプリのXcodeプロジェクト（`Hello.xcodeproj`）には、必要最小限のものだけ入れる。
- アプリの実装コードは、Swiftパッケージ（`AppFeature`、`Core`）に入れる。
- `Hello.xcodeproj` でアプリに `AppFeature` パッケージをリンクする。

この構成の場合に、ビルド時に [SwiftLint](https://github.com/realm/SwiftLint) を実行したいとします。

従来は、SwiftPMではビルド時の処理をカスタマイズできなかったため、Xcodeのビルドスクリプトで `SwiftLint` を実行する必要がありました。これが、ビルドツールプラグインを使うことで、SwiftPMで `SwiftLint` を実行できます。

なお、Swift 5.6が同梱されたのはXcode 13.3ですので、この方法を使うにはXcode 13.3以降が必要です。

## Package.swiftの記述

SwiftLintのビルドツールプラグインはまだ公式には配布されていません。そこで、試しにビルドツールプラグインのパッケージを作りました。

[usami-k/SwiftLintPlugin](https://github.com/usami-k/SwiftLintPlugin)

このプラグインパッケージを使うには、SwiftPMの設定ファイルである `Package.swift` で、次のように書きます（ファイル全体はGitHubにあります → [Package.swift](https://github.com/usami-k/XcodeSwiftPMSample/blob/main/Hello/AppFeature/Package.swift)）。

```swift
dependencies: [
    .package(url: "https://github.com/usami-k/SwiftLintPlugin", branch: "main"),
],
targets: [
    .target(
        name: "AppFeature",
        plugins: [
            .plugin(name: "SwiftLintPlugin", package: "SwiftLintPlugin"),
        ]
    ),
]
```

まず、`dependencies:` で使いたいプラグインパッケージを指定します。次に、ビルドするターゲットの `.target()` 定義の中の `plugins:` でビルド時に実行するビルドツールプラグインを指定します。

上述のサンプルリポジトリにあるXcodeプロジェクトは、このプラグインパッケージを使って `SwiftLint` を実行するよう設定済みです。Xcodeでビルドすると、SwiftLintの内容がXcode上で警告やエラーとして表示されます。

# コマンドプラグイン

コマンドプラグインは、SwiftPMのコマンドを拡張して、外部ツールを実行できるようにします。

先ほどのRelease Notesやプロポーザルの中で想定されている用途は、ドキュメントの生成、ソースコードのフォーマット、アーカイブの作成、などのタスクの実行です。

`make` コマンドや `npm run` コマンドのような、タスクランナー機能が追加されたと考えることができそうです。

## 例：Swift-DocCを使う

Swiftのドキュメント生成ツールとして [Swift-DocC](https://www.swift.org/documentation/docc/) が2021年にリリースされました。

Swift-DocCはXcodeプロジェクトのソースコードからドキュメントを生成できます。その機能がXcode 13に組み込まれており、簡単に利用できます。

しかし一方で、SwiftPMプロジェクトのソースコードからドキュメントを生成するには多少準備が必要でした。これが、コマンドプラグインを使うことで、SwiftPMで手軽にSwift-DocCを利用できます。

## Package.swiftの記述

Swift-DocCのコマンドプラグインのパッケージが、Appleから配布されています。

[apple/swift-docc-plugin](https://github.com/apple/swift-docc-plugin)

このプラグインパッケージを使うには、SwiftPMの設定ファイル `Package.swift` で次のように書きます。

```swift
dependencies: [
    .package(url: "https://github.com/apple/swift-docc-plugin", from: "1.0.0"),
],
```

単に `dependencies:` で使いたいプラグインパッケージを指定すればよいです。

これにより、次のコマンドが使えるようになります。

- `swift package generate-documentation` : ドキュメントの生成（`.doccarchive` 形式、または静的サイトホスティング形式）
- `swift package preview-documentation` : ドキュメントのプレビュー

これらのコマンドでできることについては、[Swift-DocC プラグインのドキュメント](https://apple.github.io/swift-docc-plugin/documentation/swiftdoccplugin/) を参照してください。

# 自分でプラグインを実装する

ここまでの例では、すでに配布されているプラグインパッケージを使いました。代わりに、自分でプラグインを実装する方法もあります。プラグインの動作を自分の好みにカスタマイズしたい場合や、プロジェクト固有の設定をしたい場合は、この方法が便利です。

以下、ビルドツールプラグインを自分で実装する例を紹介します。これは [ExampleSPMProjectWithSwiftLint](https://github.com/juozasvalancius/ExampleSPMProjectWithSwiftLint) を参考にしました。

## Package.swiftの記述

あるSwiftPMターゲットのビルド時にSwiftLintを実行したいとします。

そのターゲットの設定ファイル `Package.swift` で、次のように書きます。

```swift
targets: [
    .binaryTarget(
        name: "SwiftLintBinary",
        url: "https://github.com/juozasvalancius/SwiftLint/releases/download/spm-accommodation/SwiftLintBinary-macos.artifactbundle.zip",
        checksum: "cdc36c26225fba80efc3ac2e67c2e3c3f54937145869ea5dbcaa234e57fc3724"
    ),
    .plugin(
        name: "SwiftLintPlugin",
        capability: .buildTool(),
        dependencies: ["SwiftLintBinary"]
    ),
    .target(
        name: "AppFeature",
        plugins: ["SwiftLintPlugin"]
    ),
]
```

上記の設定ファイルで、ビルドするターゲットは `AppFeature` です。この `.target()` 定義の中の `plugins:` でビルド時に実行するビルドツールプラグインを指定します。ここでは `SwiftLintPlugin` プラグインを指定しています。

`SwiftLintPlugin` プラグインもターゲットの一種として記述しています。この `.plugin()` 定義の中の `dependencies:` で実行したい外部ツールのバイナリターゲット `SwiftLintBinary` を指定しています。

バイナリターゲット `SwiftLintBinary` は `.binaryTarget()` で定義しています。SwiftPMプラグインが使用するバイナリはartifact bundleという形式で準備する必要があります。上記の設定では、SwiftLintのartifact bundleは [juozasvalancius](https://github.com/juozasvalancius) さんが配布しているものを使用しています。なお、現時点では公式にはSwiftLintのartifact bundleは配布されていませんが、近いうちに公式で配布される予定です。

## プラグインの実装コード

ビルドツールプラグインの処理内容はSwiftコードで書きます。コードは `Plugins` フォルダーに置き、次のように実装します。

```swift
import PackagePlugin

@main
struct SwiftLintPlugin: BuildToolPlugin {
    func createBuildCommands(context: PluginContext, target: Target) async throws -> [Command] {
        return [
            .buildCommand(
                displayName: "Linting \(target.name)",
                executable: try context.tool(named: "swiftlint").path,
                arguments: [
                    "lint",
                    "--in-process-sourcekit",
                    "--path",
                    target.directory.string
                ],
                environment: [:]
            )
        ]
    }
}
```

このように、実行したい処理を `.buildCommand()` で定義すればよいです。ビルドターゲットにこのビルドツールプラグインが指定されていれば、ビルド実行時に自動的にこの処理が実行されます。

なお、Xcode 13.3時点での問題点として、Xcode上でビルドを実行した場合はツールに `environment:` の内容が正しく渡されないようです。このため、環境変数を参照するツールは正しく動作しませんので注意が必要です。コマンドライン引数は利用可能です。

上記の例ではSwiftLintのIn Process SourceKit機能（Xcode 13との連携機能）を使っています。その指定に環境変数 `IN_PROCESS_SOURCEKIT` は使わず、コマンドライン引数 `--in-process-sourcekit` を使っています。

# まとめ

Swift 5.6で追加されたSwiftPMのプラグイン機能について説明しました。そして、配布されているプラグインパッケージを使う方法と、自分でプラグインを実装する方法を説明しました。

SwiftPMのプラグイン機能、個人的には今後も注目していきたい機能です。