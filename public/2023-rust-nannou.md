---
title: Rust Nannouでクリエイティブコーディング
tags:
  - Rust
  - CreativeCoding
  - GenerativeArt
  - nannou
private: false
updated_at: '2023-11-11T10:55:31+09:00'
id: dd3681a047e56656146a
organization_url_name: yumemi
slide: false
ignorePublish: false
---
## クリエイティブコーディングとは

クリエイティブコーディングとは、何らかの表現を創造することを目的としたプログラミングのことです。ソフトウェア開発で行うプログラミングは一般的に何らかの機能を実現することを目的としますが、それとは異なっています。クリエイティブコーディングの愛好家たちによって、ビジュアルアートやサウンドアートなどの作品を制作する活動が行われています。アルゴリズムや数学的なルールに基づいて作品を生成するジェネラティブアートでも、クリエイティブコーディングの手法を使うことがあります。

クリエイティブコーディングのためのツールキットはいろいろ存在しています。たとえば、Processing[^processing]（JavaまたはPython）、p5.js[^p5js]（JavaScript）、openFrameworks[^openFrameworks]（C++）などが有名です。有名なツールキットを使えば、情報が多くサンプルコードも豊富です。一方で、プログラミングが好きな人ならば、自分の好きな言語でクリエイティブコーディングをしたいとも考えるでしょう。

[^processing]: https://processing.org/
[^p5js]: https://p5js.org/
[^openFrameworks]: https://openframeworks.cc/ja/

今回は、Rust言語でクリエイティブコーディングをするためのツールキットであるNannou[^nannou] を紹介します。なお、今回の記事ではRust言語の開発環境はすでに整っていることを前提とします。Rustの開発環境の構築については、Rust公式サイト[^rust]を参照してください。

[^nannou]: https://nannou.cc/
[^rust]: https://www.rust-lang.org/ja/

## はじめてのNannou

まずは実際に動かしてみましょう。`cargo` コマンドでプロジェクトを新規作成します。

```sh
cargo new nannou-sketch
```

生成された `Cargo.toml` の `dependencies` に `nannou` を追加します。

```toml
[dependencies]
nannou = "0.18.1"
```

`src/main.rs` を次のように編集します。

```rust
use nannou::prelude::*;

fn main() {
    nannou::sketch(view).run();
}

fn view(app: &App, frame: Frame) {
    let draw = app.draw();

    draw.background().color(WHITE);
    draw.ellipse().color(STEELBLUE);

    draw.to_frame(app, &frame).unwrap();
}
```

`cargo run` で実行すると、ウィンドウが表示されて中央に円が描画されます。

<img src="https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/rust-nannou_images/sketch1.png" width="320">

コードをざっと見てみましょう。まず、`main` 関数の中で `nannou::sketch` 関数を呼んでいます。`sketch` 関数は `view` 関数を引数にとり、`SketchBuilder` を返します。`SketchBuilder` の `run` 関数を呼ぶことで、ウィンドウが表示されます。

`view` 関数は `App` と `Frame` を引数に取ります。`view` 関数の中で `app.draw` 関数を呼んで `Draw` インスタンスを取得しています。`Draw` インスタンスの各種関数で図形描画を指示したあと、`to_frame` 関数で実際にレンダリングして出力します。

クリエイティブコーディングとしてのキモは、`view` 関数の中で図形を描画している部分です。`draw` の `background` 関数で背景を白で塗りつぶし、`ellipse` 関数で青い円を描画しています。この部分をいろいろと書き換えることで、さまざまな図形を描画して自分なりの表現を創造するわけです。

たとえば、`view` 関数を次のように書き換えると、右上に楕円が表示されます。

```rust
fn view(app: &App, frame: Frame) {
    let draw = app.draw();

    draw.background().color(WHITE);
    draw.ellipse()
        .color(STEELBLUE)
        .w_h(300.0, 200.0)
        .x_y(200.0, 150.0);

    draw.to_frame(app, &frame).unwrap();
}
```

なお、コンピューター画面の座標系では左上を原点とすることが多いですが、Nannouはそれとは異なります。Nannouの座標系は、中心を原点としてx軸の正の方向は右方向、y軸の正の方向は上方向です。数学での平面座標系と同様です。

## 描画に使う関数

基本的な図形の描画に使う関数をいくつか紹介します。

| 関数名 | 描画する図形 |
| --- | --- |
| `ellipse()` | 楕円 |
| `rect()` | 長方形 |
| `quad()` | 四角形 |
| `tri()` | 三角形 |
| `line()` | 線 |
| `polyline()` | 折れ線 |
| `polygon()` | 多角形 |

これらの関数はどれも `Drawing` インスタンスを返します。このインスタンスに対して `color` 関数で色を指定したり、`w_h` 関数で幅と高さを指定したり、`x_y` 関数で位置を指定したりできます。これらの関数は再び `Drawing` インスタンスを返すので、メソッドチェインで呼び出すことができます。

