---
title: "OpenClawをAWSで試しながらターミナルと格闘した話"
date: 2026-01-31
categories: ["knowledge-management"]
tags: ["openclaw", "aws", "terminal", "linux", "tmux", "ssh"]
draft: false
---

## OpenClawって何？

最近、[OpenClaw](https://openclaw.ai/)という個人向けAIアシスタントツールを試している。

OpenClawは、自分のPC（またはクラウド）で動くAIアシスタントで、DiscordやTelegramなどのチャットアプリから操作できる。ブラウザ操作、ファイル操作、シェルコマンド実行など、かなり幅広いことができる。

LLMは、ChatGPTやClaude、その他のモデルをサブスクやAPIを選んで使うことができ、いわゆる脳みそ自体は同じなんだけど、何か記憶力をよくする仕組みがついているらしい（メモリをファイルに保存する仕組みがあるとか？よくわかってないけど）。

ちなみに、ClawdBotという名前でローンチされたが、Claudeに名前が似すぎているとAnthropicから物言いがついて、MoltBot（Molt＝脱皮。元々ザリガニ🦞のClawFishにかけていたらしい）に改名。そこから2、3日でOpenClawに改名という経緯がある。

コミュニティでは、このBotにお金を渡して株取引させたり、営業メールを自動でやり取りさせたり、「寝てる間にアプリ作っといて」と指示すると朝には完成しているとか、近未来な使い方が話題になっている。今週はなんとBOT専用のSNSができて、そこでBOT同士が会話を始めたとか、面白くて目が離せない。

## なぜAWSで？

OpenClawは自分のPCでも動くみたいだけど、自分が理解していないものをメインPCに入れたら、大惨事になりかねない。ガチでわかって使ってる人たちの間では、Mac miniを買ってローカルで常時稼働させるのが流行ってるらしいが、私は素人がほんのちょっと触ってみたいという程度なので、とりあえず常時稼働できるAWS EC2のFree Tierで動かすことにした。

そして、ここから想定外の学びが始まる。

### ターミナル、苦手でした

昔から「ターミナル使えるようになりたい」とは思っていた。Linuxも触りたいと思っていた。でも、できた試しがなかった。

今回、AWS上でOpenClawを動かすために、**ターミナル操作が避けられない**状況になった。強制的に勉強するハメに。

でも、面白いからいいか。

## やったこと

### 1. SSH接続の基本

まず、AWS EC2インスタンスにSSH接続する必要がある。

```bash
ssh -i ~/.ssh/your-key.pem ubuntu@[your-ec2-ip]
```

これで接続できるんだけど、すぐに切れる。作業中にSSHが切れるとかなり困る。

### 2. SSH接続を切れにくくする

調べたら、`ServerAliveInterval`というオプションで接続を維持できることがわかった。

```bash
ssh -o ServerAliveInterval=30 -o ServerAliveCountMax=3 -i ~/.ssh/your-key.pem ubuntu@[your-ec2-ip]
```

でも、これでもSSHが切れることはある。そこで登場するのがtmux。

### 3. tmuxでセッションを保持

**tmux**は、サーバー側でセッションを保持してくれるツール。SSHが切れても作業が残るので、リモート作業には必須級らしい。

```bash
# tmuxをインストール
sudo apt update
sudo apt -y install tmux

# 新規セッション開始
tmux new -s moltbot

# セッションから一時離脱
Ctrl+b → d

# 再接続
tmux attach -t moltbot
```

SSH接続が切れても、再度SSHで繋いで`tmux attach`すれば、作業中の画面がそのまま残っている。これは便利。

#### tmuxの基本操作

| 操作 | コマンド/キー |
|------|--------------|
| 新規セッション | `tmux new -s [名前]` |
| 再接続 | `tmux attach -t [名前]` |
| 一時離脱 | `Ctrl+b` → `d` |
| ペイン分割（上下） | `Ctrl+b` → `"` |
| ペイン分割（左右） | `Ctrl+b` → `%` |
| ペイン移動 | `Ctrl+b` → 矢印キー |

### 4. シェルスクリプトを作る

ローカルMacからAWSにファイルを同期する作業を、毎回手打ちでやるのが面倒だったので、シェルスクリプトにした。

```bash
# ローカルMacで
mkdir -p ~/bin
nano ~/bin/sync-script.sh

# 1行目に必ず書く
#!/bin/bash

# 続けてrsyncコマンドなどを書く
rsync -avz --delete ~/local/path/ ubuntu@[your-ec2-ip]:/remote/path/

# 保存: Ctrl+O → Enter
# 終了: Ctrl+X

# 実行権限を付与
chmod +x ~/bin/sync-script.sh
```

これで、`~/bin/sync-script.sh`と打てばスクリプトが走る。

#### どこからでも実行できるようにする

PATHを通せば、スクリプト名だけで実行できる。

```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# これだけで実行可能に
sync-script.sh
```

### 5. SSH configとエイリアスで楽をする

毎回`ssh -i ~/.ssh/your-key.pem ubuntu@[your-ec2-ip]`と打つのは面倒すぎる。SSH configファイルを作ることで短縮できる。

```bash
nano ~/.ssh/config

# 以下を追加
Host openclaw
  HostName [your-ec2-ip]
  User ubuntu
  IdentityFile ~/.ssh/your-key.pem

# 保存・終了
# 権限を設定
chmod 600 ~/.ssh/config
```

これで、`ssh openclaw`だけで接続できるようになった。

#### さらにエイリアスでtmuxに自動接続

`.zshrc`にエイリアスを追加して、SSH接続と同時にtmuxセッションに入れるようにした。

```bash
nano ~/.zshrc

# 最後に追加
alias claw='ssh -t openclaw "tmux attach -t moltbot || tmux new -s moltbot"'

# 保存・終了
source ~/.zshrc
```

これで、ターミナルで`claw`と打つだけで、SSH接続してtmuxセッションに入れる。`Ctrl+b` → `d`で離脱すれば、元のローカル環境に戻る。

めちゃくちゃ楽になった。

## 理解できたこと

作業しながら、今まで曖昧だったことが整理できた。

### OS / シェル / コマンド / エディタの階層

最初、「tmuxってエディタだと思ってた」「nanoがコマンドだと思ってた」みたいな混乱があった。でも、この図で理解できた：

```
[OS]
 └─ [シェル] zsh / bash
      └─ [コマンド] ssh, rsync, tmux
           └─ [エディタ] nano, vim
```

- **OS**: macOS、Ubuntuなど
- **シェル**: コマンドを受け付ける窓口（zsh、bashなど）
- **コマンド**: シェルから実行するツール（ssh、tmux、rsyncなど）
- **エディタ**: ファイルを編集するツール（nano、vimなど）

tmuxはコマンドだし、nanoもコマンド。階層が違う。

### コマンド履歴の検索

ターミナルでは、`Ctrl+R`で過去のコマンドをインクリメンタル検索できる。でも、なかなか目当てのコマンドが出てこなかった。

結局、SSH configとエイリアスで解決したから、履歴検索の問題は消えた。

## 次のステップ

現状、SSH接続とtmuxの基本は押さえた。次は：

- tmux上でOpenClawが生成したMarkdownファイルを読めるようにする
- `cat`や`less`でファイルを確認する方法を覚える
- 実際にOpenClawを運用してみて、使い勝手を確かめる

## まとめ

OpenClawを試すつもりが、思わぬ形でターミナル/Linuxの基礎を学ぶことになった。

今まで「いつかやりたい」と思っていたことが、実際に必要になったことで一気に学べた。やっぱり、**必要に迫られると学習が加速する**。

tmux、SSH config、エイリアス、シェルスクリプト。どれもまだ使いこなせてないけど、少しずつわかってきた。

素人なので安全に注意しながら、少しずつ進めていく。

---

**関連記事:**
- [AIプロジェクト管理エージェント続編](/posts/2026-01-22-project-management-ai-agent-part2/)
