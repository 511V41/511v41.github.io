---
layout: post
title:  "SNES開発環境構築メモ"
date:   2019-11-19 10:00:00
---

macOS Mojave version 10.14.5

## コンパイラ

SNESは65816なので、cc65/ca65かWLA DXでアセンブラをやっていくことになる。

cc65はhomebrewで一発で入るので便利だが、先人の[ポルノアニメ](http://gyuque.hatenablog.com/)曰く、

>[cc65/ca65](http://www.cc65.org/)  というものを使っている。名前を見るとCで書けそうだけど、それは6502（初代ファミコン）用のコードだけで、65816のコードはアセンブリで書く必要がある。つまり実際に使うのはca65の方だけ。  
スーファミには、メインCPUの65816とは別にサウンド制御用のコントローラ「SPC700」が載っていて、これのアセンブラとして「[bass](https://byuu.org/tool/bass/)」を使っている。本体側とSPC側で文法が変わってしまうが、SPC側はそんなに巨大なシステムを作るわけではないので、慣れない感じで書いても大丈夫。
>しかし最近知ったのだけれど、[WLA DX](http://www.villehelin.com/wla.html)という凄いヤツがあるらしい。極めて多機能で、本体側だけではなくSPC側もこれ一本で書ける。おまけにスーファミ以外のハードも書ける。まあでも、cc65+bassで構築してしまったので今更もういいかな……  
今から始める人はこっちがいいかもしれない。

とのことなので、WLA DXでやっていこうと思う。

インストールは

```sh
mkdir tmp
cd tmp
git clone git@github.com:vhelin/wla-dx.git
cmake -G Unix\ Makefiles ./wla-dx/
make
make install
```

で簡単に入る。以降は `wla-65816` というコマンドを使う。

## エミュレータ

素直にSnes9xで良いと思われる。[公式サイト](http://www.snes9x.com/downloads.php)にミラーが乗っているので、好きなところからダウンロードして `Snes9x.app` をアプリケーションに突っ込めば良い。

## VSCodeの拡張

「65816」で検索するといくつか出てくるが、とりあえずシンタックスハイライトだけ欲しかったので[65816 Assembly](https://marketplace.visualstudio.com/items?itemName=joshneta.65816-assembly)をインストールした。

## Hello, World

[jurgiles/snes-dev](https://github.com/jurgiles/snes-dev) をcloneしてきてビルドして、Snes9xで起動できればOK… と思ったのだが、問題発生。

本来なら緑色の背景が映るというだけのはずだが、実際に映ったのは緑色と黒色の縞々が高速で上に向かって動き続けるという画面…。

とはいえ、ポルノアニメが作った [SNESZoi](https://github.com/gyuque/SNESZoi) なんかは正常に描画されるので、さて何がおかしいのやら…。

正解は [jurgiles/snes-dev](https://github.com/jurgiles/snes-dev) に含まれている `helloworld.asm` がおかしい。

```asm
; SNES Initialization Tutorial code
; This code is in the public domain.

 .include "Header.inc"
 .include "Snes_Init.asm"

 ; Needed to satisfy interrupt definition in "Header.inc".
 VBlank:
   RTI

 .bank 0
 .section "MainCode"

 Start:
     ; Initialize the SNES.
     Snes_Init

     ; Set the background color to green.
     sep     #$20        ; Set the A register to 8-bit.
     lda     #%10000000  ; Force VBlank by turning off the screen.
     sta     $2100
     lda     #%11100000  ; Load the low byte of the green color.
     sta     $2122
     lda     #%00000000  ; Load the high byte of the green color.
     sta     $2122
     lda     #%00001111  ; End VBlank, setting brightness to 15 (100%).
     sta     $2100

     ; Loop forever.
 Forever:
     jmp Forever

 .ends
```

これに書き換えて動作確認。うまく行きました。

一応[PR](https://github.com/jurgiles/snes-dev/pull/1)投げときました。
