---
layout: post
title:  "シンプルなhttpサーバを建てたい"
excerpt: "cors対応したい場合があるよね"
date:   2020-01-06 10:00:00 +0900
---

手元にあるファイルを見れるようなhttpサーバを建てたいとき、今までは

```sh
python -m SimpleHTTPServer
```

としていたんだけど、これだとCORSに対応できない。やろうとしたらpythonでスクリプト書かなきゃいけないのだ。

それは面倒。なので…

```sh
npm install -g http-server
http-server --cors
```

こうした。

のだけれど、結局もっと覚えやすい名前でalias張ったりするわけだから(するよね？)素直にスクリプト書いても良かった気がする…。
