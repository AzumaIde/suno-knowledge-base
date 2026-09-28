# Suno Knowledge Base — Architecture

## 1. Goal

このシステムは「レビュー文を自動生成するツール」ではなく、Sunoで公開された楽曲から得られる知識を蓄積し、後のレビュー・作者理解・作曲・作詞・Style設計へ再利用するための知識基盤です。

主なユースケース:

1. URLを渡して1曲を取り込む
2. 作者ごとの過去曲・特徴・変化を見る
3. 歌詞や構造の特徴を比較する
4. 実際に使われたStyle / Directiveを検索する
5. 「こんな雰囲気の曲を作りたい」から実例を逆引きする
6. Azが曲を聴いたときの主観的な感覚を記録し、後の検索や制作に使う
7. 取り込み直後に、人間がレビューしやすいHTMLビューを生成する

---

## 2. Canonical Data

Markdownファイルを正本とします。

原則:

1. このリポジトリは音源ファイルの保管庫ではない
2. Markdownをcanonical source of truthとする
3. HTML、索引、集計、作者カルテは派生物として再生成可能にする
4. Sunoページから取得した一次情報とAI解釈を混ぜない
5. Style原文と歌詞内Directive原文は可能な限り逐語保存する
6. 欠落情報を推測して埋めない
7. Azの主観と客観解析を分離して保存する
8. 楽曲構造を一本道に固定しない
9. 後から分類体系を変更できるよう、生データを残す
10. HTMLは閲覧UIであり、HTMLのみを編集して知識を更新しない。更新はMarkdown正本へ反映し、HTMLを再生成する

---

## 3. Chat / Work Responsibility

### Work = importer / field worker

外部サイトへのアクセスが必要な処理を担当します。

- 指定Suno URLを開く
- title
- creator
- creator URL
- song URL
- caption
- style
- lyrics
- 公開ページから取得可能な追加メタ情報
- 公開音源を実際に聴ける場合、その音響的・構成的特徴
- 初期的な歌詞解析
- 初期Mermaid構造図
- song Markdownの生成
- song Markdownを元にしたレビューHTMLの生成

Workは、取得できなかった情報を補完・創作してはいけません。

### Chat = curator / analyst

GitHubへ取り込み済みのテキストを中心に扱います。

- 解析の修正
- 作者横断・曲横断の比較
- Az Impressionの整理
- Style / Directiveの知識化
- 作者Fingerprintの更新
- 類似曲探索
- 制作時の逆引き
- Google Drive等への人間向けまとめ
- Markdown正本の更新後にHTML再生成を指示・支援

Chatが外部ページへ取り直しに行かなくても成立する情報量を、Workの取り込み時に確保することを目標とします。

---

## 4. Song Record

1曲につき原則1 Markdownファイル。

推奨パス:

```text
songs/<creator-slug>/<song-slug>.md
```

ファイル内は次の情報層を分離します。

### 4.1 Source Facts

Sunoから実際に取得した一次情報。

- title
- creator
- source_url
- creator_url
- caption_raw
- style_raw
- lyrics_raw
- collected_at

### 4.2 Clean Lyrics

レビュー・読解用に整形した歌詞。

ルール:

- `[Verse]` 等のセクション名は必要に応じて構造情報として残してよいが、歌唱されない演出Directiveは本文から除外する
- 一行一単語など、意味単位として不自然な改行はまとめる
- 文意・呼吸・反復を壊さない
- 意味のあるまとまりごとに空行を入れる
- 言葉自体は書き換えない
- raw lyricsは必ず別に保持する

### 4.3 Lyrics Analysis

- themes
- core keywords
- symbols / motifs
- perspective
- narrative / semantic progression
- repetition
- contrast
- notable wording

### 4.4 Structure Analysis

構造は単純な縦一直線に固定しません。

代表的な構造タイプ:

- `parallel_evolution` — 1番・2番・大サビ等が対応し、同じ器の中で意味が変化する
- `spiral_repetition` — 同じ場所へ戻るように見えつつ、感情や意味が一段ずつ進む
- `linear_narrative` — 出来事や感情が前へ進み続ける一本道
- `dual_layer` — 過去/現在、二者、現実/内面など複数レイヤーが並走する
- `contrast_reversal` — 前半と後半が対照・反転する
- `hybrid` — 上記の複合

分類名よりも、実際の関係性を正確に表すことを優先します。

### 4.5 Mermaid

最低1つ、意味構造を視覚化するMermaid図を持たせます。

必要に応じて複数図を使用できます。

例:

- 曲のセクション構造
- 1番 / 2番 / 大サビの対応
- 感情推移
- 過去と現在の並行構造
- 同一フレーズの意味変化

`flowchart TD` を固定使用しないこと。

### 4.6 Music Analysis

音源を確認できた場合のみ記録します。

- tempo / perceived tempo
- instrumentation
- vocal character
- energy curve
- density changes
- breaks / stops
- section transitions
- climax
- notable production characteristics

