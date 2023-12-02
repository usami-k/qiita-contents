---
title: Rust Nannouを使うときのtips
tags:
  - Rust
  - CreativeCoding
  - GenerativeArt
  - nannou
private: true
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
先月に技術書典15や技書博9で頒布した[『ゆめみ大技林 '23 (2)』](https://booth.pm/ja/items/5237542)で、Rustでクリエイティブコーディングをする記事を書きました。この内容はQiitaにも投稿しています。

https://qiita.com/usamik26/items/dd3681a047e56656146a

そこで取り上げたNannouについて、本記事では追加のtipsを紹介します。Nannouを知っていることが前提となります。Nannouについて知りたい方は、上述の記事をぜひ読んでみてください。

## ウィンドウサイズを指定する

`nannou:app` 関数を使う方法では、次のようにウィンドウを生成するコードを自分で書いていました。

```rust
fn main() {
    nannou::app(model).run();
}

fn model(app: &App) -> Model {
    let _window = app.new_window().view(view).build().unwrap();

    // 後略
}
```

この際に、次のように `size` を使ってウィンドウのサイズを指定できます。

```rust
fn model(app: &App) -> Model {
    let _window = app
        .new_window()
        .size(408, 408) // サイズを指定
        .view(view)
        .build()
        .unwrap();

    // 後略
}
```

ウィンドウを408x408の正方形のサイズにしています。これは筆者が作った次のコードからの抜粋です。

https://github.com/usami-k/coding-nannou/tree/main/2023/0003_grid-art

クリエイティブコーディングでは座標指定での描画をよく使うので、必要に応じてウィンドウサイズを指定すると便利です。

## キーが押されたときの処理を書く

Nannouでは、単に描画するだけでなく、ユーザーの入力を受け付けることもできます。例えば、キーが押されたときに何か処理をしたい場合は、次のように `key_pressed` を使います。

```rust
fn model(app: &App) -> Model {
    let _window = app
        .new_window()
        .key_pressed(key_pressed) // キー押下イベントを処理する関数を指定
        .view(view)
        .build()
        .unwrap();

    // 後略
}

fn key_pressed(_app: &App, model: &mut Model, key: Key) {
    if key == Key::Space {
        // スペースキーが押されたときの処理
    }
}
```

筆者は、スペースキーが押されたときにアニメーションを開始する処理をよく書いていて、例えば次のコードでも書いています。

https://github.com/usami-k/coding-nannou/tree/main/2023/0001_randomwalk

これは、作成したアニメーションを動画としてキャプチャしたいときに使っています。ウィンドウを表示して動画キャプチャの準備を整えたら、スペースキーを押してアニメーションを開始するという流れです。

## 更新処理がどのくらいの頻度で呼ばれるか確認する

Nannouの `update` 関数や `view` 関数は実行中に繰り返し呼ばれます。これによってアニメーションが実現できるわけです。このとき、これらの関数がどのくらいの頻度で呼ばれているかは気になる点でしょう。次のようにデバッグ出力すると確認できます。

```rust
fn update(app: &App, model: &mut Model, _update: Update) {
    println!("duration {:?}", app.duration);
}
```

`app.duration` はNannouの `Time` 型の値です。

https://docs.rs/nannou/latest/nannou/state/time/struct.Time.html

`since_start` がプログラムが開始してからの経過時間、`since_prev_update` が前回の更新からの経過時間です。筆者の環境では、次のように表示されました（一部抜粋）。

```
duration Time { since_start: 1.098403958s, since_prev_update: 16.951666ms }
duration Time { since_start: 1.114849167s, since_prev_update: 16.445209ms }
duration Time { since_start: 1.131892958s, since_prev_update: 17.043791ms }
duration Time { since_start: 1.148883375s, since_prev_update: 16.990417ms }
duration Time { since_start: 1.164966542s, since_prev_update: 16.083167ms }
```

上記のログで `since_prev_update` の値を観察すると、最大と最小で1ms程度の差は出ていますが、平均すると16.7ms程度のようです。つまり、毎秒60回であろうと推測できます。

なお、`Time` 型には `updates_per_second` という、毎秒の更新回数を直接表示してくれそうなメソッドがあります。これもデバッグ出力してみましょう。

```rust
fn update(app: &App, model: &mut Model, _update: Update) {
    println!("updates_per_second {:?}", app.duration.updates_per_second());
}
```

筆者の環境では、次のように表示されました（一部抜粋）。

```
updates_per_second 62.5
updates_per_second 62.5
updates_per_second 58.82353
updates_per_second 62.5
updates_per_second 62.5
```

この値は `since_prev_update` の値をもとに計算されているようで、どのくらい信頼できる値なのかはっきりしません。ただ、やはり更新頻度は毎秒60回くらいだろうとは推測できます。

## 生成される乱数を固定する

クリエイティブコーディングでは乱数を使うことがよくあります。Nannouでは乱数を簡単に利用できます。例えば次のようにします。

```rust
fn update(_app: &App, model: &mut Model, _update: Update) {
    let angle = random_range(0.0, 2.0 * PI);
}
```

この方法を使うと毎回異なる乱数が生成されます。通常はそれで問題ありませんが、あえて毎回同じ乱数を生成したい場合もあります。例えば、乱数を使ったアニメーションだが同じ動きを何度も再現したいという場合などです。同じ乱数を生成するには、乱数生成器のシード値を明示的に指定します。

```rust
use nannou::rand::prelude::*;

struct Model {
    rng: StdRng,
}

fn model(app: &App) -> Model {
    Model {
        rng: StdRng::seed_from_u64(53),
    }
}

fn update(app: &App, model: &mut Model, _update: Update) {
    let angle = model.rng.gen_range(0.0..2.0 * PI);
}
```

`StdRng::seed_from_u64` はシード値を指定して乱数生成器を生成する関数です。同じシード値を指定すると、生成される乱数が同じになります。シード値を変えると、生成される乱数も変わります。

上述のコードは筆者が作った次のコードからの抜粋です。

https://github.com/usami-k/coding-nannou/tree/main/2023/0002_randomwalk

動画キャプチャの方法を試行錯誤している際に同じ動きを何度も再現したかったので、この方法を使いました。

## まとめ

Rust Nannouを使うときのtipsを紹介しました。ゆめみ大技林を見てクリエイティブコーディングに興味を持った方の参考になれば幸いです。
