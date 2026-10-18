---
title: "Kindle購入者特典をGitHubで配る：リンクが切れない特典リポジトリの作り方"
emoji: "🐈"
type: "tech"
topics: ["github", "kindle", "kdp", "git", "個人開発"]
published: false
---

Kindle本に「購入者特典（ツールやサンプル）」を付けたいとき、ファイルの置き場所で迷っていませんか。
この記事を読むと、**本の改訂でリンクが切れず、著者のメモが読者に漏れない特典リポジトリ**を、手順どおりに公開できるようになります。
対象は、GitHubアカウントは持っているが「特典配布用に一から組むのは初めて」という個人出版者の方です。

:::message
この記事は、実際に公開した特典リポジトリ（単一HTMLツール＋学習サポート資料）の構成をもとに書いています。コマンドはすべて実行して確認したものだけを載せています。
:::

## できあがり（結論）

- QRコードは**リポジトリのトップ**を指す（ファイル名を変えても切れない）
- 公開フォルダには**読者向けだけ**を置く（価格戦略や出版ToDoは入れない）
- 本を改訂しても、**入口（README）から辿らせる**のでリンクが腐らない

## なぜ「置き方」で差がつくのか（WHY）

特典配布でありがちな失敗は3つです。

1. **QRをファイルの直リンクにしてしまう**
   `.../raw/main/tool/ツール.html` を直接指すと、ファイル名を変えたり版を上げた瞬間にQRが死にます。本は刷り直せません。
2. **著者の作業メモを一緒に公開してしまう**
   出版ToDo・発売記念価格・残作業チェックリストがPublicリポジトリに入っていると、**全世界の読者にそのまま見えます**。
3. **日本語ファイル名を直リンクで配る**
   URLエンコードされ、共有時に化けたり読み取りづらくなります。

対策はどれも同じ発想です。**入口を1つ（リポジトリのトップ＝README）に固定し、そこから辿らせる。** 中のファイルは自由に変えても、入口さえ生きていればリンクは切れません。

## 準備するもの（前提）

