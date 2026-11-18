# Gemini Skills Backup

Custom skills for Gemini tailored for English vocabulary learning and automated Anki flashcard generation (PSV data + 16:9 illustrations).

---

## Included Skills

| Skill Directory | Name | Description |
| :--- | :--- | :--- |
| [`english-learning-partner`](./english-learning-partner/SKILL.md) | **English Learning Partner** | Deep, conceptual insights into English words, phrasal verbs, idioms, and fixed expressions in clear, accessible English (meanings, core image, etymology, synonyms/nuances, collocations). |
| [`anki-il`](./anki-il/SKILL.md) | **Anki IL (Immersion Learning)** | Generates minimalist 16:9 split-canvas educational flashcard images and/or Pipe-Separated Values (PSV) flashcard data for the `ImmersionLearning` Anki note type. |
| [`anki-ade`](./anki-ade/SKILL.md) | **Anki ADE (Active Daily English)** | Generates Cloze-deletion PSV flashcards and warm 16:9 storybook-style context illustrations simultaneously for the `ActiveDailyEnglish` Anki note type. |
| [`anki-toeic`](./anki-toeic/SKILL.md) | **Anki TOEIC** | Converts uploaded images of TOEIC Part 1–5 questions, scripts, and explanations into English PSV format ready for Anki import. |

---

## How to Use (English)

### 1. Setup in Gemini
Register each `<skill-name>/SKILL.md` file in your Gemini Skills settings (or ask Gemini to save/update the skill from the `SKILL.md` content).

### 2. Trigger Commands & Prompts

| Skill | Trigger / Input Format | Example Prompt | Output |
| :--- | :--- | :--- | :--- |
| **`english-learning-partner`** | `exp [word/phrase]` or standalone word/phrase/sentence (without `[ ]` or Anki prefixes) | `exp run out of`<br>`take it for granted`<br>`It is what it is` | Structured English analysis (IPA, CEFR/Register, Meanings, Core Image, Synonyms, Collocations) |
| **`anki-il` (Mode A)** | `il [word/phrase]` | `il work`<br>`il as it turns out` | **16:9 Flashcard Image only** (split-canvas vector illustration with target & example sentence) |
| **`anki-il` (Mode B)** | `psv [word/phrase]` or sentence with `[ ]` around target(s) | `psv run into`<br>`I [ran into] my old friend.`<br>`Don't [take] his kindness [for granted].` | **PSV code block + 16:9 Flashcard Image** simultaneously |
| **`anki-il` (Follow-ups)** | `psv` or `image` | `psv` (after Mode A)<br>`image` (to regenerate illustration) | Converts previous image card to PSV, or regenerates the 16:9 image |
| **`anki-ade`** | `ade [word/phrase]`<br>Follow-up: `image` | `ade run out of`<br>`ade call off` | **Cloze PSV code block + 16:9 Storybook Illustration** simultaneously (`image` regenerates the illustration) |
| **`anki-toeic`** | Upload TOEIC Part 1–5 page image(s) (+ optional question numbers) | *(Upload image)*<br>*(Upload image)* `1, 3`<br>*(Upload Part 3/4 image)* `only 62, 63` | **PSV code block** formatted for `TOEIC_Part1`, `TOEIC_Part2`, `TOEIC_Part3_4`, or `TOEIC_Part5` |

### 3. Importing PSV Data into Anki
1. Copy the generated `text` code block (including the `#notetype`, `#deck`, `#separator: Pipe`, and `#html: true` header lines) and save it as a `.txt` file (UTF-8).
2. In Anki, go to **File → Import** and select the `.txt` file.
3. Anki will automatically detect the Note Type, Deck, Pipe (`|`) delimiter, and HTML settings from the headers.
4. *(Optional)* Drag and drop the generated 16:9 illustration into the `Image` field of the corresponding note.

---

## 使い方 (日本語)

### 1. セットアップ
各フォルダ内の `SKILL.md` を Gemini のスキル機能に登録して使用します。

### 2. スキル別トリガー・入力方法一覧

#### ① `english-learning-partner`（英単語・表現の深掘り解説）
* **トリガー**: `exp [単語・熟語]` またはプレフィックスや角括弧 `[ ]` なしの英単語・フレーズ・英文をそのまま入力
* **入力例**:
  * `exp work`
  * `run into`
  * `It is what it is`
* **出力内容**: 英語による体系的な解説（発音記号、CEFR/レジスター、主要意味・例文、ニュアンス、コアイメージ・語源、類語比較、派生語・コロケーション）が出力されます。

#### ② `anki-il`（Immersion Learning 単語カード＆16:9イラスト生成）
* **モードA（16:9 フラッシュカード画像のみ生成）**:
  * **トリガー**: `il [単語・熟語]`（例: `il work`, `il run out of`）
  * **フォローアップ**: 画像生成後に `psv` と入力すると、そのカードの例文で PSV データを出力します。
* **モードB（PSVデータ ＋ 16:9 フラッシュカード画像を同時出力）**:
  * **トリガー1**: `psv [単語・熟語]`（例: `psv run into`）
  * **トリガー2**: ターゲット表現を `[ ]` で囲んだ英文を入力（例: `I [ran into] my old friend.` / `Don't [take] his kindness [for granted].`）
  * **フォローアップ**: `image` と入力すると、16:9 イラストのみを再生成します。

#### ③ `anki-ade`（Active Daily English 穴埋めカード＆16:9文脈イラスト生成）
* **トリガー**: `ade [単語・熟語]`（例: `ade run out of`, `ade call off`）
* **出力内容**: `ActiveDailyEnglish` ノートタイプ用の Cloze（穴埋め）PSV データと、文字なしの絵本風 16:9 シチュエーションイラストを同時に生成します。
* **フォローアップ**: `image` と入力すると、イラストのみを再生成します。

#### ④ `anki-toeic`（TOEIC Part 1〜5 問題・解説画像の Anki PSV 変換）
* **トリガー**: TOEIC の問題ページや解説ページの画像をアップロード
* **問題番号の指定（任意）**:
  * 指定なし: 画像内のすべての問題を順番に PSV 化
  * Part 1 / 2 / 5 で番号指定: 画像と一緒に `1, 3` や `119` と入力すると指定番号のみ出力
  * Part 3 / 4 で設問指定: 画像と一緒に `only 62, 63` のように入力すると、3問セットの1行内で該当設問ブロックのみを埋め、未指定ブロックを空欄（`||`）にして出力

### 3. Anki へのインポート手順
1. 出力されたコードブロック（先頭4行の `#notetype`, `#deck`, `#separator: Pipe`, `#html: true` を含む）をコピーし、テキストファイル（`.txt` / UTF-8）として保存します。
2. Anki の **「ファイル」→「読み込む（Import）」** から保存した `.txt` ファイルを選択します。
3. ヘッダー情報により、ノートタイプ・デッキ・区切り文字（パイプ `|`）・HTML許可が自動認識されます。
4. 生成された 16:9 画像をダウンロードし、該当ノートの `Image` フィールドへ貼り付けます。

---

## Repository Structure

```text
gemini-skills-backup/
├── README.md
├── anki-ade/
│   └── SKILL.md
├── anki-il/
│   └── SKILL.md
├── anki-toeic/
│   └── SKILL.md
└── english-learning-partner/
    └── SKILL.md
```
