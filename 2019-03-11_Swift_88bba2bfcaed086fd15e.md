<!--
title:   Swift 5 の AdditiveArithmetic protocol とは何か
tags:    Swift
id:      88bba2bfcaed086fd15e
private: false
-->
# AdditiveArithmetic とは

`AdditiveArithmetic` は Swift 5 で新しく導入された protocol です（[SE-0233](https://github.com/apple/swift-evolution/blob/master/proposals/0233-additive-arithmetic-protocol.md)）。

Swift には数に関する protocol がいくつもありますが、そのうちのひとつに [Numeric](https://swiftdoc.org/v4.2/protocol/numeric/) protocol があります（参考：[Numeric 関連の階層](https://swiftdoc.org/v4.2/protocol/numeric/hierarchy/)）。`Numeric` は、数の性質のうち、たし算、ひき算、かけ算の3つの演算を定義する protocol です。

[AdditiveArithmetic](https://developer.apple.com/documentation/swift/additivearithmetic) は `Numeric` よりも条件が弱い protocol で、たし算とひき算の2つの演算を定義する protocol です。

Swift 5 での `AdditiveArithmetic` 導入にともない、`Numeric` は `AdditiveArithmetic` を継承してかけ算を追加する形になりました。つまり、[Numeric 関連の階層](https://swiftdoc.org/v4.2/protocol/numeric/hierarchy/)において、`Equatable` と `Numeric` の間に `AdditiveArithmetic` が入ったことになります。

余談：数学用語になじみがある方向けに補足します（なじみがない方は飛ばしてください）。上記の `AdditiveArithmetic` は [群](https://ja.wikipedia.org/wiki/%E7%BE%A4_(%E6%95%B0%E5%AD%A6))（加法群） に、`Numeric` は [環](https://ja.wikipedia.org/wiki/%E7%92%B0_(%E6%95%B0%E5%AD%A6)) に対応する、と言われれば分かりやすいかと思います。

# AdditiveArithmetic の定義

`AdditiveArithmetic` protocol の定義は以下のようなものです。単純なたし算とひき算を定義しています。

```swift
protocol AdditiveArithmetic {
    static var zero: Self
    static func + (Self, Self) -> Self
    static func += (Self, Self)
    static func - (Self, Self) -> Self
    static func -= (Self, Self)
}
```

標準の数値型である `Int` や `Float` は `AdditiveArithmetic` に準拠しています。また、独自の型で `AdditiveArithmetic` に準拠するものを作ることもできます。

この protocol の応用例として、ドキュメントには以下の例が記載されています。`AdditiveArithmetic` 型の要素を持つ `Sequence` にメソッドを宣言しています。

```swift
extension Sequence where Element: AdditiveArithmetic {
    func sum() -> Element {
        return reduce(.zero, +)
    }
}
```

`Int` や `Float` などの具体的な型を使う代わりに `AdditiveArithmetic` を使うことで、`sum()` は `AdditiveArithmetic` に準拠したあらゆる型に対応できる汎用メソッドとなっています。

なお、これにかけ算を追加したものが `Numeric` となります。

```swift
protocol Numeric : AdditiveArithmetic {
    static func * (Self, Self) -> Self
    static func *= (Self, Self)
}
```

# AdditiveArithmetic が有益な場合

従来の Swift で、すでに `Numeric` が存在していますし、それで足りるようにも思えます。なぜわざわざ Swift 5 で `AdditiveArithmetic` を導入したのでしょうか。

それは、`AdditiveArithmetic` に準拠するのは簡単だが `Numeric` に準拠するのは難しい、というたぐいの型があるからです。つまり、たし算やひき算は問題なくできるが、かけ算に相当する演算がない、という型です。

具体的な例としては、ベクトルがそうです。ベクトル同士のたし算やひき算はあるので `AdditiveArithmetic` に準拠するのは簡単です。しかし、ベクトル同士のかけ算に相当する演算はないため `Numeric` に準拠するのは難しいです。そのため、`AdditiveArithmetic` が導入されたほうが都合が良いのです。

# 具体例 : 3次元ベクトル型

`AdditiveArithmetic` の実例を見るために、3次元ベクトルの型 `Vector3D` を作ってみます。

```swift
struct Vector3D {
    typealias Scalar = Float
    var x: Scalar
    var y: Scalar
    var z: Scalar
}
```

これを、`AdditiveArithmetic` に準拠させます。

```swift
extension Vector3D : AdditiveArithmetic {
    static var zero = Vector3D(x: 0, y: 0, z: 0)

    static func + (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
        return Vector3D(x: lhs.x + rhs.x, y: lhs.y + rhs.y, z: lhs.z + rhs.z)
    }
    static func - (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
        return Vector3D(x: lhs.x - rhs.x, y: lhs.y - rhs.y, z: lhs.z - rhs.z)
    }
    static func += (lhs: inout Vector3D, rhs: Vector3D) {
        lhs = lhs + rhs
    }
    static func -= (lhs: inout Vector3D, rhs: Vector3D) {
        lhs = lhs - rhs
    }
}
```

これで、前述した `sum()` メソッドが `Vector3D` に対して使えるようになります。

```swift
extension Sequence where Element: AdditiveArithmetic {
    func sum() -> Element {
        return reduce(.zero, +)
    }
}

let sum = [Vector3D(x: 1, y: 2, z: 3),
           Vector3D(x: 4, y: 3, z: 2),
           Vector3D(x: -3, y: -3, z: -3)].sum()
if sum == Vector3D(x: 2, y: 2, z: 2) {
    print("😄")
}
```

なお、ベクトルの演算といえばたし算ひき算以外にもスカラー倍演算がありますが、`AdditiveArithmetic` の説明には関係がないのでここでは省略します。それを含めたコードは GitHub に置いています（[usami-k/AdditiveArithmeticSample](https://github.com/usami-k/AdditiveArithmeticSample)）。

# AdditiveArithmetic のメソッドを別のメソッドで実装するパターン

`AdditiveArithmetic` に準拠させる際の実装ですが、いくつかのメソッドは別のメソッドから実装できます。

上記の例でも、`+` を使って `+=` を実装しましたし、`-` を使って `-=` を実装しました。

逆に、`+=` を先に実装して `+` を実装することもできます。

```swift
static func += (lhs: inout Vector3D, rhs: Vector3D) {
    lhs.x += rhs.x ; lhs.y += rhs.y ; lhs.z += rhs.z
}
static func + (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
    var lhs = lhs ; lhs += rhs ; return lhs
}
```

また、次のような単項演算子 `-` を定義しておくと、`+` を使って二項演算子 `-` を実装することもできます。

```swift
// 単項演算子
prefix static func - (v: Vector3D) -> Vector3D {
    return Vector3D(x: -v.x, y: -v.y, z: -v.z)
}
// 二項演算子
static func - (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
    return lhs + (-rhs)
}
```

逆に、二項演算子 `-` を先に実装して単項演算子 `-` を実装することもできます。

```swift
// 二項演算子
static func - (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
    return Vector3D(x: lhs.x - rhs.x, y: lhs.y - rhs.y, z: lhs.z - rhs.z)
}
// 単項演算子
prefix static func - (v: Vector3D) -> Vector3D {
    return .zero - v
}
```

# 最後に

`AdditiveArithmetic` は、通常はあまり触れる機会はないかもしれません。しかし、上述の例で見たようにベクトル型を扱いやすくなるなどの利点があり、それが活用できる分野では役に立つこともありそうです。今後の展開が気になるところです。