---
title: SwiftのObservationフレームワークによる値の監視
tags:
  - Swift
  - SwiftUI
  - iOS
private: false
updated_at: ''
id: null
organization_url_name: yumemi
slide: false
ignorePublish: false
---
# Observationフレームワークとは

Observationフレームワークは、Swiftで値の変更を監視するためのフレームワークです。

モデル層とビュー層のあいだのデータバインディングの実現に利用でき、とくにSwiftUIでの利用が想定されています。

このフレームワークはSwift 5.9で追加されました。iOS 17、macOS 14など現時点での最新OSでのみ動作するため、それより前のOSをサポートする必要があるアプリの開発には利用できません。そのためすぐには利用できない場合が多いでしょうが、将来のデファクトスタンダードになる可能性があるため、今のうちに知っておくと良いでしょう。

# Swiftにおける値の変更の監視

これまでにも、Swiftで値の変更を監視する方法がありました。

* KVO（Key-Value Observing）
    * Objective-Cからの機能
* ObservableObject
    * Combineフレームワークの機能
* ＠Observable
    * Observationフレームワークの機能

KVOはSwift以前のObjective-Cから存在している機能です。Swiftでも利用できますが、レガシーな仕組みであるため制約もあります。

ObservableObjectはCombineフレームワークの機能で、iOS 13からのSwiftUIで利用されています。SwiftUIとCombineはiOS 13で同時に登場しました。宣言的UIであるSwiftUIには値の変更を監視する機能が必要であり、その実現にCombineが利用されました。

＠ObservableはObservationフレームワークの機能で、iOS 17からのSwiftUIで利用されています。ObservableObjectをSwiftUIで利用する際に存在していた、いくつかの欠点を改善しています。

なお、＠ObservableはSwift 5.9で追加されたマクロ機能を使って実現されています。

# ＠Observableの基本

次のように、クラスに `@Observable` をつけると、そのクラスが監視可能になります。

```swift
import Observation

@Observable
final class Model {
    var value: Int = 0
}
```

監視する側では、次のように `withObservationTracking` 関数を使います。これはObservationプロトコルによって、トップレベルに用意されている関数です。

```swift
import Observation

final class ViewController: UIViewController {
    private let model = Model()

    private func tracking() {
        withObservationTracking { [weak self] in
            guard let self else { return }
            print("value: \(model.value)")
        } onChange: {
            print("onChange")
        }
    }
}
```

`withObservationTracking` のクロージャで監視対象が決まります。この中でObservableである `Model` クラスの `model.value` を参照していますが、これによって自動的に `model.value` の値が監視対象となります。

```swift
        withObservationTracking { ...
            ...
            print("value: \(model.value)")
        } onChange: {
            ...
        }
```

監視対象である `model.value` の値が変更されると、変更の通知として `onChange` クロージャが呼ばれます。

```swift
        withObservationTracking { ...
            ...
        } onChange: {
            print("onChange")
        }
```

実際に動作を追ってみましょう。`tracking()` を呼び出すと `print("value: \(model.value)")` が呼ばれて次が出力されます。

```
value: 0
```

それと同時に、`model.value` が監視対象になります。`model.value` の値が変更されると `print("onChange")` が呼ばれて次が出力されます。

```
onChange
```

これによって、値の変更を監視できていることがわかります。

なお、注意点として、この通知は1回だけ来ます。つまり、もう一度 `model.value` の値が変更されても、`onChange` は呼ばれません。

# 監視を継続する

変更の通知は1回だけということでしたが、値の変更の監視を継続的に行うにはどうすれば良いでしょうか。

そのためには、先述のコード例を次のように変更します。監視を再帰的に呼び出すことで、変更の監視を継続できます。

```swift
    private func tracking() {
        withObservationTracking { ...
            ...
        } onChange: {
            Task { @MainActor [weak self] in
                guard let self else { return }
                tracking()
            }
        }
    }
```

# UIKitでの利用例

`@Observable` をUIKitで利用する場合の例を示します。

```swift
    private func tracking() {
        withObservationTracking { [weak self] in
            guard let self else { return }
            label.text = "value: \(model.value)"
        } onChange: {
            Task { @MainActor [weak self] in
                guard let self else { return }
                tracking()
            }
        }
    }
```

この動作を追ってみましょう。`tracking()` を呼び出すと `label.text = "value: \(model.value)"` が呼ばれて、ラベルの表示文字列が更新されます。

```
value: 0
```

それと同時に、`model.value` が監視対象になります。`model.value` の値が変更されると `onChange` が呼ばれて、その中で `tracking()` が呼ばれます。すると、再び `label.text = "value: \(model.value)"` が呼ばれて、ラベルの表示文字列が更新されます。

```
value: 1
```

それと同時に、再度 `model.value` が監視対象になります。こうして、値が変更されるたびにラベルの表示文字列が更新されます。

ここまでに説明した例の、全体のコードを挙げておきます。Observationに関する部分は既に説明したため、問題なく理解できるでしょう。

