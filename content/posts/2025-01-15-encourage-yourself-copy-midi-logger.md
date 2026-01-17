---
title: "ゴスペル曲のコピー作業：MIDI Logger改良のきっかけになった日"
date: 2025-01-15
categories: ["music"]
tags: ["gospel", "practice-log", "midi", "workflow", "obsidian-plugin"]
learned_date: 2025-12-29
draft: false
---

> 学習日: 2025-12-29 / 公開日: 2025-01-15

## 今日の作業

Donald Lawrenceの「Encourage Yourself」のコピー作業を約2時間。

{{< youtube JbEaftzaFWA >}}

## 環境セットアップで学んだこと

### MIDIキーボードの認識問題

MIDI LoggerがNORDを認識しない問題が発生。

**解決方法**: NORDのMIDIセッティングで、片っ端からUSB1に割り当てた。その後も認識しなくなることが何度かあったが、MIDIケーブルを抜いてもう一度さすと認識するようになる笑

### モニター配置の最適化

最初はポータブルモニターを鍵盤の前に置いてみたが、ABC Notationでtranscribeしようと思ったが、画面が小さくて、やりにくかった。

**現時点でのベストな配置**:
- デスクの大きいモニターを使用。MIDI鍵盤はデスクに直角においてあるのだが、音を取るくらいなら、デスクのモニタを見ながら、右手で入力できる。しかも、後日モニターアームを買って、MIdi鍵盤の方からモニターを見やすくした。
- 画面を二分割して、片方にABC Notationの五線譜表示を、片方にABC Notationの入力画面を表示
- 常時入力と出力の画面を出しておけば、ちょくちょく切り替えて、どの小節を作業してたか迷子にならない

## Obsidian Plug in MIDI Loggerの改良

この日の作業がきっかけで、Obsidian MIDI Loggerプラグインを改良することに。

元のプラグインは、ABCNotationなど書いていくときに、MIDI鍵盤からMIDI Note情報をCaptureできるというもので、（Note情報だけで、長さとかはCaptureしない）に「C Majorに換算した音をキャプチャするだけ」だったが、
**Key-aware版**を作成して、選択したキーに応じた臨時記号で出力できるようにした。

オリジナル版はKeyがGの曲を弾いている時にF#を弾くとF#としてキャプチャーしてしまうが、ABC Notationの観点からは、臨時記号なしのFでないといけない。逆にFナチュラルを引いた時にナチュラルの臨時記号をつけないといけない。それを解決した、**Key-aware版**を作ったことで、DbキーのEncourage youselfのtranscribeが格段にやりやすくなった。

改良版はGitHubで公開中:
- [ShioriLaws/midi-logger](https://github.com/ShioriLaws/midi-logger)

元のプロジェクト:
- [maybe-hello-world/midi-logger](https://github.com/maybe-hello-world/midi-logger)

プラグインの改良からGitHubでの公開まで、全て初めての体験だった。
ChatGPTと一緒にアプリを作るというのも初めてで、スモールスタートだけど、かなり達成感があった。

## 感想

「Encourage Yourself」は改めて難曲だと感じた。難しいのはキーやコード進行、拍子ではなく：

- 歌のRunの細かなニュアンス
- アレンジで音数を抑えつつ、ここぞという時にかっこいいリックを入れる技術

まだまだボキャブラリーを増やさないといけない。
コピー作業は、こうしたボキャブラリーを増やすために役立つと思う。

自分の困りごとを解決するためにプラグインを改良して公開できたのは、
Learning in Publicの良い例になった。
