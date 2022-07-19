<!--
title:   Swift Regexでキャプチャや名前付きキャプチャを使う
tags:    QiitaEngineerFesta2022,Swift,regex,正規表現
id:      5a13d12ffc332ddf0512
private: false
-->
Swift 5.7で、Swift Regexが導入されます。これまでSwiftで正規表現を使う場合は `NSRegularExpression` を使っていましたが、言語組み込みの機能としてサポートされます。

他の言語の正規表現の機能と同様に、キャプチャの機能があり、名前付きキャプチャもあります。ここでは、Swift Regexのキャプチャと名前付きキャプチャの記法をまとめます。

## Swift Regexの記法

Swift Regexでは、正規表現の記法が2通りあります。

1つはRegexリテラルで、次のような記法です。一般的な正規表現の記法と同様です。

```swift
let regex = /\d{4}-\d{2}-\d{2}/
```

もう1つはRegexビルダーで、次のような記法です。こちらはSwift独自の記法です。冗長にはなりますが、意味が分かりやすくなります。

```swift
let regex = Regex {
    Repeat(count: 4) { .digit }
    "-"
    Repeat(count: 2) { .digit }
    "-"
    Repeat(count: 2) { .digit }
}
```

RegexリテラルとRegexビルダーのどちらの記法で書いても、使い方は同様です。

```swift
let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print(match.0) // -> 2022-07-18
}
```

## Regexリテラルでのキャプチャ

マッチした文字列の一部分を取り出して使うには、キャプチャを使います。キャプチャは `( )` を使って書きます。

```swift
let regex = /(\d{4})-(\d{2})-(\d{2})/

let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print("\(match.1)年\(match.2)月\(match.3)日") // -> 2022年07月18日
}
```

キャプチャした文字列は連番で参照します。しかし、キャプチャする部分が増えると、連番では分かりにくくなります。

そこで、キャプチャした部分に名前をつける、名前付きキャプチャという記法があります。これは `(?<name>pattern)` という書き方をします。

```swift
let regex = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/

let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print("\(match.year)年\(match.month)月\(match.day)日") // -> 2022年07月18日
}
```

連番の代わりに名前で参照できるようになり、分かりやすくなりました。

## Regexビルダーでのキャプチャ

Regexビルダーの場合、キャプチャは `Capture` を使って書きます。

```swift
let regex = Regex {
    Capture { Repeat(count: 4) { .digit } }
    "-"
    Capture { Repeat(count: 2) { .digit } }
    "-"
    Capture { Repeat(count: 2) { .digit } }
}

let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print("\(match.1)年\(match.2)月\(match.3)日") // -> 2022年07月18日
}
```

やはり、キャプチャした文字列は連番で参照します。

名前付きキャプチャは `Capture(as:)` を使って書きます。

```swift
let year = Reference(Substring.self)
let month = Reference(Substring.self)
let day = Reference(Substring.self)
let regex = Regex {
    Capture(as: year) { Repeat(count: 4) { .digit } }
    "-"
    Capture(as: month) { Repeat(count: 2) { .digit } }
    "-"
    Capture(as: day) { Repeat(count: 2) { .digit } }
}

let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print("\(match[year])年\(match[month])月\(match[day])日") // -> 2022年07月18日
}
```

Regexビルダーの場合は、`Reference` を用意しておく必要があります。また、参照のしかたが、Regexリテラルでは `match.year` だったところが `match[year]` に変わります。

## TryCapture

Regexビルダーでのキャプチャの場合、`TryCapture` を使うとキャプチャした文字列を変換できます。

```swift
let regex = Regex {
    TryCapture { Repeat(count: 4) { .digit } } transform: { Int($0) }
    "-"
    TryCapture { Repeat(count: 2) { .digit } } transform: { Int($0) }
    "-"
    TryCapture { Repeat(count: 2) { .digit } } transform: { Int($0) }
}

let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print("\(match.1)年\(match.2)月\(match.3)日")
}
```

名前付きキャプチャでも `TryCapture` が使えます。この場合、`Reference` の型も変わります。

```swift
let year = Reference(Int.self)
let month = Reference(Int.self)
let day = Reference(Int.self)
let regex = Regex {
    TryCapture(as: year) { Repeat(count: 4) { .digit } } transform: { Int($0) }
    "-"
    TryCapture(as: month) { Repeat(count: 2) { .digit } } transform: { Int($0) }
    "-"
    TryCapture(as: day) { Repeat(count: 2) { .digit } } transform: { Int($0) }
}

let match = "2022-07-18".wholeMatch(of: regex)
if let match {
    print("\(match[year])年\(match[month])月\(match[day])日")
}
```

## Regexリテラルでの後方参照

キャプチャした文字列をその正規表現の中で使うには、後方参照を使います。キャプチャを後方参照するには `\数字` という書き方をします。

```swift
let regex = /([a-z]+) \1/

let match = "the the".wholeMatch(of: regex)
if let match {
    print(match.1) // -> the
}
```

名前付きキャプチャを後方参照するには `\k<name>` という書き方をします。

```swift
let regex = /(?<word>[a-z]+) \k<word>/

let match = "the the".wholeMatch(of: regex)
if let match {
    print(match.word) // -> the
}
```

## Regexビルダーでの後方参照

Regexビルダーの場合、後方参照は `Capture` を使って書きます。名前付きキャプチャを使う必要があります。

```swift
let word = Reference(Substring.self)
let regex = Regex {
    Capture(as: word) { OneOrMore(/[a-z]/) }
    One(.whitespace)
    Capture(word)
}

let match = "the the".wholeMatch(of: regex)
if let match {
    print(match[word]) // -> the
}
```

## 参考

以下を参考にさせていただきました。ありがとうございます。

- [Swift Regex](https://swiftregex.com/) : Regular Expression Tester with highlighting for Swift Regex
- [正規表現で名前付きキャプチャを使う - Qiita](https://qiita.com/jnchito/items/cceb669cb06fc044f411)

また、筆者がSwift Regexについて勉強会で登壇したスライドも挙げておきます。

- [Meet Swift Regex - Swift愛好会スピンオフ WWDC22セッション要約会](https://speakerdeck.com/usamik26/meet-swift-regex)
- [Swift Regexの話 - YUMEMI.swift #15 〜WWDC復習会〜](https://speakerdeck.com/usamik26/swift-regex)