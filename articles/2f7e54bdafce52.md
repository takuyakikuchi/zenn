---
title: "CSR、SSR、SSGの違い"
emoji: "☘️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["csr", "ssr", "ssg"]
published: true
---

# CSR、SSR、SSGの違い
## CSR
クライアントサイドレンダリング。
**ブラウザ上でJavaScriptを実行してDOMを生成し、マウント後、コンテンツを表示させる方法。**
ページの初期ロードでは、コンテンツは何も表示されず、Hydration後に表示される。
Create React Appで作成されたReactアプリケーションはこの方法でレンダリングされる。

![No Pre-rendering](https://storage.googleapis.com/zenn-user-upload/777c5397d879-20220702.png)
(画像元：https://nextjs.org/learn/basics/data-fetching/pre-rendering)

## SSR
サーバーサイドレンダリング。
サーバーサイドでコンポーネントを評価、実行し、その結果をHTMLとミニマルなJavaScriptで配信する方法。
**サーバーへのリクエストがある度に、サーバー側で処理を行い、HTMLが生成する。**
Nuxt.js、Next.jsなどはこの方法でレンダリングされる。
初期ロードからコンテンツが表示され、その後Hydrationによって、インテラクティブな状態になる。(例、`<Link />` でジャンプ可能な状態に)
CSRよりもパフォーマンスに優れ、SEOにも良いとされる。

![Pre-rendering](https://storage.googleapis.com/zenn-user-upload/cad4eac3cad2-20220702.png)
(画像元：https://nextjs.org/learn/basics/data-fetching/pre-rendering)

![Server-side Rendering](https://storage.googleapis.com/zenn-user-upload/bc863f9d59a5-20220702.png)
(画像元：https://nextjs.org/learn/basics/data-fetching/two-forms)

## SSG
静的サイトジェネレーター。
SSRと同じく先にHTMLがサーバーサイドで生成される。
SSRとの違いは、**HTMLの生成タイミングがビルド時となり、リクエストがある度CDNよりコンテンツが配信される。**
ブログや、ヘルプサイト、Eコマースの商品リストなど静的なページで利用される。
HTMLの生成タイミングがビルド時になるため、SSRよりもパフォーマンスが良い。

![Static Generation](https://storage.googleapis.com/zenn-user-upload/248424db1225-20220702.png)
(画像元：https://nextjs.org/learn/basics/data-fetching/two-forms)

# 使い分けについて

**「ユーザのリクエストより事前に、ページをPre-renderingして良いか？」**
「イエス」の場合は、SSGを使用する。
「ノー」の場合は、SSRかCSRを使用する。
Next.jsはページごとに、SSGかSSRかを設定できる。

# 参照元
- [Pre-rendering - Pre-rendering and Data Fetching | Learn Next.js](https://nextjs.org/learn/basics/data-fetching/pre-rendering)
- [Two Forms of Pre-rendering - Pre-rendering and Data Fetching | Learn Next.js](https://nextjs.org/learn/basics/data-fetching/two-forms)
- [SSR、SSG、Client Side Renderingの違いをまとめた - Qiita](https://qiita.com/akashixi/items/84cd79e090a283bb8c67)

This article is also available in English: https://dev.to/takuyakikuchi/the-difference-between-csr-ssr-and-ssg-2e0c
