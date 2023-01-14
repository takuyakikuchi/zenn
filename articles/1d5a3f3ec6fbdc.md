---
title: "Game Changer? :has() の到来"
emoji: "🛸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [css]
published: true
---

# この記事について

この記事は 2023/01/13 に行われた、「WebフロントエンドなんでもLT 会 #7」で私が発表した LT の内容を記事にまとめたものです。

https://ncdc-dev.connpass.com/event/265595/

内容は、2022年に実装された `:has()` についてです。
本記事で登場する、サンプルコードやデモは、Chrome Developers Blog の *:has(): the family selector* より引用させていただきました。
以下内容は、オリジナルのブログの内容をぎゅっとまとめ、筆者なりに整理したものです。

https://developer.chrome.com/blog/has-m105/

# `:has()` is 何? 🤔

`:has()` は Parent Selector、いやそれだけに止まらない Family Selector 👨‍👩‍👧‍👦 だ！と紹介があります。

どいうことかというと、`:has()` 以前の CSS セレクターの指定の方向は、横方向 ➡️、またはほとんどの場合が下方向 ⬇️ でした。

```css
/* special クラスをもっている <li> 要素へ焦点を当てるとき(横方向) */
li.special { … }

/* <article> 要素の内側にある <p> 要素の <span> 要素に焦点を当てるとき(下方向)  */
article p span { ... }

/* <h1> 要素の直後に来る <ul> 要素の、そのまた直後に来る <p> 要素に焦点を当てるとき(横方向) */
h1 + ul + p { ... }
```

`:has()` の到来により、上方向 ⬆️ に親要素を選択可能になります 🙌
例を見てみましょう。

```html: foo.html
/* 👇 この .everybody を指定したい  */
<div class="everybody">　
  <div>
    <div class="a-good-time"></div>
  </div>
</div>

/* 🙅🏻 これは指定しない  */
<div class="everybody"></div>
```

`:has()` 以前では、例えば以下のように `.everybody` の位置指定によってターゲットするかと思います。(横方向)

```css: without-has.css
.everybody:first-of-type { ... }
```

`:has()` を使うと、以下のように指定ができます 🪄✨
(`.a-good-time` から**上方向**に `.everybody` を指定)
```css: with-has.css
.everybody:has(.a-good-time) { ... }
```

構文は以下の通りです。
```css
/* 子・孫要素の条件を指定して、親要素を選択する */
<target>:has(<condition>) { <styles> }
```

# いつ使うの? 🤔

`:has()` が便利そうなのは理解いただけたかと思いますが、まだユースケースがパッとしていないかもしれません。