- GitHubアカウント（無料でOK）
- Git（`git --version` で確認。なければ [git-scm.com](https://git-scm.com) から）
- 任意：GitHub CLI（`gh`）。あればリポジトリ作成〜pushが1コマンド
- 配布したい特典ファイル一式

```bash
$ git --version
git version 2.43.0        # 2.x 系ならOK
```

## 手順1：公開フォルダを「読者向けだけ」で組む（WHAT）

まず、公開するフォルダを作ります。ポイントは**読者向けと著者専用を混ぜない**こと。

```
kuroneko-himekuri-tokuten/     ← これを公開する
├─ README.md                   入口。ここから全部へ案内する
├─ 利用規約.md                 改変可・無料配布可・再販禁止 などの方針
├─ tool/
│  └─ 黒猫先生の日めくり_v2_2_PRO_Kindleお客様用.html
├─ docs/                       学習サポート資料（穴埋めシート・用語じてん 等）
└─ artifacts/                  追加のミニツール（読書手帖ジェネレーター 等）
```

:::message alert
`publish/`（出版ToDo・ローンチ価格・リポジトリ設定ガイド）や `test/`（QA資料）など、**著者の作業用フォルダは公開フォルダに入れない**でください。別の場所（手元 or プライベートリポジトリ）に残します。
:::

手元では一緒に管理したい、という場合は `.gitignore` で公開から外します。

```bash
# .gitignore（公開したくないものを列挙）
publish/
test/
*.zip
.DS_Store
```

## 手順2：READMEを入口にする

READMEは「迷わせない案内板」です。最低限これだけは書きます。

```markdown
# 黒猫先生の日めくり ― 購入者特典

書籍の購入者向け特典です。ダウンロードして、ブラウザで開くだけで動きます。
インストール不要・オフライン対応。

## ツールの入手
- GitHub版：`tool/黒猫先生の日めくり_v2_2_PRO_Kindleお客様用.html`
- すぐ試す（アーティファクト版）：（公開リンク）

## うまく動かない時
1. ファイル名の末尾が `.html` か確認
2. ブラウザを再読み込み
3. `docs/エラー翻訳集.md` を参照

## ライセンス・注意
個人利用での改変・職場での無料配布は可、再販は禁止。詳しくは `利用規約.md`。
```

:::message
READMEに `[LinkedIn](...)` のようなリンクを置くなら、**公開前に実URLを入れる**こと。`【LinkedIn URL】` のような空欄のまま公開すると、読者にそのまま見えてしまいます。
:::

## 手順3：Gitで初期化してコミット

```bash
$ cd kuroneko-himekuri-tokuten
$ git init
$ git add .
$ git commit -m "初回公開：購入者特典一式"
# git version 2.43.0 で確認
```

## 手順4：GitHubに公開（Public）する

### 方法A：GitHub CLI（最短）

```bash
$ gh repo create kuroneko-himekuri-tokuten --public --source=. --remote=origin --push
# リポジトリ作成 → origin登録 → push までを一度に実行
```

### 方法B：Webで作る

1. GitHub右上「＋」→「New repository」
2. 名前を入力し、**Public** を選択（READMEは追加しない。手元にあるため）
3. 作成後に表示されるURLで、手元から push する

```bash
$ git branch -M main
$ git remote add origin https://github.com/<ユーザー名>/kuroneko-himekuri-tokuten.git
$ git push -u origin main
```

公開できたら、ブラウザで `https://github.com/<ユーザー名>/kuroneko-himekuri-tokuten` を開き、READMEが表示されることを確認します。**このトップURLが、本のQRが指すべきURL**です。

## 手順5：QRはトップURLで作る

```bash
$ pip install "qrcode[pil]"
$ python -c "
import qrcode
from qrcode.constants import ERROR_CORRECT_H
qr = qrcode.QRCode(error_correction=ERROR_CORRECT_H, box_size=12, border=2)
qr.add_data('https://github.com/<ユーザー名>/kuroneko-himekuri-tokuten')  # ← トップURL
qr.make(fit=True)
img = qr.make_image(fill_color=(0x22,0x2B,0x45), back_color=(0xFB,0xF7,0xEF))
img.save('qr_tokuten.png')
"
# 誤り訂正レベルH（多少汚れても読める）。印刷時は4.5〜5.8cm角を維持
```

読み取り先がリポジトリのトップに飛べば成功です。**ファイルの直リンクではなくトップにする**ことで、あとで中身を入れ替えてもQRは生き続けます。

## 手順6：本の改訂に耐える運用

- **版はファイル名に埋め込まない**（`_v2_2_` を頻繁に変えると直リンクが腐る）。版の管理は GitHub の「Releases」でタグを切って残す
- 大きな更新のたびに Release を作ると、読者は常に最新をトップから入手でき、過去版も Releases から辿れる
- Issue は「質問を受けるなら有効、受けないなら無効」。受けるなら、READMEに「不具合は Issue へ」と一文を添える

## つまずきやすい箇所

| 症状 | 原因 | 対処 |
|---|---|---|
| QRが数か月後に死ぬ | ファイル直リンクにした | トップURLに作り直す |
| 読者に作業メモが見えた | `publish/` を公開した | `.gitignore` で除外、公開リポジトリから削除 |
| リンクが文字化け | 日本語ファイル名を直リンク配布 | トップから辿らせる。直リンクは避ける |
| 空欄がそのまま公開 | `【LinkedIn URL】` 等を残した | 公開前にREADMEを全文チェック |
| push で認証エラー | HTTPSのパスワード認証は廃止 | `gh auth login` か PAT（個人アクセストークン）を使う |

:::message
`git push` で `Authentication failed` が出たら、GitHubはパスワード認証を廃止済みです。`gh auth login` でログインするか、Settings → Developer settings → Personal access tokens でトークンを作り、それをパスワード欄に使います。
:::

## まとめ

**入口を1つに固定し、QRはトップURL、公開には読者向けだけ。** これだけで、本を改訂してもリンクは切れず、著者の手の内も漏れません。

---

*面白きこともなき世を面白く。*
