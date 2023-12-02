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
organization_url_name: yumemi
slide: false
ignorePublish: false
---
先月に技術書典15や技書博9で頒布した[『ゆめみ大技林 '23 (2)』](https://booth.pm/ja/items/5237542)で、Rustでクリエイティブコーディングをする記事を書きました。この内容はQiitaにも投稿しています（[Rust Nannouでクリエイティブコーディング](https://qiita.com/usamik26/items/dd3681a047e56656146a)）。

そこで紹介したNannouについて、本記事ではちょっとしたtipsを紹介します。ゆめみ大技林を見てクリエイティブコーディングに興味を持った方の参考になれば幸いです。

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

この際に、次のように `size` を使ってウィンドウのサイズを指定できます。これは私が作った [0003_grid-art](https://github.com/usami-k/coding-nannou/tree/main/2023/0003_grid-art) のコードの抜粋です。ウィンドウを408x408の正方形のサイズにしています。

```rust
fn model(app: &App) -> Model {
    let _window = app
        .new_window()
        .size(408, 408)
        .view(view)
        .build()
        .unwrap();

    // 後略
}
```

クリエイティブコーディングでは座標指定での描画をよく使うので、必要に応じてウィンドウサイズを指定すると便利です。

## キーが押されたときの処理を書く

Nannouでは、単に描画するだけでなく、ユーザーの入力を受け付けることもできます。例えば、キーが押されたときに何か処理をしたい場合は、次のように `key_pressed` を使います。

```rust
fn model(app: &App) -> Model {
    let _window = app
        .new_window()
        .key_pressed(key_pressed)
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

私は、スペースキーが押されたときにアニメーションを開始する処理をよく書いていて、例えば [0001_randomwalk](https://github.com/usami-k/coding-nannou/tree/main/2023/0001_randomwalk) でも書いています。これは、作成したアニメーションを動画としてキャプチャしたいときに使っています。ウィンドウを表示して動画キャプチャの準備を整えたら、スペースキーを押してアニメーションを開始するという流れです。

## 更新処理がどのくらいの頻度で呼ばれるか確認する

Nannouの `update` 関数や `view` 関数は実行中に繰り返し呼ばれます。これによってアニメーションが実現できるわけです。このとき、これらの関数がどのくらいの頻度で呼ばれているかは気になる点でしょう。次のようにデバッグ出力すると確認できます。

```rust
fn update(app: &App, model: &mut Model, _update: Update) {
    println!("duration {:?}", app.duration);
}
```

私の環境では、次のように表示されました（一部抜粋）。

```
duration Time { since_start: 1.098403958s, since_prev_update: 16.951666ms }
duration Time { since_start: 1.114849167s, since_prev_update: 16.445209ms }
duration Time { since_start: 1.131892958s, since_prev_update: 17.043791ms }
duration Time { since_start: 1.148883375s, since_prev_update: 16.990417ms }
duration Time { since_start: 1.164966542s, since_prev_update: 16.083167ms }
```

`app.duration` はNannouの [Time](https://docs.rs/nannou/latest/nannou/state/time/struct.Time.html) 型の値で、`since_start` はプログラムが開始してからの経過時間、`since_prev_update` は前回の更新からの経過時間です。上記のログで `since_prev_update` の値を観察すると、最大と最小で1ms程度の差は出ていますが、平均すると16.7ms程度のようです。つまり、毎秒60回であろうと推測できます。

なお、`Time` 型には `updates_per_second` という、毎秒の更新回数を直接表示してくれそうなメソッドがあります。これもデバッグ出力してみましょう。

```rust
fn update(app: &App, model: &mut Model, _update: Update) {
    println!("updates_per_second {:?}", app.duration.updates_per_second());
}
```

私の環境では、次のように表示されました（一部抜粋）。

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

この方法だと、毎回異なる乱数が生成されます。乱数を使うときに、あえて毎回同じ乱数を生成したい場合があります。

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

https://github.com/usami-k/coding-nannou/tree/main/2023/0002_randomwalk