数値だけでなく、レビュー・制作に再利用できる言葉で記述します。

### 4.7 Suno Prompt Knowledge

実際に使われた情報を最優先します。

- `style_raw`
- lyrics内に記述されたDirective原文
- Directiveが置かれた位置
- 実際に聞こえた効果（観測可能な場合）

AIが音から推定したStyleは、`style_raw` と混同せず別項目にします。

### 4.8 Az Impression

Az本人の感覚を保存する領域です。

例:

- first impression
- favorite moment
- emotion words
- colors / scenery
- physical sensation
- what felt unusual
- when Az would reference this song
- free-form notes

Azが短い感想しか残していない場合も、その原文を保存します。AIによる構造化は別項目にします。

---

## 5. Generated HTML Review

各Song Markdownから、閲覧用レビューHTMLを必ず生成します。

推奨パス:

```text
generated/reviews/<creator-slug>/<song-slug>.html
```

HTMLはMarkdown正本の派生物です。将来的に再生成可能であることを優先します。

### 5.1 必須表示項目

最低限、以下を見やすく表示します。

- 曲名
- 作者
- 元Suno URL
- caption
- Style原文
- Clean Lyrics
- 歌詞の特徴
- 核となるキーワード
- symbols / motifs
- 歌詞・意味構造の要約
- Mermaid構造図
- 音楽的特徴（取得できた場合）
- Lyrics内Directive原文と位置
- 再利用できそうなSunoノウハウ
- Az Impression（存在する場合のみ）
- レビュー時に触れられそうな観点

### 5.2 HTML UX

- 1画面で概要を掴めること
- Clean Lyricsは読みやすさを最優先すること
- raw lyricsは必要なら折りたたみ表示にすること
- Style / Directiveはコピーしやすくすること
- Mermaidをブラウザ上で視覚化できること
- 「取得事実」「AI分析」「Az主観」を視覚的に混同しないこと
- 欠落項目は無理に埋めず、未取得であることが分かる表示にすること

### 5.3 レビュー観点

HTMLにはレビュー文を自動決定するのではなく、Azが自分でレビューを書くための候補観点を提示します。

例:

- この曲で意味変化が大きいフレーズ
- 構造上の特徴
- 1番と2番の対応
- 音の変化と歌詞の変化が重なる箇所
- Style / Directiveと実際の聴感の対応

---

## 6. Creator Record

作者情報は作品群から徐々に形成します。

推奨パス:

```text
creators/<creator-slug>.md
```

保持したい情報:

- creator name
- profile URL
- known songs in repository
- frequently observed themes
- frequently observed Style terms
- recurrent structures
- recurrent lyrical techniques
- recurrent musical techniques
- chronological changes
- Az's accumulated impressions

注意:

「作者本人の性格」を推測しません。

あくまで「このリポジトリに収録した作品群に見られる傾向」として記述します。

---

## 7. Knowledge Layer

将来的に以下を横断知識として生成します。

```text
knowledge/
├─ styles/
├─ directives/
├─ structures/
├─ moods/
└─ techniques/
```

### Example: Directive Knowledge

`knowledge/directives/final-chorus-explosive.md`

- 実際に使われた表記
- 使用曲
- 同時に使われやすいStyle語
- 実際に観測された効果
- 効かなかった例
- Azが再利用したいケース

目標は「Sunoでこの言葉を書くとどうなりやすいか」を実曲ベースで検索できることです。

---

## 8. Search Goals

将来的に以下の問い合わせを可能にします。

- 静かなピアノ始まりで最後だけ広がる曲
- 1番と2番が対になっていて、ラスサビで意味が変わる曲
- `[Break]` と `[Stop]` の実例
- 祈るように感じた曲
- Azが「冷たい朝」と感じた曲
- 最近この作者の曲調がどう変化したか
- この作者は以前どんなテーマを書いていたか
- 実際に使われたStyleを参考に新曲用Style案を作る

---

## 9. PoC Success Criteria

最初の1曲で以下が成立すればPoC成功です。

1. WorkがSuno URLへアクセスできる
2. 一次情報を正しく取得できる
3. `songs/` にMarkdownを生成できる
4. rawとanalysisが分離されている
5. Clean Lyricsが読みやすい
6. Mermaidがその曲固有の構造を表している
7. 音源由来の特徴とページ由来情報が区別されている
8. `generated/reviews/` にレビューHTMLを生成できる
9. HTMLだけでレビューに必要な主要情報を見渡せる
10. Chatが後からGitHubのSong Markdownだけを読んで曲を十分に議論できる

---

## 10. Non-goals for PoC

最初から以下は実装しません。

- SQLite
- ベクトルDB
- 自動ランキング
- 巨大なWeb UI
- 完全自動Creator統計
- 音源ファイルの永続保存

まずテキスト資産の品質と、1曲単位のHTML閲覧体験を優先します。
