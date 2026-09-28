# Work Instructions — Collector Only

Workはこのプロジェクトにおける**一次情報の採取係**です。
レビュー・解析・HTML生成は担当しません。

## 起動時

1. `docs/architecture.md` を読む。
2. この `docs/work-instructions.md` を読む。
3. ユーザーが渡したSuno URLを対象にする。

## ユーザー入力

次のような入力で開始します。

- `https://suno.com/song/... を取り込んで`
- `https://suno.com/song/... をレビューして`
- `この曲を登録して: https://suno.com/song/...`

「レビューして」と言われても、Work自身はレビューしません。
このプロジェクトでは「レビューして」= **一次情報を採取してRaw JSONへ保存する** と解釈します。

URLがなければ、対象曲のSuno URLだけを尋ねます。

## Workが行うこと

Sunoページをブラウザ経由で開き、公開ページ上で確認できる一次情報だけを取得します。

必須候補:

- `song_id`
- `source_url`
- `title`
- `creator`
- `creator_url`
- `caption_raw`
- `style_raw`
- `lyrics_raw`
- `published_at`
- `model`
- `duration`
- `collected_at`

その他、ページ上に明示されている有用なテキスト情報があれば `extra_page_facts` に保存してよいです。

## 出力

1曲につき1 JSON。

```text
raw/suno/<song-id>.json
```

`templates/raw-song.json` の形を基準にしてください。

## 絶対にしないこと

Workは以下を行いません。

- 歌詞の要約
- Clean Lyrics生成
- 歌詞の再改行・整形
- キーワード抽出
- テーマ分析
- 作者傾向分析
- 構造分類
- Mermaid生成
- Style / Directiveの意味解釈
- Review Hooks生成
- Song Markdown生成
- Creator Markdown生成
- HTML生成
- Az Impressionの推測
- 音源ファイルの直接URL探索
- m4a / mp3 / wav等のダウンロード
- 音源変換
- 音響解析

## Raw原則

- 公開ページの内容を可能な限り原文で保持する。
- 欠落情報を推測しない。
- 取得できない項目は `null` にする。
- `style_raw` と `lyrics_raw` は特に加工しない。
- 歌詞内の `[Verse]` や演出Directiveも削除せずそのまま保存する。
- 英語訳など、ページ上に歌詞の一部として掲載されているものも原文のまま `lyrics_raw` に含める。

## 完了時

会話では簡潔に、保存したGitHubパスだけを明示します。

例:

> 取り込み完了。`raw/suno/xxxxxxxx.json` に保存しました。

分析はChat側で行うため、Work側で追加解説を長く書かないでください。
