---
layout: post
title:  "CHE READERというツールを作った"
excerpt: より進化して強くなった
date:   2020-08-06 10:30:00 +0900
---

[前回]({% post_url 2020-05-29-che %})

カルネージハートエクサ([詳細]({% post_url 2020-05-25-kernageheart %}))からエクスポートしたチームデータを閲覧できるツール。前回はNode.jsで動かすスクリプトでしたが、今回はブラウザ版です。

`gh-pages` で公開しています。[こちら](https://511v41.github.io/che-reader/)からどうぞ。リポジトリは[こちら](https://github.com/511V41/che-reader)

とりあえずざっくりと素朴に作って一区切りついたので公開しました。今後チマチマと手を加えていけたらいいかなと…。

## 進歩した点

- 誰でもブラウザで簡単に利用できるようになった
- チームのエンブレム画像を表示できるようになった

## 技術的なこと

データをデコードする部分はほとんど流用できたので、基本的にはTypeScriptでReactを書いてガワを新作したといったところです。

ただし追加で、エンブレム画像も表示できるようになりました。↓の「アフロ」というアイコンがそれです。

![エンブレム画像が取れている様子]({{site.baseurl}}/images/ss-2020-08-06.png)

エンブレム画像は32x32で、データとしては

- カラーパレット
- 各座標がカラーパレットのどれを使っているか

が入っているので、そのとおりにCanvasで描画することで表示できました。案外簡単。

カラーパレットはRGBが28〜228でAが0〜255というなんだか良くわからない感じだったので

```ts
// rgbは0〜200に修正済み
const r = (color.r / 200) * 255;
const g = (color.g / 200) * 255;
const b = (color.b / 200) * 255;
const a = color.a / 255;
context.fillStyle = `rgba(${r},${g},${b},${a})`;
```

こういう風にしました。

また、フォーメーション情報も取れてはいるのですが、まだ表示していません。これもCanvasで描画することになりそう。

ReactでCanvasを扱うのはちょっと面倒なことにならないかな？と不安でしたが `useRef` と `useEffect` を使えば案外すんなりいきました。React Hooks最高！

CardやButtonのようなコンポーネントには[Material-UI](https://material-ui.com/)を使っています。簡単に導入できて便利。TSのバンドルなどには[Parcel v2](https://v2.parceljs.org/)を使っています。parcelでTS書くの本当にDXが良いのでオススメ。

今後改善したい点はパフォーマンスですね。というのも、このツールは基本的に「たくさんのチームデータから目当てのものを探す」ために使うものなので、大量のファイルを捌けないと不便です。なんとかします。

なんだかカルネージハート自体を遊ばずに周辺ツールばかり作っている気がしますが…。とりあえず以上。