次のコードは長方形を描画する例です。実のところ、先ほどの楕円を描画する例とほどんど同じで、`ellipse` が `rect` に置き換わっただけです。

```rust
    draw.rect()
        .color(STEELBLUE)
        .w_h(300.0, 200.0)
        .x_y(200.0, 150.0);
```

また、次のコードは三角形を描画する例です。`points` 関数で頂点の座標を指定しています。

```rust
    draw.tri().color(STEELBLUE).points(
        pt2(150.0, 200.0),
        pt2(-100.0, 300.0),
        pt2(-50.0, -150.0),
    );
```

## アニメーション

ここまでは静止画を描画してきました。Nannouは画像のアニメーションもできます。

実は `view` 関数は実行中に繰り返し呼ばれます。`view` 関数の中で描画する図形を変えることで、アニメーションを実現できます。たとえば、`view` 関数を次のように書き換えると、円が動くアニメーションを表示できます。アプリケーションを起動してからの経過時間をもとに円の位置を変化させて描画しています。

```rust
fn view(app: &App, frame: Frame) {
    let draw = app.draw();

    let sin = app.time.sin();
    let sin2 = (app.time / 2.0).sin();
    let window = app.window_rect();
    let x = map_range(sin, -1.0, 1.0, window.left(), window.right());
    let y = map_range(sin2, -1.0, 1.0, window.bottom(), window.top());

    draw.background().color(WHITE);
    draw.ellipse().color(STEELBLUE).x_y(x, y);

    draw.to_frame(app, &frame).unwrap();
}
```

`app.time` でアプリケーションの起動からの経過時間を取得できます。`sin` 関数を使って、`-1.0` 〜 `1.0` の値を生成しています。`map_range` 関数で、`-1.0` 〜 `1.0` の値をウィンドウの端から端の値に変換しています。なお、`window_rect` でウィンドウの大きさを取得しています。

## 動画ファイルへの出力

アニメーションが実現できたら、それを動画ファイルとして保存したくなります。その方法を考えてみます。

ひとつは、Nannouの機能を利用する方法が考えられます。次のような関数を作って `view` 関数の中から呼び出すことで、各フレームを静止画ファイルとして出力できます。あとは、この静止画ファイルからFFmpeg[^ffmpeg] などのツールを使って動画ファイルを生成できます。

[^ffmpeg]: https://www.ffmpeg.org/

```rust
fn capture_frame(app: &App, frame: Frame) {
    let file_path = app
        .project_path()
        .unwrap()
        .join("frames")
        .join(format!("{:04}", frame.nth()))
        .with_extension("png");
    app.main_window().capture_frame(file_path);
}
```

ただ、この方法には欠点があります。アニメーションの描画処理とファイルへの出力処理を両方行うため処理が重くなり、場合によってはアニメーションが滑らかに動かなくなります。画面上の表示だけでなく、動画ファイルでも動きがかくついてしまいます。

そのような現象が起こる場合は、別の手段としてウィンドウの画面収録をするとよいでしょう。たとえばmacOSの場合、標準の画面収録機能（⌘+Shift+5）を使う、QuickTime Playerの画面収録機能を使う、サードパーティ製の画面収録アプリを使う、などの方法があります。

## Nannou App

ここまでは `nannou::sketch` 関数を使ってきましたが、もうひとつ `nannou::app` 関数を使う方法があります。

`nannou::app` 関数を使う方法は、`nannou::sketch` 関数よりはコード量が増えますが、Modelが使えるようになります。最初の例を `nannou::app` 関数を使って書き換えてみましょう。`src/main.rs` を次のように編集します。

```rust
use nannou::prelude::*;

fn main() {
    nannou::app(model).run();
}

struct Model {
    _window: window::Id,
    bg_color: Srgb<u8>,
    fg_color: Srgb<u8>,
}

fn model(app: &App) -> Model {
    let _window = app.new_window().view(view).build().unwrap();
    Model {
        _window,
        bg_color: WHITE,
        fg_color: STEELBLUE,
    }
}

fn view(app: &App, model: &Model, frame: Frame) {
    let draw = app.draw();

    draw.background().color(model.bg_color);
    draw.ellipse().color(model.fg_color);

    draw.to_frame(app, &frame).unwrap();
}
```

こちらもコードをざっと見てみましょう。まず、`main` 関数の中で `nannou::app` 関数を呼んでいます。`app` 関数は `model` 関数を引数にとり、`Builder` を返します。`Builder` の `run` 関数を呼ぶことで、ウィンドウが表示されます。

`model` 関数は `Model` 構造体を返します。このModelを使うことで、描画に使う値を保持できます。また、`model` 関数の中でウィンドウを生成しています。`nannou::sketch` の場合は明示的に生成する処理を書かなくてもウィンドウが生成されますが、`nannou::app` の場合は自分で処理を書きます。生成したウィンドウに対して `view` 関数を指定しています。

`view` 関数は `Model` を引数に取ります。先ほど生成したModelをここで使用します。このコードでは、描画に使う色をModelで保持して描画時に使用しています。

