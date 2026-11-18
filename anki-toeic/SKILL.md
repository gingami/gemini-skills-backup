---
name: anki-toeic
description: Convert uploaded images of TOEIC Part 1, Part 2, Part 3, Part 4, or Part 5 questions, scripts, and explanations into English Pipe-Separated Values (PSV) format for Anki import. Trigger whenever the user uploads TOEIC book or test page images (optionally specifying question numbers like "1, 3" or "only 62, 63") or asks to convert TOEIC materials into Anki PSV.
---
# Anki TOEIC

## Summary

Analyze uploaded images of TOEIC Part 1, Part 2, Part 3, Part 4, or Part 5 question and explanation pages (including Japanese explanations) and convert them into raw English Pipe-Separated Values (PSV) format ready for Anki import.

## When to Use

  - The user uploads one or more images of TOEIC Part 1, Part 2, Part 3, Part 4, or Part 5 materials, with or without specifying target question numbers.

## Steps

### 1. Detect Part & Select Questions

  - Automatically detect whether the uploaded image belongs to **Part 1** (Photographs), **Part 2** (Question-Response), **Part 3** (Conversations, Q32-70), **Part 4** (Talks, Q71-100), or **Part 5** (Incomplete Sentences).
  - **Question Selection Rule**:
      - For Parts 1, 2, and 5: If the user specifies question numbers (e.g., `1, 3` or `119`), process and output ONLY those specified questions. Otherwise, output ALL visible questions in numerical order.
      - For Parts 3 and 4: Each conversation/talk set (typically 3 questions) forms a SINGLE PSV row. By default, populate all 3 questions in `Q1_Block`, `Q2_Block`, and `Q3_Block`. If the user specifies particular question numbers (e.g., `only 62, 63`), populate ONLY the corresponding block(s) and leave unselected `Q_Block` field(s) completely empty (represented by adjacent pipes `||`).

### 2. Apply Global Output Format Rules

1.  **Code Block Only**: Output ONLY a single plain-text (`text`) code block containing the 4 Anki header lines and PSV rows. Do NOT include greetings, explanations, or filler outside the code block.
2.  **English-Only Content**: All output text across all fields must be in English. Translate and adapt any Japanese explanations or vocabulary notes into concise, natural English.
3.  **Single-Line & HTML Formatting**: Output one record per line using `|` as the delimiter. Never use literal line breaks or `|` inside any field content. Use HTML tags (`<br>`, `<ul>`, `<li>`, `<b>`) for internal formatting.
4.  **Clean Metadata**: Ignore printed question numbers (except where required in Part 3/4 fields), CD/track numbers, circled clue numbers, nationality flag icons, and speaker gender markers (except `[M]`/`[W]` in Part 3/4 `Script`).

### 3. Apply Part-Specific Headers & Field Schemas

#### Part 1 (Photographs - 9 Output Columns)

Headers:

```text
#notetype: TOEIC_Part1
#deck: 3. TOEIC::Part1
#separator: Pipe
#html:true
```

Row Rule: Start directly with `|` (`Note ID` blank) and end immediately after Field 9 (`VocabNotes`) with NO trailing `|` (omitting `Image` and `Audio`).

1.  `Note ID`: Blank (leading `|`).
2.  `Category`: Photo type and grammatical/lexical focus (e.g., `One Person (Action & Phrasal Verb)`, `Two People (Interaction)`, `Scene / Objects (Passive Being + p.p. vs. State)`).
3.  `SceneContext`: Concise 1-sentence objective description of what is visually shown in the photo without directly giving away the exact phrasing of the answer.
4.  `Options`: `(A) xxx<br>(B) xxx<br>(C) xxx<br>(D) xxx`
5.  `Answer`: Correct option letter and sentence (e.g., `(D) He's working on a wheel.`).
6.  `KeyStatement`: Correct answer sentence without the option letter, wrapping the key action verb/phrasal verb and target noun in `<b>` tags (e.g., `He's <b>working on</b> a <b>wheel</b>.`).
7.  `CoreLogic`: Concise 1-2 sentence explanation of how the visual scene maps to the correct phrasing, highlighting key trigger words with `<b>` tags.
8.  `DetailedExplanation`: HTML list (`<ul><li><b>(A) sentence</b>: [Trap Type / Correct Role] Brief explanation...</li>...</ul>`) covering all four options and their TOEIC Part 1 distractor traps (e.g., `Visible Noun / Wrong Action Trap`, `Being + p.p. Action vs. State Trap`, `Unpictured Object Trap`).
9.  `VocabNotes`: HTML list (`<ul><li><b>phrase/word</b>: concise English definition</li></ul>`) of key verbs, phrasal verbs, and nouns.

#### Part 2 (Question-Response - 10 Output Columns)

Headers:

```text
#notetype: TOEIC_Part2
#deck: 3. TOEIC::Part2
#separator: Pipe
#html:true
```

Row Rule: Start directly with `|` (`Note ID` blank) and end with a trailing `|` (representing blank Field 10 `Audio`).

