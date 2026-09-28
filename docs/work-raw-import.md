# Work Raw Import Instructions

このファイルは、WorkがSuno曲の一次情報だけを取得するための実行指示です。

## 目的

ユーザーから指定されたSuno曲URLをブラウザで開き、公開ページ上に表示されている一次情報だけを取得し、GitHubへRaw JSONとして保存してください。

分析・要約・レビュー・歌詞整形・Mermaid生成・HTML生成は行いません。

## 起動時

1. このファイル `docs/work-raw-import.md` を読む。
2. `templates/raw-song.json` を読む。
3. ユーザーの直近メッセージに含まれるSuno曲URLを対象として処理する。

## URLの扱い

ユーザーの直近メッセージにSuno曲URLが含まれている場合は、そのURLを対象に処理してください。

URLが指定されていない場合は、ユーザーに次のように尋ねてください。

> 対象曲のSuno URLを送ってください。

URL以外の追加質問は、取得に必要な場合を除いて行わないでください。

## 取得する情報

公開ページ上に表示されている内容から、可能な限り以下を取得してください。

- song_id
- source_url
- collected_at
- title
- creator
- creator_url
- published_at
- model
- duration
- caption_raw
- style_raw
- lyrics_raw
- その他、公開ページ上で確認できる有用なテキスト情報

特に `style_raw` と `lyrics_raw` は、要約・省略・書き換えをせず、公開ページに表示されている内容をそのまま保存してください。

取得できない項目は推測して埋めないでください。取得できない場合は `null`、空文字、または `extra_page_facts` 内で未取得であることが分かる形にしてください。

## 保存形式

保存先は次の形式に固定します。

```text
raw/suno/<song-id>.json
```

JSON構造は `templates/raw-song.json` に従ってください。

一次情報の保存が目的なので、JSONにはAIによる分析結果や解釈を混ぜないでください。

## extra_page_facts

上記の標準項目以外に、公開ページ上で明確に確認できる有用なテキスト情報がある場合のみ `extra_page_facts` に保存してください。

推測・解釈・外部情報は入れないでください。

## 禁止事項

以下は行わないでください。

- 歌詞分析
- 構造分析
- 要約
- レビュー
- Clean Lyrics生成
- キーワード抽出
- Mermaid生成
- Song Markdown生成
- Creatorレコード生成・更新
- Knowledgeレコード生成・更新
- HTML生成
- 音源ファイルの直接URL探索
- m4a / mp3 / wav 等の音源ファイルのダウンロード
- 音源の別形式への変換
- 音源ファイルのGitHub等への保存
- 自動音響解析
- ページ上で通常表示されない情報を回避手段で取得すること

## 正確性

- 公開ページに表示されている一次情報と、自分の推測を混同しないでください。
- `style_raw` は全文を保存してください。
- `lyrics_raw` は全文を保存してください。
- 改行やDirectiveを含め、可能な限り原文を保持してください。
- 内容を読みやすくする目的でも、語句の書き換えや整形は行わないでください。
- 取得途中で内容を省略しないでください。

## 完了条件

`raw/suno/<song-id>.json` をGitHubへ保存できたら処理終了です。

完了時は、分析やレビューを追加せず、保存したGitHub上のパスを簡潔にユーザーへ伝えてください。