```swift
import Observation
import UIKit

@Observable
final class Model {
    var value: Int = 0
}

final class ViewController: UIViewController {
    private let model = Model()
    private let label = UILabel()
    private let button = UIButton()

    override func viewDidLoad() {
        super.viewDidLoad()
        setup()
        tracking()
    }

    private func setup() {
        view.backgroundColor = .white

        view.addSubview(label)
        label.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            label.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 16),
            label.leadingAnchor.constraint(
                equalTo: view.safeAreaLayoutGuide.leadingAnchor, constant: 16),
            label.trailingAnchor.constraint(
                equalTo: view.safeAreaLayoutGuide.trailingAnchor, constant: -16),
            label.heightAnchor.constraint(equalToConstant: 32),
        ])
        label.textColor = .black
        label.textAlignment = .center

        view.addSubview(button)
        button.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            button.topAnchor.constraint(equalTo: label.bottomAnchor, constant: 16),
            button.leadingAnchor.constraint(
                equalTo: view.safeAreaLayoutGuide.leadingAnchor, constant: 16),
            button.trailingAnchor.constraint(
                equalTo: view.safeAreaLayoutGuide.trailingAnchor, constant: -16),
            button.heightAnchor.constraint(equalToConstant: 32),
        ])
        var configuration = UIButton.Configuration.plain()
        configuration.title = "Update"
        button.configuration = configuration

        button.addAction(
            UIAction { [weak self] _ in
                guard let self else { return }
                update()
            },
            for: .touchUpInside)
    }

    private func update() {
        model.value += 1
    }

    private func tracking() {
        withObservationTracking { [weak self] in
            guard let self else { return }
            label.text = "value: \(model.value)"
        } onChange: {
            Task { @MainActor [weak self] in
                guard let self else { return }
                tracking()
            }
        }
    }
}
```

# SwiftUIの場合

次に、`@Observable` をSwiftUIで利用する場合の例を示します。

実は、SwiftUIではUIKitよりずっと楽に利用できます。

```swift
struct MyView: View {
    var model = Model()
    
    var body: some View {
        Text(model.value)
    }
}
```

これだけで継続的に値の変更を監視できます。

`View` の `body` 内で参照されているプロパティが自動的に監視対象になります。そして継続的に監視されます。`withObservationTracking` 関数のコード記述は不要です。

# Observationの特徴

Observationフレームワークの特徴的な性質をいくつか紹介します。

次のように、監視可能なプロパティがふたつある場合を考えます。

```swift
@Observable
final class Model {
    var value1: Int = 0
    var value2: Int = 0
}

struct MyView: View {
    var model = Model()
    
    var body: some View {
        Text(model.value1)
    }
}
```

`body` 内で参照されている `model.value1` のみが監視対象になり、`model.value2` は監視対象になりません。`model.value2` の値が変更されても、`MyView` は再描画されずにすみます。不要な監視は発生せず、効率的です。

また、次のようにコレクションを含む場合を考えます。これはAppleのドキュメントに挙げられている例です。

```swift
@Observable
final class Book: Identifiable {
    var title = "Sample Book Title"
    ...
}

struct LibraryView: View {
    @State private var books = [Book(), Book(), Book()]

    var body: some View {
        List(books) { book in 
            Text(book.title)
        }
    }
}
```

この場合、`books` への要素の追加や削除に応じてListを更新できます。つまり、コレクションが監視できます。例えば要素が3つから4つに増えると、Listの表示アイテムも4つに増えます。

また、どれかひとつの `book.title` が変更されると、その `book.title` に対応するViewだけが更新されます。例えば `books[1].title` が変更されると、その `books[1]` に対応するViewだけが更新されます。全体が再描画されないため、効率的です。

# Observableフレームワークの制約

制約もいくつかあります。これらは今後解消されなさそうです。

構造体には `@Observable` をつけられません。値型（構造体）では使えず、参照型（クラス）で使える機能となります。これについては、Swiftは構造体の値変更を監視するようには設計されてないとのことです。

バックポートには対応しません。Swiftの別の機能であるConcurrencyがバックポートに対応したという事例があったため、Observationにもバックポートが期待されました。しかし、議論の結果、バックポートには対応しないことになりました。古いバージョンのSwiftUIを対応させるのが難しいとのことです。

# おわりに

Observationフレームワークによる値の変更の監視は、既存の方法よりもシンプルで効率的です。とくにSwiftUIとの相性が良いです。

古いOSで使えないのでまだ採用できない場合が多いでしょうが、将来のデファクトスタンダードとなりそうです。

# 参考文献

* [Observation | Apple Developer Documentation](https://developer.apple.com/documentation/observation)
    * Appleのドキュメント
* [Discover Observation in SwiftUI - WWDC23 - Videos - Apple Developer](https://developer.apple.com/wwdc23/10149)
    * WWDC23での紹介動画
* [Swift 5.9からのデータ監視 Observationフレームワーク入門：Personal Factory](https://techbookfest.org/product/vFjd8cyB0ic0v2a7EECm7u)
    * 技術書典15で頒布された技術同人誌
    * マクロの展開など仕組みの深掘り、ObservableObjectとの比較、SwiftUIとの連携など、詳細な解説があります。今回、とくに参考にさせていただきました。