次に、アニメーションの例を書き換えてみましょう。先ほどのAppの例では、`model` 関数で `Model` を生成して、`view` 関数で `Model` を参照するだけでした。実行中に `Model` の値を更新するには `update` 関数を使います。

```rust
use nannou::prelude::*;

fn main() {
    nannou::app(model).update(update).run();
}

struct Model {
    _window: window::Id,
    bg_color: Srgb<u8>,
    fg_color: Srgb<u8>,
    x: f32,
    y: f32,
}

fn model(app: &App) -> Model {
    let _window = app.new_window().view(view).build().unwrap();
    Model {
        _window,
        bg_color: WHITE,
        fg_color: STEELBLUE,
        x: 0.0,
        y: 0.0,
    }
}

fn update(app: &App, model: &mut Model, _update: Update) {
    let sin = app.time.sin();
    let sin2 = (app.time / 2.0).sin();
    let window = app.window_rect();
    model.x = map_range(sin, -1.0, 1.0, window.left(), window.right());
    model.y = map_range(sin2, -1.0, 1.0, window.bottom(), window.top());
}

fn view(app: &App, model: &Model, frame: Frame) {
    let draw = app.draw();

    draw.background().color(model.bg_color);
    draw.ellipse().color(model.fg_color).x_y(model.x, model.y);

    draw.to_frame(app, &frame).unwrap();
}
```

`main` 関数の中が変わっています。`nannou::app` 関数は `Builder` を返しますが、それに対して `update` 関数を指定してから `run` 関数を呼んでいます。`update` 関数は `Model` を引数に取ります。この `Model` は `&mut` で借用しているので、`Model` の値を更新できます。

`view` 関数は `&mut` を使っていないため `Model` の値を更新できないことに注意してください。これによって、`Model` の値を更新する箇所と描画する箇所が自然に分離されます。そのため、ロジックが複雑になっても分かりやすくなります。これは `nannou:app` を利用するメリットのひとつです。

## 簡単な例：ランダムウォーク

ここまでの内容を踏まえた簡単な例として、ランダムウォークを実装してみましょう。ランダムウォークとは、行き先をランダムに決めて動き続ける運動です。物理現象のシミュレーションや、ジェネラティブアートの生成に使われます。

ここでは、一歩の長さが一定で方向を360度の全方向からランダムに決めるランダムウォークを考えます。運動の軌跡をアニメーションで描画する実装の例を次に示します。

```rust
use nannou::prelude::*;

fn main() {
    nannou::app(model).update(update).run();
}

struct Model {
    _window: window::Id,
    bg_color: Srgb<u8>,
    fg_color: Srgb<u8>,
    step_length: f32,
    start: Point2,
    end: Point2,
}

fn model(app: &App) -> Model {
    let _window = app.new_window().view(view).build().unwrap();
    Model {
        _window,
        bg_color: WHITE,
        fg_color: STEELBLUE,
        step_length: 10.0,
        start: pt2(0.0, 0.0),
        end: pt2(0.0, 0.0),
    }
}

fn update(_app: &App, model: &mut Model, _update: Update) {
    // 前回の終点を始点にする
    model.start = model.end;

    // 一歩の移動増分を求める
    let angle = random_range(0.0, 2.0 * PI);
    let vec = vec2(angle.cos(), angle.sin()) * model.step_length;

    // 一歩進んだ先の点を求める
    model.end = model.start + vec;
}

fn view(app: &App, model: &Model, frame: Frame) {
    let draw = app.draw();

    // 最初だけ背景を描画
    if app.elapsed_frames() == 0 {
        draw.background().color(model.bg_color);
    }

    // ランダムウォークの一歩を描画
    draw.line()
        .color(model.fg_color)
        .start(model.start)
        .end(model.end);

    draw.to_frame(app, &frame).unwrap();
}
```

`random_range` 関数を使って角度をランダムに生成しています。これは実行のたびに異なる結果が得られます。実行したときの例を次に示します。

<img src="https://raw.githubusercontent.com/usami-k/qiita-contents/main/images/rust-nannou_images/randomwalk.png" width="320">

この例をさらに発展させることも可能です。たとえば、ウィンドウの外に出ていかないようにする、時間経過にあわせて色を変化させる、移動する方向を限定するなどが考えられます。

## まとめ

Rust Nannouでクリエイティブコーディングをする方法を紹介しました。より詳しい情報はNannou公式のガイド[^nannou-guide]やAPIリファレンス[^nannou-ref]を参照してください。また、NannouのGitHubリポジトリ[^nannou-repo]にはサンプルコードもあります。

[^nannou-guide]: https://guide.nannou.cc/
[^nannou-ref]: https://docs.rs/nannou/
[^nannou-repo]: https://github.com/nannou-org/nannou/

## 宣伝

本記事は「[ゆめみ大技林 '23 (2)](https://techbookfest.org/product/2nuPxEDA5h1DQY26u38mrY)」に収録した記事です。他のメンバーの記事もありますので、ぜひご覧ください。
