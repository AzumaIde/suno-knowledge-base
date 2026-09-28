# Suno Knowledge Base

Sunoで公開された楽曲から、作曲・作詞・構成・Style・Directive・作者傾向・Azの主観的な印象をテキスト知識として蓄積するためのリポジトリです。

## Purpose

このリポジトリの目的は、単なる楽曲レビュー支援ではありません。

- 楽曲の芳名帳を作る
- 作者ごとの作風・変化を追跡する
- 実際にSunoで使用されたStyle / Directiveを保存する
- 歌詞構造や楽曲構造を再利用可能な知識にする
- Azが曲を聴いたときに感じたことを保存する
- 「こんな曲を作りたい」から、実在する成功例を逆引きできるようにする

## Core Principle

**Markdownが正本です。**

HTML、統計、ダッシュボード、索引、作者カルテなどはMarkdownから再生成できる派生物として扱います。

## Roles

### Work

外部情報の取得を担当します。

- Suno URLを開く
- title / creator / style / caption / lyrics を取得する
- 公開音源から観察可能な特徴を抽出する
- 取得した一次情報と初期解析をMarkdownへ保存する

### Chat

蓄積済み情報の整理・分析・再利用を担当します。

- 歌詞構造や意味構造の再分析
- Mermaid構造図の改善
- 作者傾向の比較
- Azの印象の構造化
- Style / Directiveの横断分析
- 作曲時の逆引き検索
- Google Drive等への人間向け整理

## Repository Layout

```text
.
├─ README.md
├─ docs/
│  └─ architecture.md
├─ inbox/
│  └─ request-001.md
├─ templates/
│  └─ song.md
├─ songs/
├─ creators/
└─ knowledge/
   ├─ styles/
   ├─ directives/
   ├─ structures/
   ├─ moods/
   └─ techniques/
```

`inbox/` はWorkへの作業依頼置き場です。Workが処理した成果物は `songs/` 以下へ保存します。
