# 🍚meCSS Practice

meCSS Practiceは、meCSSの実践的知見のパートです。meCSSをプロジェクトに取り入れるにあたって、遵守してもいいですし、参考程度に留めてもらっても構いません。

この内容は、著者の心変わりによって変わることがあるかもしれません。

## クラス命名規則系

### レイアウトブロックと通常ブロックを分けない

「レイアウト用のブロックに `l-` プレフィックスを付ける」というようなルールは無い方がいいでしょう。レイアウトブロックと通常ブロックという分類は存在せず、すべて通常ブロックだと考えます。

何がレイアウトで、何がレイアウトでないかは線引きが難しい問題で、人や場合によって適切に判断することが難しいためです。また、プレフィックスを見て「これはレイアウト用ブロックだ」ということが分かっても、あまりうれしいことはありません。

### 抽象化はとても慎重に行う

ブロック間の見た目や役割の類似性に着目し、共通項目を括り出したり上位概念を作ったりすることを抽象化といいますが、CSSを抽象化する場合はとても慎重に行わなくてはいけません。

一般にプログラミング言語には、共通した処理をまとめて記述する手段が用意されており、同じことを何度も書かないことを良しとする[DRY原則](https://ja.wikipedia.org/wiki/Don%27t_repeat_yourself)というものがあります。CSSでは逆に、あえて重複を許容することで複雑化に歯止めをかけられることがよくあります。

次のような目安をもって抽象化を行う・行わないの判断をするとよいでしょう。

- **抽象化する**
  - 🆗 項目の属性を表す「タグ」の画面要素が様々なブロックで使われていて、表示箇所によっては文字サイズに若干の違いはあるものの、役割は同一である。よって `Tag` ブロックとしてこれを共通化する。
  - 🆗 プロジェクトのカラーパレットが定義されていて、サイト全体を通してカラーパレットをきちんと守ってデザインが行われている。ゆえに色を管理する変数を定義する。
  - 🆗 やりたいことが明白で、ほぼ副作用がなく、何度も呼び出したいCSSプロパティのセットがある。そのため `clearfix`, `button-reset`, `visually-hidden` などのミックスインを作成する
- **抽象化しない**
  - 🆖 サイト全体を通して領域を左右に分割するブロックが数多くある。デザイナーはグリッドルールを意識的にデザインしているわけではないが、工数削減のために領域を分割している箇所はすべて `Grid` ブロックを使う。
    →**共通ブロックを用いるべきではない。**冗長でも、ブロックごとに領域分割のCSSルールを記述するべき。
  - 🆖 ボタンは色やサイズのバリエーションが様々だ。最もプレーンな基本ボタンを定義し、派生ボタンはSassの `@extend` 継承パターンを使って実装する。
    →**よほどでない限りSassの `@extend` は用いるべきではない。**

### エレメント名に迷ったら `__1` `__2`

```html
<div class="BlockName">
  <div class="BlockName__1"></div>
  <div class="BlockName__2"></div>
</div>
```

明瞭な名前をつけるに越したことはありませんが、難しい場合は `__1`, `__2` みたいなエレメント名でもよいものとします。

近年のHTMLは複雑化の傾向があります。レスポンシブデザイン実現のために、複数のデバイスで必要なHTML構造を最小公倍数的に用意しておく必要があるためです。このために増えたHTML要素それぞれに、納得ずくの名前を考えるのは難しいことです。

### 端的な名称

ブロック名をつけるときは、必ず「そのブロックは何を表しているのか？」母国語で（日本語で）説明してみることを心がけるとよいでしょう。そして、そのままブロック名に落とし込みます。「TextBox」「CenterBox」のような曖昧模糊とした名前付けは避けます。「ProductDetail」「CarouselA」「PasswordGenerator」など、その「モノ」を表す特徴的な単語を含めて名付けるようにします。デザイナーやクライアントとの間で共通言語になっている単語があれば、積極的に採用しましょう。

### むやみに単語を省略しない

単語を省略することは、わかりにくくなる一方でメリットがありません。頻出単語であったり、省略語のリストを別資料として残すのだとしても、単語は省略しないほうが賢明です。省略で節約できた数キロバイトは、たとえば画像を適切に圧縮するなどの簡単な工夫で節約できるバイト数に比べたら微々たるものです。

## CSSの書き方系

### ひとつのファイルの構成例

```scss
@charset "UTF-8";

.BlockName {
  @at-root {
    $_localVariable: 10px;

    .BlockName { ... }

    .BlockName__element1 { ... }

    .BlockName__element2 { ... }
  }
}
```

という感じに記述します。ローカル変数のためのスコープ（変数の効果が及ぶ範囲）を作るため、ブロック全体を `.BlockName {...}` で囲みます。 `@at-root` を入れ子にすることで、エレメント用セレクタの詳細度を小さく保ちます。

ローカル変数名はアンダースコアから始めます。ベタだけどやっぱりわかりやすいでしょう。

### `&__element` の書き方をしない

Sassの機能で、親のセレクタを使って `&__element` というような書き方ができますが、使用しません。制作時におけるデメリットが山ほどあるためです。例えば、セレクタによる検索ができなくなる。エディタによるリファクタリング機能が効かなくなる。使用箇所の一覧、定義箇所の参照ができなくなる、など。

親セレクタとの関係性を表す用途に `&` を使うことは許容します。

```scss
.Block1 {
  /* OK */
  & + .Block2 { ... }
}
```

### `@import` はアルファベット順

記述順に依存したスタイルの指定はできる限り避けるべきで、その実践としてブロック名をアルファベット順に読み込むという習慣は悪くないでしょう。

ただし、先述の第一級ブロックは、ほかの多数のブロックに内包される機会が多いため、先に読み込みます。第一級ブロックのアルファベット順→通常ブロックのアルファベット順→ページブロックのアルファベット順、に読み込むのがよいでしょう。

```scss
// Primary Blocks
@import "blocks/button";
@import "blocks/checkbox";

// Blocks
@import "blocks/header";
@import "blocks/main";
@import "blocks/paragraph";

// Page Blocks
@import "blocks/detail-page";
@import "blocks/home-page";
@import "blocks/inquiry-complete-page";
```

### スタイルを当てる*主体*に注目する（ブロック内ブロック編）

１ブロック＝１ファイルとした上で、`_hoge-block.scss` には「HogeBlockを主体とするスタイルを記述する」のが原則です。

次のようなケースを考えてみてください：「FugaBlockに内包されたときのHogeBlockのスタイルを指定する」。このとき、`.FugaBlock .HogeBlock {...}` というセレクタは、どちらのファイルに書くのが適切でしょうか？

多くの場合 `_hoge_block.scss` の中に書くのが適切です。そのとき `_hoge_block.scss` の中身を上から読み上げていくと、「HogeBlockは基本こんなスタイル。ただし**FugaBlockに内包されたときに限り**、スタイルちょっと変える」というふうになります。`_hoge_block.scss` は、HogeBlockに関する原則と例外を含みますが、あくまで**HogeBlockについての（を主体とした）スタイル**を宣言しているという点が重要です。

```scss
.HogeBlock {
  @at-root {
    // HogeBlockは基本こんなスタイル
    .HogeBlock { ... }
    .HogeBlock__element { ... }
    
    // ただしFugaBlockに内包されたときに限り……
    .FugaBlock {
      // スタイルちょっと変える
      .HogeBlock { ... }
      .HogeBlock__element { ... }
    }
  }
}
```

難しいですね？

### スタイルを当てる*主体*に注目する（モディファイア編）

- HogeBlockブロックがある
- HogeBlockは `-opened` モディファイアをとる
- `-opened` のとき、HogeBlockやHogeBlock__elementのスタイルが変わる

これをどのようにコード化したらよいでしょうか？

```scss
// 案１
.HogeBlock {
  @at-root {
    & { color: red; }
    &.-opened { color: blue; }

    .HogeBlock__element { display: none; }
    &.-opened .HogeBlock__element { display: block; }
  }
}

// 案２
.HogeBlock {
  @at-root {
    & { color: red; }
    .HogeBlock__element { display: none; }

    &.-opened {
      & { color: blue; }
      .HogeBlock__element { display: block; }
    }
  }
}
```

案１と案２、どちらを採用するべきかは、実際のところ場合によります。しかし理由をはっきりさせたうえで選択するとコード見通しが良くなるので、ひと呼吸おいて考えるようにしましょう。ポイントは、「モディファイアがつくことによって、そのブロックがどのように変化するか」の捉え方です。

案１は、「基本はこのスタイル、**ただし -opened モディファイアが付いたときには**こうなる」というニュアンスを表現するのに向いています。

案２は、モディファイアの有無によって、**ブロック内の様々なエレメントが見た目を変える**という性質を表現するのに向いています。

やっぱり難しいですね？　わかります。でも頑張りましょう。

## HTMLの書き方系

### `class` 属性を最初に書く

ソースコードの読みやすさと一貫性のため、`class` 属性を最初に書くことを推奨します。

属性に関するルールには、「`id` 属性値の方が先であるべき」「`a` に対する `href`、`img` に対する `src` のような必須な属性が真っ先にあるべき」「アルファベット順であるべき」など様々あります。

### ブロックの中のブロック

ブロックの中に別のブロックを配置する場合、いくつかパターンが考えられます。

```html
<!-- パターン１ -->
<div class="BlockA">
  <div class="BlockB">
    ...
  </div>
</div>

<!-- パターン２ -->
<div class="BlockA">
  <div class="BlockA__blockB BlockB">
    ...
  </div>
</div>

<!-- パターン３ -->
<div class="BlockA">
  <div class="BlockA__blockB">
    <div class="BlockB">
      ...
    </div>
  </div>
</div>
```

パターン３が最もよいでしょう。CSSの書き方が整理されるし、問題が起きても対処がしやすいです。モディファイアが所属するブロックやエレメントも自明になります。

### divやspanが増えることを恐れない

本来使われるべきタグの代わりにdivタグやspanタグを使うのは忌避されるべきですが、divタグやspanタグそのものには罪はありません。ちょっと難しいスタイルを実現する目的のためなら、５重くらいdivで囲むくらいまでならためらわずやってしまいましょう。divの数を一つでも減らしたいがために、不安定でわかりづらいスタイル指定をしてしまうことのほうが、避けるべきです。