ここからは Chrome Developers Blog の [:has(): the family selector](https://developer.chrome.com/blog/has-m105/) の中で紹介されているいくつかのユースケースを取り上げ、使い方についてイメージを膨らませていきたいと思います。

:::message
本記事の以下のサンプルコードは、上記ブログで紹介されているサンプルコードを簡略化したものです。
:::

## Cards

@[codepen](https://codepen.io/web-dot-dev/pen/JjLJyWx)

複数の Card を Grid レイアウトで表示している例です。
Card のバナー有無や、写真の有無でレイアウトが異なっています。

このスタイルを表現するのは、
「レイアウトの違う Card に対して、 class を追加して...🤔」などど考えそうですが、
**思考プロセスを転換**し、`:has()` を使ったアプローチを採用することでよりシンプルに表現できます。

```html: foo.html
<!-- li 要素自体は同じ構成 -->
<ul>
  <li class="card"> ... </li>
  <li class="card"> ... </li>
  <li class="card"> ... </li>
  <li class="card"> ... </li>
  <li class="card"> ... </li>
</ul>
```

子・孫要素に `.card__banner`("FEATURED ⭐️"タグ) や、 `.card__media`(写真)があるかどうかを `:has()` で条件指定することで、**class を余分に追加することなく**ターゲットの `.card` に対してスタイリングをします。
以下例では、レイアウトの違いを `has:()` を使って作り出しています。
```css: bar.css
/* バナーを持つ場合は、Grid の横幅いっぱいの大きさに */
.card:has(.card__banner) {
  grid-row: 1;
  grid-column: 1 / -1;
}
/* 写真ありの時の Grid レイアウトの指定 */
.card:has(.card__media) {
  grid-template-columns: 1fr 1fr;
  grid-template-rows: repeat(3, auto);
}
.card__media {
  grid-column: 2;
  grid-row: 1 / -1;
}
```

思考プロセスの転換が必要ですが、`:has()` を使いこなすことでよりコードがスッキリしそうですね ✨

## Forms

@[codepen](https://codepen.io/web-dot-dev/pen/ZExyJKx)

続いてはフォームです。
フォームの入力値の状態に合わせて、異なるスタイリングを適用している例です。
JavaScript を使わず実現されています。

```html: foo.html
<form action="">
  <div class="form-group">
    <label for="email" class="form-label">
      <span class="sr-only">Email</span>
    </label>
    <div class="form-group__input">
      <input
        required type="email" id="email" class="form-input"
        pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,4}$"
        title="Enter valid email address"
        placeholder="Enter valid email address"/>
      <div class="form-group__error">Enter a valid email address</div>
    </div>
  </div>
</form>
```

`:invalid` 、 `:focus`、 `:valid`、 `:placeholder-shown` などの擬似クラスを `:has()` のコンディションにして異なるスタイリングを表現しています。

```css: bar.css
/* invalid なフォームのカラーを変える */
.form-group:has(:invalid) {
  --color: var(--invalid);
}
/* フォーカスが外れているかつ、プレースホールダーが表示されていない、エラーメッセージを表示する */
.form-group:has(:invalid:not(:focus):not(:placeholder-shown)) .form-group__error {
  display: block;
}
```

上記のエラーメッセージの表示を制御するスタイリングのように、`:has()` の条件をいくつか組み合わせる使い方は強力です！ 🏋️

## Content

@[codepen](https://codepen.io/web-dot-dev/pen/abYwyWQ)

この例では、画像、イラスト、グラフ、コードスニペットなど、自己完結型のコンテンツを表す `<figure>` 要素に、 `<figcaption>` 要素(コンテンツを説明するキャプション)が含まれているかどうかで、レイアウトを変更しています。

```html: foo.html
<main>
  <article>
    <h1>Some Awesome Article</h1>
    <p> ... </p>
    <figure>
      <img  alt="" width="200" height="200"
         src="https://assets.codepen.io/605876/team-awesome.png"
      />
    </figure>
    <p> ... </p>
    <figure>
      <img  alt="" width="200" height="200"
         src="https://assets.codepen.io/605876/team-awesome.png"
      />
      <figcaption>Shot of the CSS, UI, and DevTools Chrome Dev</figcaption>
    </figure>
    <p> ... </p>
  </article>
</main>
```

```css: bar.css
figure:not(:has(figcaption)) {
  float: left;
  margin: 2rem 2rem 2rem 0;
}
```

「figure が figcaption を持っていたら...」と直感的かつシンプルで分かりやすいですね 👍

## Reacting to State

@[codepen](https://codepen.io/web-dot-dev/pen/YzaQxQK)

マークアップの状態に応じて、スタイルを反応させるスライド Navbar の例です。
ハンバーガーメニューのボタンに対して、クリックイベントで `aria-expanded` 属性を変更しています。

```js: foo.js
const NAV_CONTROL = document.querySelector('.nav-control')

const CONTROL_NAV = () => {
  NAV_CONTROL.setAttribute('aria-expanded',
    NAV_CONTROL.matches('[aria-expanded="false"]') ? true : false)
  NAV_CONTROL.setAttribute('aria-pressed',
    NAV_CONTROL.matches('[aria-expanded="false"]') ? true : false)
}

NAV_CONTROL.addEventListener('click', CONTROL_NAV)
```

CSSでは、`aria-expanded` 属性の値をコンディションとして、CSS のカスタムプロパティを変更し、`body` 要素をシフトさせています。

```css: bar.css
body {
  transform: translateX(calc(var(--open) * -200px));
}
:root:has([aria-expanded="true"]) {
  --open: 1;
}
```

「JavaScript でクラス名を追加...」ではなく、
JavaScript でマークアップの更新し、CSS はその情報を `:has()` のコンディションとして使ってスタイリングを実現する。
これも `:has()` による発想の転換です 💡


## 既成概念にとらわれない発想

CSSアートなど、CSSのみでできることの幅については驚かされることが多いですが、以下も html と css だけで作られたゲームです 🤯　`:hover` を利用しています。

`has()` 以前にも、`+` や`~` などの結合子を駆使した"トリック 🧙‍♀️" を使うことで、このような作品を作ることはできているのですが、`:has()` の到来により、これらの"トリック 🧙‍♀️"の難易度がより下がったのではないかと思います。

:::message
以下のデモは、ブラウザの別タブで試してみてください。
:::


@[codepen](https://codepen.io/web-dot-dev/pen/rNdwzwK)

# まとめ

`:has()` で親要素を選択可能になりました 🙌

```css
/* 子・孫要素の条件を指定して、親要素を選択する */
<target>:has(<condition>) { <styles> }
```

## `:has()` のメリット
- クラス名の苦悩(タイポ・命名)から抜け出せます。コードがスッキリ 🧹✨
- `:has()` のユースケースはかなり幅広そう 😼
- `:has()` が新たな発想を生み出すゲームチェンジャーとなる(!?) 👩🏻‍🔬 
  - よりシンプルな要素指定が可能になったことにより、CSS でやれることの幅が広がる・ハードルが下がる
  - JavaScript 肥大化を防ぐ一手となる(!?)

# ブラウザ対応状況

:::message alert
Firefox はまだ標準対応外なので注意です。
(執筆時点: 2023/01/13)
:::

![](https://storage.googleapis.com/zenn-user-upload/170e8abb8cdf-20230108.png)

https://developer.mozilla.org/en-US/docs/Web/CSS/:has

# 参照

はじめにも書きましたが、本記事の内容は以下の Chrome Developers Blog の *:has(): the family selector* よりサンプルコードやデモを引用させていただきました。
より詳しく理解したい方は、ぜひこちらをご覧ください。

https://developer.chrome.com/blog/has-m105/

This article is also available in English: https://dev.to/takuyakikuchi/game-changer-the-arrival-of-has-boe