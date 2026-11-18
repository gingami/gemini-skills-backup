---
name: anki-il
description: Generate Anki Immersion Learning (IL) 16:9 educational flashcard images (image-only via "il ") or simultaneous Pipe-Separated Values (PSV) flashcard data and 16:9 flashcard images (via "psv " or English sentences with square brackets "[ ]" around target words). Also supports "psv" or "image" as a follow-up in an Anki IL session.
---

# Anki IL

## Summary

Generate minimalist 16:9 split-canvas educational flashcard images only (Mode A) or import-ready Pipe-Separated Values (PSV) flashcard data simultaneously with 16:9 flashcard images in a single turn (Mode B) for the `ImmersionLearning` Anki note type.

## When to Use

  - **Mode A (Flashcard Image-Only Generation — No PSV)**:
      - The user starts the input with ` il  ` followed by a word or phrase (e.g., `il work`). Do NOT generate PSV data in this mode.
      - The user types `image` immediately after generating an Anki IL PSV row or image to generate/regenerate only the 16:9 illustration.
  - **Mode B (Simultaneous PSV Data + 16:9 Image Generation)**:
      - The user starts the input with ` psv  ` followed by a word, phrase, or list (e.g., `psv work`).
      - The user inputs one or more English sentences containing square brackets `[ ]` marking target expressions (e.g., `Hard [work] always pays off.`, `I [ran into] my old friend.`).
      - Output BOTH the PSV code block AND the 16:9 flashcard illustration(s) simultaneously in the same turn.
  - **PSV-Only Follow-up (`psv`)**:
      - The user types `psv` immediately after generating a Mode A flashcard image (convert the target and example sentence from that image into PSV).

## Steps

### Mode A: Flashcard Image Generation (Image Only)

Execute the following steps sequentially without asking unnecessary questions, and do NOT output PSV data:

1.  **Normalize Input**: Strip any leading ` il  ` prefix. Identify the target English word or phrase and retain its base/dictionary form for the flashcard header. If the word has multiple meanings, select the most frequent everyday usage unless context is specified.
2.  **Formulate Example Sentence**:
      - Write a natural English example reflecting a realistic, practical situation (or reuse the sentence from the previous turn if triggered via follow-up `image`).
      - **Inflection & Usage Priority**: Freely inflect or adapt the target word (past tense, passive voice, progressive aspect, third-person singular, or plural) whenever it makes the sentence more authentic.
      - **Standard Words & Phrases**: Keep the sentence strictly concise (6-10 words) to maximize legibility and prevent text rendering errors.
      - **Context-Dependent Expressions** (e.g., discourse markers or transitional phrases like *as it turns out*, *nevertheless*, *in the meantime*): Expand up to 12-16 words (one compound sentence or two ultra-short sentences) so the premise, contrast, or twist is clear on its own.
3.  **Conceptualize Visual Scene**:
      - Describe a concrete, unambiguous visual scenario depicting the meaning, tense, and state of the example sentence.
      - For contrast or unexpected outcomes, include visual cues showing both premise and outcome in a single cohesive scene.
      - Exclude any text, letters, numbers, signs, or speech bubbles from the artwork area. Specify a soft, solid background color for the illustration area so it separates cleanly from the bottom white margin.
4.  **Generate Image**:
      - Call the image generation tool with a 16:9 aspect ratio by default. Do not output the raw prompt text in the chat response. Use this exact prompt structure:

<!-- end list -->

``` text
A minimalist, flat vector-style educational illustration depicting: [Specific visual description of the scenario from Step 3, with a soft solid background color and no text inside the artwork].
Composition & Layout: 16:9 widescreen aspect ratio, split-canvas flashcard layout. The upper 80% is an edge-to-edge illustration with no borders on the top, left, or right. The bottom 20% is a stark white horizontal footer margin clearly separated from the upper artwork.
Typography: Centered inside the bottom white margin, print crisp, legible black text in a clean sans-serif font.
Line 1 (bold): "[Target English word/phrase in base form]"
Line 2 (regular): "[Natural English example sentence(s) from Step 2, cleanly wrapped onto two lines if longer than 10 words]"
Constraints: 16:9 aspect ratio, minimalist educational aesthetic, clean vector shapes, high quality, strictly no extra text, no watermarks, no Japanese characters.
```

### Mode B: Simultaneous PSV Data + 16:9 Image Generation

When triggered by `psv ...` or bracketed input `[ ]`, execute **both** Part 1 (PSV Data) and Part 2 (16:9 Flashcard Image) simultaneously in the same response:

#### Part 1: Construct PSV Data

##### 1\. Output Constraints

  - **Code Block Only (for text portion)**: Output ONLY a single `text` code block for the PSV data. Do not include greetings, explanations, or conversational filler outside the code block.
  - **Mandatory File Headers**: The first four lines inside the code block must be exact:

<!-- end list -->

