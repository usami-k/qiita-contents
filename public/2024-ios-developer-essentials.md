---
title: iOSアプリ開発のいま：基礎知識から業務レベルのツールまで
tags:
  - 'iOS'
  - 'iOSDC2024'
  - 'Swift'
  - 'Xcode'
private: false
updated_at: ''
id: null
organization_url_name: yumemi
slide: false
ignorePublish: true
---
# iOSアプリ開発のいま：<br />基礎知識から業務レベルのツールまで

<div class="author-info">
宇佐見公輔（株式会社ゆめみ）<br />
𝕏: @usamik26<br />
Bluesky: @usamik26.bsky.social
</div>

iOS アプリ開発のチュートリアルや入門書は多く存在しており、学校の授業で扱われることもあります。しかし一方で、実際のアプリ開発の業務では、より多くの知識が求められます。このギャップに初学者がとまどうことも少なくありません。この記事では、業務レベルのアプリ開発でどんな知識が求められ、どんなツールが使われているのかをざっくり紹介します。初学者に参考としてもらうとともに、中級〜上級者にも開発環境を俯瞰的に捉える機会になればと考えています。

## 開発対象デバイス

iOS アプリ開発の対象デバイスは主に **iPhone** です。しかし近年では、同時に **iPad** にも対応することが多いです。OS は iOS と iPadOS ですが、開発者目線ではこのふたつの OS はあまり区別しません。基本的に同じソースコードで両方に対応します。ただし、iPad 対応のための UI 設計や実装は必要になります。iPhone のみを意識していると、iPad で思わぬクラッシュが発生することもあります。

他の対象デバイスには、Apple Watch（watchOS）、Apple Vision Pro（visionOS）、Mac（macOS）、Apple TV（tvOS）があります。これらは基本的に、iPhone / iPad アプリとは別の実装が必要になります。**Apple Watch** 対応は、数として多くはありませんが存在しています。**Apple Vision Pro** 対応は今後増える可能性があります。iOS アプリ開発としての Mac 対応や Apple TV 対応は実例が少ないようです。

## ネイティブアプリ開発とクロスプラットフォーム開発

iPhone / iPad を対象にしたアプリ開発として、ネイティブアプリ開発とクロスプラットフォーム開発のふたつの選択肢があります。**ネイティブアプリ開発**は、iPhone / iPad アプリ開発に特化して Swift で実装する開発方法です。**クロスプラットフォーム開発**は、iPhone / iPad を含む複数のプラットフォームに対応できる技術を使って実装する開発方法です。

一般に iOS アプリ開発といえばネイティブアプリ開発のことを指します。iPhone / iPad を対象にしたアプリ開発としては、クロスプラットフォーム開発よりもネイティブアプリ開発が効率的であり、iOS 固有の機能を活用しやすいという利点があります。

一方で、複数のプラットフォームに対するアプリ開発を共通化したいという観点から、クロスプラットフォーム開発が採用されることもあります。クロスプラットフォーム開発のための技術にはさまざまなものがあり、技術への人気にも移り変わりがあります。現在よく使われている技術は次のものがあります。

