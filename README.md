# ヘルスケア広告 背景画像の自己採点ハーネス
# Self-Scoring Harness for Healthcare Ad Backgrounds

[日本語](#日本語) | [English](#english)

---

## 日本語

+81 Builders ハッカソン「モデルではなく、ハーネスをつくる」（2026/11/17）の作品です。

## 一言でいうと

AIが作った広告用の背景画像を、**デザイナーの採点基準** で採点し、良くなるまで画像プロンプトを直させる仕組みです。モデルには手を加えず、その周りの仕組み（ハーネス）だけを作りました。

改善したハーネスの要素：**評価**

## 解決したい課題

AIに「ヘルスケアの広告の背景画像をつくって」と頼むと、それっぽい画像は出てきます。でも、ブランドに合っているかは誰もチェックしていません。何を良しとするかは用途やブランドによって違うので、汎用のモデルには判断できません。

## 仕組み

```
商品の概要 ＋ お手本画像（デザイナーが選ぶ）
  ↓
① プロンプト係：画像生成用の英語プロンプトを書く（1周目はお手本を見せない＝改善前）
  ↓
② 画像生成：外部の画像生成AIで生成し、ページに貼る
  ↓
③ 採点係：お手本と見比べて3項目を10点満点で採点（評価）
  ↓
④ 検証：ブランド色が入っているか、使わない言葉がないかをプログラムで判定（AIに判断させない）
  ↓
⑤ 7点未満なら、採点理由を渡してプロンプトを書き直す → ②へ（ループ）
   最大3周で停止（ガードレール）
```

### 採点基準（デザイナーが言語化したもの）

| 項目 | 10点 | 1点 |
|---|---|---|
| ヘルスケアらしさ | 心拍の波形、分子のような六角形、白衣・聴診器など医療を連想させるモチーフがあり、清潔で信頼感がある | 医療・健康と結びつかない（雑貨、食卓、生活の場面の写真など） |
| テクノロジー感 | 点と線のネットワーク、六角形やドット、光の粒、やわらかな発光で、データやAIを上品に感じさせる | テクノロジー要素がない、または多色でギラギラしている |
| ブランド・背景としての使いやすさ | ブランドカラー `#2563F4` を軸にした青のモノトーン。文字を載せられる広い余白がある | 青以外の色が目立つ、ごちゃごちゃして文字が載らない、生活感のある写真 |

採点係が甘くならないよう、「弱点を一つでも書くなら6点以下」というルールも入れています。

## 結果と気づき

- 1周目（AI任せ）の平均は約3.2点。寝室の生活写真が出てきて2点でした。作り直しを重ねると平均約6.2点になり、心拍線と六角形のある青い背景に近づきました。ほかの商品でも同じ基準で回ることを確認しました。
- **最初の失敗が一番の学びでした。** はじめは採点係に「AIっぽさは悪いもの」と教えていたので、おしゃれな朝の机の写真を合格にしてしまいました。でも、ヘルスケアの背景画像でブランドが求めていたのは、むしろ上品なテクノロジー感でした。お手本画像を見ながら採点基準を書き直したところ、採点係の点数がデザイナーの目に合ってきました。
- **評価は、人間にしか決められない部分でした。**

## 動かし方

`index.html` は Claude のアーティファクト（claude.ai 上で公開するページ）として動きます。ページから Claude を呼び出す機能（`claude.use("sample")`、画像入力あり）を使っているので、ブラウザで `index.html` を直接開いても AI は動きません。

1. claude.ai でアーティファクトとして公開したページを開く
2. お手本画像を2〜3枚入れる
3. 商品の概要を書き、「プロンプトをつくる」を押す
4. プロンプトをコピーして画像生成AIで画像をつくり、カードに貼る
5. 採点結果と改善プロンプトを見ながら繰り返す

## 次にやりたいこと

- 採点係の答え合わせの自動化：デザイナーが良い・悪いと判断した画像を10枚ずつ用意し、採点係の点数と一致するかを測る
- 採点の厳しさの調整（今は少し厳しすぎる）
- 採用された画像を覚えさせる（記憶）
- 画像生成APIをつないで全自動にする

## 注意

お手本画像には Adobe Stock の透かし入り画像を使ったため、このリポジトリには含めていません。

---

## English

A project for the +81 Builders hackathon "Build the Harness, Not the Model" (Nov 17, 2026).

### In one sentence

A system that scores AI-generated ad background images against **a designer's own scoring criteria**, and keeps revising the image prompt until the images get better. The model itself is untouched; only the system around it (the harness) was built.

Harness component improved: **evaluation**

### The problem

If you ask an AI to "make a background image for a healthcare ad," you get something plausible. But nobody checks whether it fits the brand. What counts as "good" depends on the use case and the brand, so a general-purpose model can't judge it on its own.

### How it works

```
Product summary + reference images (chosen by the designer)
  ↓
① Prompt writer: writes an English image-generation prompt
   (round 1 does not see the references = the "before" baseline)
  ↓
② Image generation: generated in an external image AI, then pasted into the page
  ↓
③ Scorer: compares the image with the references and scores 3 criteria out of 10 (evaluation)
  ↓
④ Verification: code (not AI) checks the brand color is present and banned words are absent
  ↓
⑤ If any score is below 7, the scorer's reasons are fed back and the prompt is rewritten → back to ② (loop)
   Stops after at most 3 rounds (guardrail)
```

### Scoring criteria (written by the designer)

| Criterion | 10 points | 1 point |
|---|---|---|
| Healthcare feel | Has motifs that evoke medicine — heartbeat waveforms, molecule-like hexagons, white coats, stethoscopes — and feels clean and trustworthy | No link to health or medicine (household goods, dining tables, lifestyle photos) |
| Technology feel | Network nodes and lines, hexagon or dot patterns, light particles, soft glow — data and AI expressed with restraint | No technology elements, or garish multi-color effects |
| Brand fit / usability as a background | Blue monochrome built around brand color `#2563F4`, with generous empty space for text | Non-blue colors stand out, too busy for text, lifestyle photo |

To keep the scorer from being lenient, it also follows a rule: "if you name any weakness, the score must be 6 or lower."

### Results and lessons

- Round 1 (AI on its own) averaged about 3.2. It produced a bedroom lifestyle photo that scored 2. After revisions the average rose to about 6.2, converging on blue backgrounds with heartbeat lines and hexagons. The same criteria also worked for other products.
- **The first failure was the biggest lesson.** At first I told the scorer that "looking AI-made" was bad, so it passed a stylish morning-desk photo. But what the brand actually wanted for healthcare backgrounds was the opposite: a refined sense of technology. After rewriting the criteria while looking at reference images, the scorer started to agree with the designer's eye.
- **Evaluation turned out to be the part only a human can define.**

### How to run

`index.html` runs as a Claude artifact (a page published on claude.ai). It calls Claude from the page (`claude.use("sample")`, with image input), so opening `index.html` directly in a browser will not run the AI parts.

1. Open the page published as an artifact on claude.ai
2. Add 2–3 reference images
3. Enter a product summary and press 「プロンプトをつくる」 (Generate prompts)
4. Copy the prompt, generate an image in an image AI, and paste it onto the card
5. Repeat while reviewing the scores and the improved prompts

### Next steps

- Automate scorer calibration: prepare 10 images the designer judges good and 10 judged bad, and measure how well the scorer agrees
- Tune the scorer's strictness (currently a bit too strict)
- Remember adopted images (memory)
- Connect an image-generation API for full automation

### Note

The reference images were watermarked Adobe Stock images, so they are not included in this repository.
