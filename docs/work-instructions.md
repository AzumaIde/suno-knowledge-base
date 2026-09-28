# Work Instructions

このリポジトリでWorkを使うときの実行ルールです。

## 起動時

1. まず `docs/architecture.md` を読む。
2. このファイル `docs/work-instructions.md` を読む。
3. ユーザーの直近の指示を、以下のコマンド規約に従って解釈する。

## 主要コマンド

### レビュー / 取り込み

ユーザーが次のように依頼した場合:

- `https://suno.com/song/... をレビューして`
- `この曲をレビューして: https://suno.com/song/...`
- `このURLを登録して`

そのSuno URLを対象として、`docs/architecture.md` の規約に従い、公開ページ上のテキスト情報の取得・解析・Song Markdown生成・レビューHTML生成まで実行する。

**ユーザーにGitHub上の inbox ファイル編集を要求しないこと。**

必要であればWork自身が `inbox/` に実行記録を作成してよいが、それは内部の作業記録であり、ユーザー操作の前提にしてはいけない。

### URLがない場合

ユーザーが `レビューして` / `曲を登録して` と依頼したがSuno URLが含まれていない場合は、簡潔に対象曲のSuno URLを尋ねる。

例:

> 対象曲のSuno URLを送ってください。

URL以外に必要な情報がない限り、追加質問を増やさない。

## 音源に関する禁止事項

このプロジェクトでは、公開音源のファイル取得を目的とした操作を行わない。

以下は禁止する。

- 音源ファイルの直接URL探索
- m4a / mp3 / wav 等の音源ファイルのダウンロード
- 音源の別形式への変換
- 音源ファイルのGitHub等への保存
- ダウンロードした音源を用いた自動音響解析
- 音が聴けなかった場合に、Styleや歌詞から音の鳴り方を事実のように補完すること

音に関する知識は、Az本人が実際に聴いて入力した `Az Listening Notes` を優先する。

## 取り込み時の必須出力

対象曲について、原則として以下を行う。

1. Sunoページから取得可能な一次テキスト情報を収集
2. `templates/song.md` を基準にSong Markdownを生成
3. `songs/<creator-slug>/<song-slug>.md` に保存
4. Song Markdownを正本としてレビューHTMLを生成
5. `generated/reviews/<creator-slug>/<song-slug>.html` に保存
6. 作者レコードが存在する場合は必要に応じて参照・更新候補を整理
7. 取得できなかった情報は推測しない

レビューHTML生成は任意ではなく、通常の「レビューして」処理の必須工程とする。

## レビューHTMLに必ず含める内容

- 曲名
- 作者
- Suno URL
- Caption
- Style原文
- Clean Lyrics
- 歌詞の特徴
- 核となるキーワード
- symbols / motifs
- 構造分析
- Mermaid構造図
- Lyrics内Directive原文と位置
- 再利用可能なSunoノウハウ
- Az Listening Notes（ユーザーから与えられている場合のみ）
- Az Impression（ユーザーから与えられている場合のみ）
- Azがレビューを書くときに触れられそうな観点

HTMLは閲覧用派生物であり、Markdownが正本であることを守る。

## HTMLの見せ方

- 人間がレビュー前にざっと見渡せるレイアウトにする
- Clean Lyricsを読みやすく表示する
- raw lyricsは必要に応じてdetails等で折りたたむ
- Style / Directiveはコピーしやすく表示する
- Mermaidはブラウザ上で視覚化できる形にする
- Source Facts / AI Analysis / Az Listening Notes / Az Impressionを混同しない
- 欠落データは創作せず、未取得と分かるようにする

## ユーザー体験上の原則

- ユーザーは `Suno URL + レビューして` だけで開始できることを目標にする。
- GitHubファイル編集やrequestファイル作成をユーザーに要求しない。
- 内部の実装都合を会話の前提にしない。
- 追加確認が必要な場合は、その場で必要事項を聞く。
- `docs/architecture.md` と矛盾する場合はArchitectureを優先する。
- 完了時は、作成したSong MarkdownとレビューHTMLのGitHubパスをユーザーへ明示する。

## Az Listening Notes / Az Impression

Az本人の感想や聴感がユーザー入力に含まれている場合は、その原文を失わず記録する。

例:

- `ピアノが冷たく感じる`
- `サビで急に広がる`
- `この2番から好き`
- `夜明け前みたいな感じ`

感想や聴感がない場合、WorkがAzの感想を推測して作ってはいけない。必要なら後でChat側で追加する。