1.  `Note ID`: Blank (leading `|`).
2.  `Category`: Question type and response style (e.g., `Who Question (Direct)`, `Where Question (Indirect)`, `Statement (Implied Request)`, `Negative/Tag Question`).
3.  `Question`: The spoken question or statement script only.
4.  `Options`: `(A) xxx<br>(B) xxx<br>(C) xxx`
5.  `Answer`: Correct option letter and sentence (e.g., `(A) Elizabeth and her colleagues in marketing.`).
6.  `Dialogue`: `Q: [Question]<br>A: <b>[Correct Answer text without the option letter]</b>`
7.  `CoreLogic`: Concise 1-2 sentence explanation focusing on the speaker's intent and why the response fits, highlighting trigger words with `<b>` tags.
8.  `DetailedExplanation`: HTML list (`<ul><li><b>(A) sentence</b>: [Trap Type / Correct Role] Brief explanation...</li>...</ul>`) covering all three options (e.g., `Associated Word Trap`, `Word Repetition`, `Similar Sound`, `Yes/No Trap`, `Tense/Pronoun Mismatch`).
9.  `VocabNotes`: HTML list (`<ul><li><b>phrase/word</b>: concise English definition</li></ul>`) of key expressions.
10. `Audio`: Blank (represented solely by the trailing `|` at the end of each line).

#### Part 3 & Part 4 (Conversations & Talks - 9 Output Columns)

Headers:

```text
#notetype: TOEIC_Part3_4
#deck: 3. TOEIC::Part3
#separator: Pipe
#html:true
```

*(Use `#deck: 3. TOEIC::Part4` if the image is Part 4.)*

Row Rule: Output ONE line per 3-question set. Start with `|` (`Note ID` blank) and end immediately after Field 9 (`VocabNotes`) with NO trailing `|`.

1.  `Note ID`: Blank (leading `|`).
2.  `Category`: `Part 3 [Topic] (Qxx-xx)` or `Part 4 [Topic] (Qxx-xx)`.
3.  `Q1_Block`: `<div class="q-title">[Q#]. [Question text]</div><div class="q-opts">(A) xxx<br>(B) xxx<br>(C) xxx<br>(D) xxx</div>`
4.  `Q2_Block`: Same format for the 2nd question in the set (leave blank if filtered out by user).
5.  `Q3_Block`: Same format for the 3rd question in the set (leave blank if filtered out by user).
6.  `Answers`: `<b>[Q#]:</b> (X) Answer &nbsp;/&nbsp; <b>[Q#]:</b> (Y) Answer &nbsp;/&nbsp; <b>[Q#]:</b> (Z) Answer`
7.  `Script`: Complete script in English. Prefix Part 3 turns with `[M]` or `[W]` separated by `<br>`. Wrap evidence sentences for each question in bold tags prefixed with the question number, like `<b>[Q62] evidence sentence.</b>`.
8.  `ExplanationBlock`: For each question, output core logic followed by a collapsible `<details>` block: `<div class="logic-item"><b>Q[xx] Logic:</b> [1-2 sentence explanation with <b>key terms</b>].</div><details><summary>Q[xx] Option Analysis</summary><ul><li><b>(A) ...</b>: [Role/Trap] ...</li>...</ul></details>`
9.  `VocabNotes`: HTML list (`<ul><li><b>phrase/word</b>: concise English definition</li></ul>`) of 4-6 key expressions.

#### Part 5 (Incomplete Sentences - 9 Output Columns)

Headers:

```text
#notetype: TOEIC_Part5
#deck: 3. TOEIC::Part5
#separator: Pipe
#html:true
```

Row Rule: Start directly with `|` (`Note ID` blank) and end immediately after Field 9 (`VocabNotes`) with NO trailing `|`.

1.  `Note ID`: Blank (leading `|`).
2.  `Category`: Grammatical or lexical category (e.g., `Part of Speech`, `Verb Tense/Voice`, `Vocabulary (Adjective)`, `Conjunction vs. Preposition`, `Pronoun`).
3.  `Question`: Original question sentence with the blank represented as `-------`.
4.  `Options`: `(A) xxx<br>(B) xxx<br>(C) xxx<br>(D) xxx`
5.  `Answer`: Correct option letter and word(s) (e.g., `(D) profitable`).
6.  `FullSentence`: Complete sentence with the correct answer filled in and wrapped in bold tags (`<b>word</b>`).
7.  `CoreLogic`: Concise 1-2 sentence explanation focusing on the fastest grammatical or contextual clue under exam conditions, with key trigger words or grammar terms in `<b>` tags.
8.  `DetailedExplanation`: HTML list (`<ul><li><b>(A) word</b>: [Part of Speech] Brief definition and reason why it is correct or incorrect.</li>...</ul>`) covering all four options.
9.  `VocabNotes`: HTML list (`<ul><li><b>phrase/word</b>: concise English definition</li></ul>`) of key vocabulary, collocations, or business expressions.

## Gotchas

  - Check trailing pipe rules carefully by part: ONLY Part 2 ends with a trailing `|` (for `Audio`); Parts 1, 3, 4, and 5 must NOT have a trailing `|`.
  - If the user uploads multiple pages in a single turn, group questions belonging to the same Part under a single code block, or output separate code blocks if different Parts are mixed.
