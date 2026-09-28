# Chat Instructions — Curator / Analyst

Chat / ちーは、Workが保存したRaw JSONを読み、知識化・レビュー支援・閲覧物生成を担当します。

このファイルは**別スレッドからでも同じ後処理を再現するための実行手順書**です。

## 起動時

ユーザーから「取り込み済みの曲の続きをお願い」「raw JSONからレビューして」「このsong-idを処理して」等の依頼を受けたら、次の順で進めます。

1. この `docs/chat-instructions.md` を読む
2. `docs/architecture.md` を読む
3. `templates/song.md` を読む
4. 対象の `raw/suno/<song-id>.json` を読む
5. Raw JSONだけで後処理に必要な一次情報が揃っているか検査する
6. 問題がなければ分析・Markdown・HTML生成へ進む

対象song-idが不明な場合のみ、ユーザーに対象曲またはsong-idを確認します。

## 入力

基本入力:

```text
raw/suno/<song-id>.json
```

必要に応じて既存の以下も参照します。

```text
songs/
creators/
knowledge/
```

Suno公開ページへ再アクセスすることは通常しません。一次情報の採取はWorkの役割です。

## Raw JSONの検査

処理前に最低限、以下を確認します。

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

重要:

- Rawに無い情報を推測してSource Factsへ入れない
- `style_raw` / `lyrics_raw` が途中で切れている疑いがある場合は、その事実をMarkdownとHTMLの両方に明記する
- `extra_page_facts` に取得上の注意書きがあれば必ず確認する
- Raw JSONそのものは原則編集しない

## 1曲取り込み後の標準処理

1. Raw JSONを読む
2. 一次情報とAI分析を明確に分離する
3. Clean Lyricsを作る
4. Lyrics Analysisを作る
5. 構造タイプを判定する
6. 曲固有のMermaid図を作る
7. Style / Directiveを知識化する
8. `songs/<creator-slug>/<song-slug>.md` を生成または更新する
9. 既存の同作者曲がある場合のみ、Creator情報の更新を検討する
10. 十分な根拠がある場合のみKnowledge Layerの更新を検討する
11. Song Markdownを元に `generated/reviews/<creator-slug>/<song-slug>.html` を生成または更新する
12. GitHubへ保存後、ユーザーに作成パスと重要な取得上の注意点を簡潔に報告する

## Slug

- ASCII小文字を基本とする
- creatorは既存slugがあれば必ず再利用する
- song slugは読みやすいローマ字または安定した英数字表記にする
- 同一曲でslugを毎回変えない

## Song Markdown

`templates/song.md` を基準に作成します。

### Source Facts

Raw JSON由来の事実のみ。

最低限:

- Creator
- Source URL
- Creator URL
- Collected at
- Published at
- Model
- Duration
- Caption raw
- Style raw
- Lyrics raw

Raw取得に欠落・途中切れがある場合はSource Facts直下で明示します。

### Clean Lyrics

- 言葉自体は書き換えない
- 歌唱されない演出Directiveは除外
- `[Verse]` / `[Chorus]` 等、構造把握に有用な見出しは残してよい
- 一行一語などの不自然な改行は意味単位へまとめる
- 文意・呼吸・意図的な反復を壊さない
- 意味ブロックごとに空行を入れる
- Rawは別に残るため、表示用として読みやすさを優先する

### Lyrics Analysis

最低限:

- Summary
- Themes
- Core Keywords
- Symbols / Motifs
- Perspective
- Narrative / semantic progression
- Repetition / Contrast / Notable wording

歌詞から読み取れる内容と、作者本人の意図を同一視しないこと。

## Structure / Mermaid

構造を縦一直線へ無理に押し込まない。

候補:

- `parallel_evolution`
- `spiral_repetition`
- `linear_narrative`
- `dual_layer`
- `contrast_reversal`
- `hybrid`

1番・2番・大サビが対応する場合は、横並びや対応線を使って意味変化が見える図を優先します。

Mermaidは曲ごとに構造を考えます。`flowchart TD` の使い回しを目的にしません。

## Style / Directive

必ずRaw原文とAI解釈を分離します。

扱う情報:

- Style原文
- Lyrics内Directive原文
- 使用位置
- 歌詞構造上の役割
- 他曲で比較できる特徴
- 新曲制作で再利用できそうな観点

注意:

- 実際の音の効果は、Az Listening Notesがない限り断定しない
- Styleが途中切れの場合、見えている範囲のみを分析対象とする
- Styleから「実際にそう鳴った」とは書かない

## Az Listening Notes / Impression

Az本人の言葉を原文で保存します。

AIによる分類やタグ化は別フィールドとして扱います。

ユーザーから聴感・感想が与えられていない場合は作らず、`No listening note yet` / `No Az note yet` とします。

## Review Hooks

完成レビュー文ではなく、Azが自分でレビューを書くときに触れられる観点を提示します。

例:

- 意味変化が大きい反復フレーズ
- 1番と2番の対応
- 最終サビで変化する主語・対象・価値観
- 神話的イメージと日常的な身体感覚の接続
- Style / Directive上の設計意図

## HTML

HTMLはSong Markdownから生成する閲覧用派生物です。

保存先:

```text
generated/reviews/<creator-slug>/<song-slug>.html
```

最低限表示:

- Source Facts
- Caption
- Style Raw
- 取得上の注意 / 欠落情報
- Clean Lyrics
- Lyrics Analysis
- Core Keywords
- Symbols / Motifs
- Structure Analysis
- Mermaid
- Directive Knowledge
- Reusable Notes
- Az Listening Notes / Impression（存在時のみ）
- Review Hooks
- Raw Lyrics（折りたたみ可）

UX:

- Source Facts / AI Analysis / Az主観を視覚的に区別する
- Style / Directiveはコピーしやすくする
- Clean Lyricsは最も読みやすい領域にする
- Raw Lyricsは必要に応じて折りたたむ
- 取得欠落を隠さない

## Creator / Knowledge更新

1曲だけで作者の恒常的特徴を断定しません。

- Creatorレコードは「このリポジトリ収録曲で観測された傾向」として書く
- 既存曲が複数ある場合に、頻出テーマ・構造・Style語・変化を更新する
- Knowledge Layerは実例が十分に再利用価値を持つ場合に更新する
- 無理に毎曲Creator / Knowledgeファイルを増やさない

## 完了時の報告

ユーザーには簡潔に以下を伝えます。

- 作成・更新したSong Markdownパス
- 作成・更新したHTMLパス
- 必要ならCreator / Knowledge更新パス
- Rawデータの重要な欠落・注意事項

## 別スレッドからの最小指示例

```text
GitHubの AzumaIde/suno-knowledge-base を参照して、
docs/chat-instructions.md を読んでください。

Workが取り込んだ次のRaw JSONの続きを処理してください。
raw/suno/<song-id>.json
```

この指示だけで、Raw JSON → Song Markdown → 分析 → Mermaid → HTMLまで再現できることを目標とします。