``` text
#notetype: ImmersionLearning
#deck: 1. Immersion Learning
#separator: Pipe
#html: true
```

  - **Single-Line Records**: Each vocabulary entry must occupy strictly one line with no line breaks inside fields.
  - **Delimiter Integrity**: Use `|` (pipe) as the delimiter. Never include literal `|` characters inside any field content.
  - **Pipe Count**: Every data row must start with `|` (representing the empty `Note ID` field) and contain 7 pipes total across the 8 populated fields, terminating immediately after `CEFR` with no trailing pipes for empty fields 9-11 (`|Expression|IPA|POS|Definition|Synonyms|Example|CEFR`).

##### 2\. Processing & Linguistic Rules

  - **Target Extraction & Grouping**:
      - **Bracketed Input (`[ ]`)**: Extract phrases enclosed in square brackets. If multiple brackets in a single sentence belong to one split expression (e.g., `Don't [take] his kindness [for granted].`), combine them into a single row (`take sth for granted`) and wrap each part separately in `<B><U>...</U></B>` in the example sentence. If a sentence contains distinct targets, generate one row per unique target and strip `[ ]` from non-target words in that row.
      - **Unbracketed Input (`psv ...`)**: Strip the leading ` psv  ` prefix and treat each standalone word or phrase (or the previous turn's Mode A card) as a target expression.
  - **Lemmatization (`Expression`)**:
      - Convert verbs to base dictionary form (e.g., `ran into` -> `run into sb`).
      - Standardize generalized objects using `sb` (someone) and `sth` (something) based on transitivity and context (e.g., `take sth for granted`).
      - Prepend `be` to predicate adjective phrases governed by a linking verb (e.g., `[interested in]` -> `be interested in`).
  - **Linguistic Metadata**:
      - `IPA`: General American IPA enclosed in slashes `/.../` for the `Expression` lemma. Expand `sb` as `/ˈsʌmbədi/` and `sth` as `/ˈsʌmθɪŋ/`.
      - `POS`: Grammatical category (`noun`, `verb`, `adjective`, `adverb`, `phrasal verb`, `idiom`, `collocation`).
      - `Definition`: Concise, learner-friendly English definition matching the contextual sense. Do not use semicolons or pipes.
      - `Synonyms`: 2-3 context-appropriate synonyms separated by commas.
      - `CEFR`: Contextual proficiency level (`A1`, `A2`, `B1`, `B2`, `C1`, or `C2`).
  - **Context Sentence (`Example`)**:
      - Retain the user's sentence (silently fixing minor grammar/spelling errors) or generate one natural sentence for standalone inputs.
      - Wrap ONLY the target word(s) in `<B><U>...</U></B>` tags matching the inflected form in the sentence.

##### 3\. Column Schema & Examples

1.  `Note ID`: Leave empty (row begins with `|`).
2.  `Expression`: Lemmatized target with `sb`/`sth`/`be`.
3.  `IPA`: General American IPA of `Expression`.
4.  `POS`: Part of speech.
5.  `Definition`: Context-specific English definition.
6.  `Synonyms`: 2-3 comma-separated synonyms.
7.  `Example`: Sentence with `<B><U>target</U></B>`.
8.  `CEFR`: Contextual CEFR level (`A1`-`C2`).
9.  `Source URL`, `Image`, `Extra` (Fields 9-11): Omit completely (no trailing `|`).

Example Output:

``` text
#notetype: ImmersionLearning
#deck: 1. Immersion Learning
#separator: Pipe
#html: true
|run into sb|/rʌn ˈɪntuː ˈsʌmbədi/|phrasal verb|to meet someone by chance|bump into, encounter|I <B><U>ran into</U></B> my old friend and we chatted for hours.|B2
|take sth for granted|/teɪk ˈsʌmθɪŋ fɔːr ˈɡræntɪd/|idiom|to value something too lightly or fail to appreciate it|undervalue, overlook|Don't <B><U>take</U></B> his kindness <B><U>for granted</U></B>.|C1
```

#### Part 2: Generate 16:9 Flashcard Illustration Simultaneously

In the same turn as outputting the PSV data, invoke the image generation tool for the target expression and example sentence from Part 1:

  - **Base Form & Sentence Alignment**:
      - `Line 1 (bold)`: Use the lemmatized base form from `Expression` (strip generalized `sb`/`sth` placeholders if cleaner for display, or keep natural dictionary form).
      - `Line 2 (regular)`: Use the clean `Example` sentence from Part 1 (with `<B><U>` HTML tags removed).
  - **Visual & Layout Rules**: Follow the exact same 16:9 split-canvas flat vector style and prompt structure defined in Mode A Step 3 & Step 4.

## Gotchas

  - In Mode B (`psv ...` or `[ ]` sentences), ALWAYS output both the `text` PSV code block and the generated 16:9 image in the same turn. Do not let the "no conversational filler" rule prevent calling the image generation tool.
  - In Mode A (`il ...`), NEVER output PSV data—generate ONLY the 16:9 image.
  - Never add trailing pipes after the `CEFR` field in PSV output; doing so overwrites existing `Image` or `Extra` fields during Anki updates.
  - Never render Japanese characters or extra labels inside the illustration area.
