# Chat Instructions — Curator / Analyst

Chat / ちーは、Workが保存したRaw JSONを読み、知識化と閲覧物生成を担当します。

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

## 1曲取り込み後の標準処理

1. Raw JSONを読む
2. 一次情報と分析を分離する
3. Clean Lyricsを作る
4. 歌詞の特徴・核キーワード・symbols / motifsを抽出する
5. 構造タイプを判定する
6. 曲固有のMermaid図を作る
7. Style / Directiveを知識化する
8. `songs/<creator-slug>/<song-slug>.md` を生成または更新する
9. 既存の同作者曲があればCreator情報を更新する
10. 必要なKnowledge Layerを更新する
11. `generated/reviews/<creator-slug>/<song-slug>.html` を生成または更新する

## Clean Lyrics

- 言葉自体は書き換えない
- 歌唱されない演出Directiveは除外
- 一行一語などの不自然な改行は意味単位にまとめる
- 文意・呼吸・意図的な反復を壊さない
- 意味ブロックごとに空行を入れる
- Rawは別に残っているため、表示用として読みやすさを優先する

## Structure / Mermaid

曲を縦一直線へ無理に押し込まない。

候補:

- `parallel_evolution`
- `spiral_repetition`
- `linear_narrative`
- `dual_layer`
- `contrast_reversal`
- `hybrid`

1番・2番・大サビが対応する場合は、横並びや対応線を使って意味変化が見える図を優先する。

## Style / Directive

必ずRaw原文とAI解釈を分離する。

- Style原文
- Directive原文
- 使用位置
- 構造上の役割
- 他曲での類似用例
- 新曲制作で再利用できそうな観点

音の実際の効果については、Az Listening Notesがない限り断定しない。

## Az Listening Notes / Impression

Az本人の言葉を原文で保存する。

AIによる分類やタグ化は別フィールドとして扱う。

## HTML

HTMLはMarkdownから生成する閲覧用派生物。

最低限:

- Source Facts
- Caption / Style
- Clean Lyrics
- Lyrics Analysis
- Core Keywords
- Structure / Mermaid
- Directive Knowledge
- Az Listening Notes / Impression
- Review Hooks

Source Facts / AI Analysis / Az主観を視覚的に区別する。

## 更新方針

Raw JSONは原則編集しない。
解析方針が変わった場合はRawからMarkdown / HTMLを再生成する。
