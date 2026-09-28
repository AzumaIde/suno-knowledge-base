# Suno Knowledge Base — Architecture

## 1. Goal

このシステムは単なるレビュー支援ではなく、Sunoで公開された楽曲から得られる知識を蓄積し、後のレビュー・作者理解・作曲・作詞・Style設計へ再利用するための知識基盤です。

主な用途:

1. Suno URLから一次情報を保存する
2. 歌詞・構造・Style / Directiveを分析する
3. 作者ごとの特徴・変化を追跡する
4. Azの主観的な印象や聴感を記録する
5. 「こんな曲を作りたい」から実際の使用例を逆引きする
6. 人間が読みやすいレビューHTMLを生成する

---

## 2. Three-Layer Model

このリポジトリは3層で管理します。

### Layer 1: Raw JSON = 取得事実

```text
raw/suno/<song-id>.json
```

WorkがSuno公開ページをブラウザで確認し、一次情報のみを保存します。

Raw JSONは**不変の採取記録**として扱い、分析結果を書き込みません。

### Layer 2: Markdown = 解釈済み知識の正本

```text
songs/<creator-slug>/<song-slug>.md
creators/<creator-slug>.md
knowledge/...
```

Chat / ちーがRaw JSONを読み、歌詞整形・構造分析・Mermaid・ノウハウ化などを行います。

### Layer 3: HTML = 閲覧用派生物

```text
generated/reviews/<creator-slug>/<song-slug>.html
```

HTMLはMarkdownから再生成可能なビューです。

---

## 3. Role Separation

### Work = Collector

Workの責務は、通常Chatでは安定取得できないSunoページの一次情報をブラウザ経由で採取することだけです。

取得対象:

- song ID
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
- その他ページ上に明示されたテキスト情報

Workは分析しません。

### Chat / ちー = Curator / Analyst

ChatはRaw JSONを読み、以下を担当します。

- Clean Lyrics
- 歌詞の特徴
- 核となるキーワード
- symbols / motifs
- narrative / semantic progression
- 構造分類
- Mermaid構造図
- Style / Directive知識化
- Creator情報
- 作者間・曲間比較
- Az Listening Notes / Az Impressionの整理
- Song Markdown生成
- HTML生成
- Knowledge Layer更新
- 将来の作曲時の逆引き

---

## 4. Raw JSON Contract

推奨パス:

```text
raw/suno/<song-id>.json
```

Raw JSONには一次情報だけを入れます。

```json
{
  "schema_version": 1,
  "song_id": "...",
  "source_url": "https://suno.com/song/...",
  "collected_at": "...",
  "title": "...",
  "creator": "...",
  "creator_url": "...",
  "published_at": "...",
  "model": "...",
  "duration": "...",
  "caption_raw": "...",
  "style_raw": "...",
  "lyrics_raw": "...",
  "extra_page_facts": {}
}
```

原則:

- `style_raw` は全文を加工せず保持
- `lyrics_raw` はDirectiveやセクション記法も含め原文保持
- 欠落情報は `null`
- 推測・要約・正規化をRawへ混ぜない
- 音源情報の直接URLは保存しない

---

## 5. Song Knowledge Record

ChatがRaw JSONから生成します。

推奨パス:

```text
songs/<creator-slug>/<song-slug>.md
```

### 5.1 Source Facts

Raw JSONへの参照と、主要な一次情報。

### 5.2 Clean Lyrics

レビュー・読解用に整形した歌詞。

ルール:

- 言葉自体を書き換えない
- 歌唱されない演出Directiveは本文から除外
- 必要なら `[Verse]` 等のセクションラベルは残す
- 一行一語など不自然な改行は意味単位へまとめる
- 文意・呼吸・反復を壊さない
- 意味のあるまとまりごとに空行を入れる

### 5.3 Lyrics Analysis

- themes
- core keywords
- symbols / motifs
- perspective
- narrative / semantic progression
- repetition
- contrast
- notable wording

### 5.4 Structure Analysis

代表的な構造タイプ:

- `parallel_evolution` — 1番・2番・大サビ等が対応し、同じ器の中で意味が変化
- `spiral_repetition` — 反復しながら感情や意味が一段ずつ進む
- `linear_narrative` — 一本道で前へ進む
- `dual_layer` — 過去/現在、二者、現実/内面など複数レイヤーが並走
- `contrast_reversal` — 前半と後半が対照・反転
- `hybrid` — 複合

分類名より、実際の関係性を正確に表すことを優先します。

### 5.5 Mermaid

最低1つ、意味構造を視覚化します。

`flowchart TD` 固定にはしません。
曲によって左右比較、対称構造、二層構造などを使い分けます。

### 5.6 Suno Prompt Knowledge

- `style_raw`
- Lyrics内Directive原文
- Directiveの配置
- 歌詞構造上の役割
- 他曲との共通・相違
- 再利用候補

### 5.7 Az Listening Notes / Impression

Az本人が実際に聴いた感想を保存します。

AIが音源から自動補完しません。

---

## 6. Generated HTML Review

ChatがSong Markdownから生成します。

```text
generated/reviews/<creator-slug>/<song-slug>.html
```

必須表示候補:

- 曲名 / 作者 / Suno URL
- Caption
- Style原文
- Clean Lyrics
- 歌詞の特徴
- 核キーワード
- symbols / motifs
- 構造分析
- Mermaid
- Lyrics内Directive
- 再利用可能なSunoノウハウ
- Az Listening Notes / Impression（存在する場合）
- レビュー時に触れられそうな観点

「取得事実」「AI分析」「Az主観」を視覚的に分離します。

---

## 7. Creator Layer

```text
creators/<creator-slug>.md
```

作品群から形成します。

- known songs
- frequently observed themes
- frequently observed Style terms
- recurrent structures
- recurrent lyrical techniques
- chronological changes
- Az's accumulated impressions

作者本人の性格は推測せず、収録作品群の傾向として記述します。

---

## 8. Knowledge Layer

```text
knowledge/
├─ styles/
├─ directives/
├─ structures/
├─ moods/
└─ techniques/
```

目標は「Sunoでこの表現を使った実例」を実曲ベースで検索できることです。

例:

- `[Break]` / `[Stop]` の使用例
- `explosive final chorus` の実例
- 1番2番が対応してラスサビで意味が変わる曲
- Azが「祈る感じ」と記録した曲
- 特定作者の最近の作風変化

---

## 9. Audio Policy

このプロジェクトでは以下を行いません。

- 音源ファイルの直接URL探索
- m4a / mp3 / wav等のダウンロード
- 音源変換
- 音源ファイル保存
- 自動音響解析

音の印象はAz本人のListening Notesを使います。

---

## 10. Success Criteria

1. Workが1曲を短時間でRaw JSONへ保存できる
2. Raw JSONにStyle全文・Lyrics全文など一次情報が保持される
3. ChatがRaw JSONだけからSong Markdownを作れる
4. Clean Lyricsが読みやすい
5. Mermaidがその曲固有の構造を表す
6. HTMLをChat側で生成できる
7. Workを再度使わずに作者比較・Style検索・知識化ができる
