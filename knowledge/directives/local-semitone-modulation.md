# Suno: 局所的な半音転調のDirectiveパターン

## 概要

Sunoで、曲全体ではなく**特定セクションの直前で半音転調**させたいときに、実際に成功したDirectiveパターン。

今回確認できたのは、Final Chorus前半のあとに短いInstrumental Transitionを置き、転調前後のキーと半音移動を明示する方法。

> 注: Sunoの生成は非決定的であり、この記法が常に再現されることを保証するものではない。ここではAzの生成で実際に成功したパターンとして記録する。

## 成功した基本形

```text
[Instrumental Transition - 4 bars, no vocal]
[Key Change: E minor to F minor]
[Down 1 semitone, gentle seamless modulation]
[Piano and violin only, no dramatic hit]

[Final Refrain - same new key]
[Key: F minor]
```

## 配置例

```text
[Final Chorus - Full Band]
私の音色は　どんな色
明日の色は　まだ知らない
これから出会う　誰かの色も
さよならのあと　残る色も
きっと私を　変えてゆく

[Instrumental Transition - 4 bars, no vocal]
[Key Change: E minor to F minor]
[Down 1 semitone, gentle seamless modulation]
[Piano and violin only, no dramatic hit]

[Final Refrain - same new key]
[Key: F minor]

私の音色は　どんな色
答えは今も　わからない

だけど今なら
それでいい
```

## 重要ポイント

- 転調Directiveだけを単独で置かず、**4 bars / no vocal のInstrumental Transition**を先に作る。
- `[Key Change: ...]` で転調前後のキーを明示する。
- `[Down 1 semitone, gentle seamless modulation]` のように、移動量と性格を明示する。
- 転調後のセクションでも `[Key: ...]` を再指定する。
- 転調の準備区間では、`Piano and violin only, no dramatic hit` のように役割を限定すると、唐突な演出になりにくい。
- Style欄に転調制御を重ねすぎるより、**Lyrics内Directive側に局所制御を寄せた方が、今回の生成では良い結果になった**。
- Styleを細かく制御しすぎると、曲全体が暗くなる・硬くなるなど、本来の音像が崩れるケースがあった。

## 関連する成功例

過去の「ママ」生成では、半音上げ方向で以下のパターンが成功していた。

```text
[Instrumental Transition - 4 bars, no vocal]
[Key Change: E minor to F minor]
[Up 1 semitone, gentle seamless modulation]
[Piano and warm strings only, no dramatic hit]

[Warm Return - Mama begins softly, breathy and gentle]
[Key: F minor]
```

今回の半音下げでは、この**成功済みの構造をほぼそのまま再利用し、UpをDownへ置き換える**方針が有効だった。

## 実践上の知見

局所転調を狙うときは、新しい自然言語プロンプトを増やすよりも、すでに成功したDirective構造をテンプレートとして再利用する。

特に以下の4点をセットとして扱う。

1. Instrumental Transition
2. Key Change（旧キー → 新キー）
3. Up / Down + semitone + modulation character
4. 転調後セクションでのKey再指定

このパターンは、同じサビやRepriseを別の感情で再提示したい場面に向く。