- Flutter (https://flutter.dev)
- Kotlin Multiplatform (https://www.jetbrains.com/kotlin-multiplatform/)
- React Native (https://reactnative.dev)

**Flutter** は動作パフォーマンスの良さやエンジニアにとっての開発体験の良さなどから、現在もっとも人気があります。**Kotlin Multiplatform** は Kotlin を知っていれば学習することが少なくてすむ利点があります。**React Native** も JavaScript や React を知っていれば学習することが少なくてすむ利点があります。

本記事では、このセクションより後では、ネイティブアプリ開発を前提とします。

## プログラミング言語

iOS アプリ開発で使われるプログラミング言語は Swift です。かつては Objective-C が使われていましたが、現在ではほとんど見かけません。**Swift** は新規参入者には親しみやすく、エキスパートには強力な汎用プログラミング言語です。速く、モダンで、安全であり、書くのが楽しいです。

Swift は WWDC 2014 で発表されてから 10 年が経ち、継続的にバージョンアップしています。記事執筆時点での Swift のバージョンは 5.10 で、まもなく Swift 6 がリリースされます。バージョンアップごとにさまざまな機能が追加されているため、難易度が上がっている印象もあります。

最近の機能で重要なものは、**Swift Concurrency** です。同時並行処理（非同期処理や並列処理）のコードを書くためのサポートが言語に組み込まれています。`async` / `await` キーワード、タスク、アクター、`Sendable` 型などがそれに当たります。言語に組み込まれたのは Swift 5.5 でした。Swift 6 ではコンパイラによる安全性チェックが追加されます。安全性チェックはこれまでもオプションとして存在していましたが、Swift 6 で対応必須となります。これに伴って、ソースコードの修正が必要になってきます。

Swift の情報源に公式ドキュメント「**The Swift Programming Language**」があります。原文は英語ですが、コミュニティ有志によって翻訳された日本語版があり、Swift 公式ページからリンクされています。

<!-- textlint-disable prh -->
<!-- 半角カッコに対する指摘を無効化する -->

- The Swift Programming Language(日本語版)：https://www.swiftlangjp.com

<!-- textlint-enable prh -->

## UI フレームワーク

iOS アプリ開発で使われる UI フレームワークには、UIKit と SwiftUI のふたつがあります。**UIKit** は iPhone とともに登場して以来、iOS アプリ開発の主要なフレームワークとして使われてきています。**SwiftUI** は WWDC 2019 で登場した、Swift 言語の機能を活用した宣言的 UI フレームワークです。

SwiftUI は登場当初、UI を簡潔に記述できる利点はあったものの、UIKit に比べて多くの機能が不足していました。そのため、高度な画面を実装できない未完成なフレームワークという評価でした。その後、バージョンアップのたびに機能が追加され、高度な画面も実装できるようになってきました。それによって評価が改善していて、普及しつつあります。

一方、UIKit についても機能追加が引き続き行われており、急に UIKit が使えなくなる状況は当面ないと考えられます。ただし、iOS の一部の機能は SwiftUI でのみサポートされています。たとえば、ウィジェットの UI 実装では UIKit はサポートされておらず、SwiftUI で実装する必要があります。

現在の iOS アプリ開発では、UIKit と SwiftUI とを併用するケースが多くなっています。ただ、併用のしかたには大きくふたつの方針が考えられます。

ひとつは、UIKit を中心にして、部分的に SwiftUI を採用するという方針です。とくに、既存の UIKit ベースのプロジェクトがある場合には、いきなり全面的に SwiftUI に移行するのではなく、部分的に SwiftUI を取り入れていくのが現実的なアプローチです。また、既存アプリにウィジェット機能を追加したい、などの状況でこの方針になることもあります。

もうひとつは、SwiftUI を中心にして、必要な箇所だけ UIKit を採用するという方針です。とくに、新規のプロジェクトではこのケースが増えてきています。ほとんど完全に SwiftUI で実装することで、アプリ全体の設計も見通しがよくなります。とはいえ、UIKit がまったく不要というケースはなかなかありません。

最近 iOS アプリ開発を始めた方は、SwiftUI から学び始めたパターンが多いでしょう。それ自体は問題ではないのですが、実際に業務として iOS アプリ開発をする場合には UIKit の知識も必要になる、というのが現在の iOS アプリ開発の状況です。将来的には、徐々に SwiftUI の比率が増していくでしょう。

### UI フレームワーク - SwiftUI についての補足

SwiftUI は**データバインディング**の設計が優れています。`@State`、`@Binding`、`@Environment` などのプロパティラッパーを使って、データの変更を UI に反映させることができます。これによって、UI の状態とデータの状態が同期されるため、コードが簡潔になります。

iOS 17 から **Observation** フレームワークが登場して、データバインディングの実装手段が増えており、注目しておくべき要素です。Observation フレームワークは iOS 17 以降でしか使えませんが、サードパーティ製のバックポートライブラリ Perception があります。これにより、iOS 13 以降で Observation を活用できます。

- Perception (https://github.com/pointfreeco/swift-perception)

### UI フレームワーク - UIKit についての補足

UIKit で重要なコンポーネントに **UITableView** と **UICollectionView** があります。複数のアイテムを効率的にリスト表示したりグリッド表示したりするためのコンポーネントです。実装がやや難しいのですが、大量のアイテムを扱う際には必須のコンポーネントです。なお、UITableView の機能は UICollectionView でほぼ代替できるため、UICollectionView に置き換わっていく傾向があります。

もうひとつ重要な UI コンポーネントとして、**WKWebView** があります。アプリ内で Web コンテンツを表示するためのコンポーネントです。WKWebView は SwiftUI や UIKit ではなく WebKit フレームワークに属しますが、UI の仕組みは UIKit のコンポーネントと同様です。そのため、これを使いたい場合も UIKit の知識が必要になります。

### UI フレームワーク - UI レイアウトについての補足

UIKit で難しい点のひとつに Auto Layout による UI レイアウトがあります。単純なレイアウトであればそう難しくはありませんし、UIStackView を活用すれば柔軟なレイアウトも可能です。ただ、場合によっては Auto Layout の詳細な知識が必要になることもあります。この問題を根本的に解決したのが SwiftUI だといえます。この点は、SwiftUI に移行するメリットのひとつになります。

## 統合開発環境（IDE）

iOS アプリ開発では IDE として **Xcode** が使われます。かつては AppCode という JetBrains 社の IDE も使われていましたが、2022 年に販売・サポートが終了しました。Swift 言語を使ったプログラミングは、 Visual Studio Code でも公式の Swift 拡張機能をインストールすれば可能です。しかし、iOS アプリ開発では Xcode を使う必要があります。

Xcode は、以前は機能がそれほど充実しておらず、IDE としての評価はあまり高くありませんでした。しかし、毎年のバージョンアップで少しずつ機能追加や改善が行われていて、現在ではそれほど低い評価はされなくなってきています。ただ、ときおりバグのような挙動が見受けられることはあります。この点では、今後の改善が期待されます。

Xcode は iOS SDK などとセットでの配布になっています。このため場合によっては、バージョン違いの複数の Xcode をインストールする必要があります。複数の Xcode を管理するには、次の **Xcodes.app** というツールが便利です。

- Xcodes.app (https://github.com/XcodesOrg/XcodesApp)

## Formatter と Linter

Formatter とは、ソースコードのフォーマットを自動で整えるツールです。これによって可読性が向上し、コードのメンテナンスの手間が軽減されます。Linter とは、ソースコードを解析してプログラミングスタイルの問題を発見するツールです。これによってコーディング規約違反をみつけて、潜在的な誤りを早期に修正できます。Formatter と Linter は、コードの品質を保つために必須のツールです。

Swift 言語向けの Formatter と Linter でよく使われるものに、次があります。

<!-- textlint-disable prh -->
<!-- swift-format に対する指摘を無効化する -->

- apple/swift-format (https://github.com/apple/swift-format)
- nicklockwood/SwiftFormat (https://github.com/nicklockwood/SwiftFormat)
- SwiftLint (https://realm.github.io/SwiftLint/)

**apple/swift-format** は Swift 公式の Formatter であり、最近の iOS アプリ開発でよく採用されています。**nicklockwood/SwiftFormat** はサードパーティ製の Formatter です。公式の swift-format の登場前にはよく採用されていましたし、現在でも使われています。**SwiftLint** はサードパーティ製の Linter で、以前から現在まで iOS アプリ開発でよく採用されています。

Formatter は簡易的な Linter 機能を持っていますし、Linter は簡易的な Formatter 機能（Auto correct）を持っています。そのため、どれかひとつだけを導入することも考えられます。ただ、実際のプロジェクトでは swift-format＋SwiftLint または SwiftFormat＋SwiftLint という組み合わせで導入することが多いです。

<!-- textlint-enable prh -->

難点として、Formatter と Linter が別々にルール設定を持っているため、お互いのルールが干渉してしまうことがあります。そのため、ルール設定には注意が必要です。もしルール設定が大変だと感じられるなら、まずは現場で採用率が高くコードの品質向上の効果が高い SwiftLint を最初に導入することをお勧めします。

## パッケージ管理

iOS アプリ開発でライブラリを使いたい場合、一般にはライブラリをパッケージとして導入します。パッケージ管理ツールには、次のものがあります。

- Swift Package Manager（SwiftPM）
- Carthage (https://github.com/Carthage/Carthage)
- CocoaPods (https://cocoapods.org)

以前は CocoaPods や Carthage を使うのが一般的でした。しかし現在では、**Swift Package Manager** を採用するのが普通になってきています。Swift Package Manager は Swift 公式のパッケージ管理ツールであり、Xcode にも統合されています。特に理由がない限りは、CocoaPods や Carthage を導入する代わりに Swift Package Manager を使うことをお勧めします。

## Xcode プロジェクト

iOS アプリ開発では IDE として Xcode を使いますが、Xcode はソースコードの管理に拡張子 `.xcodeproj` のバンドル（フォルダ）を用います。この中に含まれる `project.pbxproj` ファイルには、プロジェクトに含まれるソースコードのファイルパスなどの情報が保存されています。

個人開発の場合にはあまり問題になりませんが、チーム開発の場合にはこの形式は問題を引き起こすことがあります。プロジェクトにファイルを追加したり削除したりすると、この `project.pbxproj` ファイルが変更されるため、コンフリクトが発生しやすくなります。

この問題を回避する方法のひとつに、`project.pbxproj` ファイルを Git の管理対象から除外する方法があります。代わりに **XcodeGen** などのツールで `project.pbxproj` ファイルを自動生成します。

- XcodeGen (https://yonaskolb.github.io/XcodeGen/)
- Tuist (https://tuist.io)

問題回避のもうひとつの方法は、Swift Package を利用して、プロジェクトを複数のモジュールに分割する**マルチモジュール構成**を採用する方法です。Swift Package ではシンプルな `Package.swift` ファイルでプロジェクトを管理するため、`project.pbxproj` ファイルの問題が発生しにくくなります。

## アプリ実装のアーキテクチャ

チーム開発の場合には、アプリの実装に何らかのアーキテクチャを導入することが多いです。チーム内での実装パターンを統一することで、コードの品質を保つことができます。iOS アプリ開発で使われるアーキテクチャはさまざまなものがありますが、たとえば次のものがあります。

- MVC
- MVVM
- Redux
- Composable Architecture（TCA）(https://github.com/pointfreeco/swift-composable-architecture)

アーキテクチャの選定は、プロジェクトの規模やチームのスキルなどによって異なってきます。一概にどれがよいとはいえません。最近の iOS アプリ開発では、**Composable Architecture（TCA）** が注目されているアーキテクチャのひとつです。

## Swift のガイドライン

Swift コードを書く際には、Swift 公式ドキュメント「**API Design Guidelines**」を読んでおくことを勧めます。

- API Design Guidelines (https://www.swift.org/documentation/api-design-guidelines/)

API の命名や設計についての、重要なガイドラインです。Swift の分かりやすさや書きやすさは、API の命名や設計によるところも大きいのです。

- API 使用時点での明確さは最重要な目標
- 明確さは簡潔さよりも重要
- API の機能をシンプルな言葉で記述できないときは誤った API を設計している可能性がある

## デザインのガイドライン

iOS アプリなど Apple プラットフォームのデザインに関するガイドラインは、Apple の公式ドキュメント「**Human Interface Guidelines**」にまとめられています。名称が長いため、略して「HIG」と呼ばれることもあります。

デザインのガイドラインというと、開発者にはあまり関係がないと感じる方もいるでしょうか。しかし、開発者にとっても重要な事柄が多く記載されています。実際に読んでみれば、iOS SDK の API 設計と HIG の記述とが密接に関連していることに気づくでしょう。

少なくとも、どんなことが書いてあるのかざっと目を通すくらいはしておくべきドキュメントです。以前は英語のみでしたが、最近は公式から日本語版が公開されているため、読みやすくなりました。

<!-- textlint-disable spellcheck-tech-word -->
<!-- 「インターフェイス」に対する指摘を無効化する -->

- ヒューマンインターフェイスガイドライン (https://developer.apple.com/jp/design/human-interface-guidelines/)

<!-- textlint-enaable spellcheck-tech-word -->

たとえば、iOS アプリにアクセシビリティ対応を入れたいのならば、HIG のアクセシビリティのセクションを読むべきです。そこに重要な情報が書かれています。

## Web API 通信

iOS アプリ開発では、サーバとの Web API 通信が必要になることが多いです。Web API 通信には、次のクラスまたはライブラリを使います。

- URLSession
- Alamofire (https://github.com/Alamofire/Alamofire)
- APIKit (https://github.com/ishkawa/APIKit)

**URLSession** は標準で用意されているクラスで機能も豊富なため、多くの場合はこれを採用して問題なく実装できます。場合によっては、Alamofire や APIKit といったサードパーティ製のライブラリが採用されることもあります。

最近では、通信に必要なコードを生成する **Swift OpenAPI Generator** というツールが登場しました。実践例はまだ少ないですが、今後検討の余地はあるでしょう。

- Swift OpenAPI Generator (https://github.com/apple/swift-openapi-generator)

通信のデバッグツールには、**Proxyman** や **Charles** があります。どちらも Mac で使うことができ、iOS デバイスの通信をプロキシして解析できます。通信内容を書き換えてのテストも可能です。

- Proxyman (https://proxyman.io)
- Charles (https://www.charlesproxy.com)

## ユニットテスト

アプリの品質を保つには、ユニットテストも重要です。iOS アプリ開発でユニットテストを行うには、次のものが使われます。

- XCTest
- Quick, Nimble (http://quick.github.io/Quick/)

**XCTest** は標準のテストフレームワークであり、Xcode に統合されています。**Quick** と **Nimble** はサードパーティ製のテストフレームワークで、XCTest の拡張機能として使われます。

なお、WWDC24 で **Swift Testing** についてのセッションが公開されました。今後はこれも使われていくと思われます。

## 依存オブジェクト注入（Dependency Injection、DI）

依存オブジェクト注入（DI）は、アプリケーションのコンポーネント間の依存関係を整理するためのデザインパターンです。採用するアーキテクチャによっては、DI が必須となることがあります。また、ユニットテストを容易にするためにも DI が有効です。

<!-- textlint-disable prh -->
<!-- swift-dependencies に対する指摘を無効化する -->

Swift で DI を実現するための手法は、まだあまり定番としては確立されていません。しかし、**swift-dependencies** は有力です。SwiftUI の `@Environment` に似せた DI ライブラリです。

- swift-dependencies (https://github.com/pointfreeco/swift-dependencies)

<!-- textlint-enable prh -->

## 継続的インテグレーション（Continuous Integration、CI）

継続的インテグレーション（CI）は、リポジトリへのコミットをトリガーにビルドやテストを自動化するプラクティスです。CI サービスにはさまざまなものがありますが、たとえば次のものが使われます。

- Xcode Cloud
- GitHub Actions
- Bitrise (https://www.bitrise.io)

**Xcode Cloud** は Apple の公式 CI サービスであり、Xcode に統合されています。**GitHub Actions** は GitHub の公式 CI サービスであり、GitHub との連携が強力です。**Bitrise** は iOS アプリ開発をサポートしています。

また、CI 上での実行処理の自動化ツールとして、iOS アプリ開発では **Fastlane** が有名で、よく使われています。

- Fastlane (https://fastlane.tools)

## その他の知っておきたい知識

最後に、iOS アプリ開発で知っておくとよい知識を、詳細は省略してリストとして列挙しておきます。必須の知識ではありませんが、実際のアプリ開発への応用が効きやすいものです。

- プッシュ通知
    - APNs
- OS Extension
    - ウィジェット 
    - App Intents
    - Universal Links
- データベース
    - CoreData / SwiftData
    - Realm
    - Firebase
- リアクティブプログラミング
    - RxSwift
    - Combine
- アプリ内課金
    - StoreKit
- AR / VR
    - ARKit
    - RealityKit
- メディア
    - AVFoundation
    - PhotoKit
    - Metal
- 機械学習
    - CoreML
- 位置情報
    - Core Bluetooth
    - Core Location
    - MapKit
