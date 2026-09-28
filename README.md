# Suno Knowledge Base

Sunoで公開された楽曲から、作曲・作詞・構成・Style・Directive・作者傾向・Azの主観的な印象をテキスト知識として蓄積するためのリポジトリです。

## Purpose

このリポジトリの目的は単なる楽曲レビュー支援ではありません。

- 楽曲の芳名帳を作る
- 作者ごとの作風・変化を追跡する
- 実際にSunoで使用されたStyle / Directiveを保存する
- 歌詞構造や楽曲構造を再利用可能な知識にする
- Azが曲を聴いたときに感じたことを保存する
- 「こんな曲を作りたい」から実在する作例を逆引きできるようにする

## Data Flow

```text
Suno URL
  ↓
Work
  ↓
raw/suno/<song-id>.json   ← 公開ページ上の一次情報だけ
  ↓
Chat / ちー
  ├─ songs/<creator>/<song>.md
  ├─ creators/<creator>.md
  ├─ knowledge/...
  └─ generated/reviews/<creator>/<song>.html
```

## Core Principle

3層を明確に分離します。

1. **Raw JSON = 取得事実**
2. **Markdown = 解釈済み知識の正本**
3. **HTML = 閲覧用派生物**

Workは採取だけを担当し、レビュー分析・Mermaid・HTML生成は行いません。

## Roles

### Work = Collector

Sunoページをブラウザで開き、公開ページ上に表示されている一次情報だけを取得してRaw JSONへ保存します。

- title
- creator
- creator URL
- song URL
- caption raw
- style raw
- lyrics raw
- published date
- model
- duration
- その他、ページ上で明示的に確認できるテキスト情報

Workは要約・レビュー・歌詞整形・構造分析・Mermaid・HTML生成をしません。

### Chat / ちー = Curator / Analyst

GitHubのRaw JSONを読み、以降の知識化を担当します。

- Clean Lyrics
- 歌詞分析
- 構造分類
- Mermaid
- Style / Directive知識化
- Creator情報更新
- Az Listening Notes / Impressionの整理
- Song Markdown生成
- HTML生成
- 作者横断・曲横断の比較

## Repository Layout

```text
.
├─ README.md
├─ docs/
│  ├─ architecture.md
│  ├─ work-instructions.md
│  └─ chat-instructions.md
├─ raw/
│  └─ suno/
├─ templates/
│  ├─ raw-song.json
│  └─ song.md
├─ songs/
├─ creators/
├─ knowledge/
│  ├─ styles/
│  ├─ directives/
│  ├─ structures/
│  ├─ moods/
│  └─ techniques/
└─ generated/
   └─ reviews/
```

音源ファイルの直接URL探索・ダウンロード・変換・保存・自動音響解析は行いません。
