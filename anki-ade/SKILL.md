---
name: anki-ade
description: "Generate Anki Active Daily English (ADE) Cloze-deletion Pipe-Separated Values (PSV) flashcards AND 16:9 storybook-style context illustrations simultaneously in a single turn. Trigger when the user starts input with \"ade \" (e.g., \"ade work\", \"ade run out of\") or inputs \"image\" to regenerate an illustration."
---
# Anki ADE

## Summary

Generate both an import-ready Cloze-deletion Anki flashcard in Pipe-Separated Values (PSV) format and a 16:9 storybook-style context illustration in a single turn for the `ActiveDailyEnglish` Anki note type.

## When to Use

  - **Default Simultaneous Mode (`ade [word/phrase]`)**: The user inputs one or more English words, phrasal verbs, idioms, or expressions prefixed with ` ade  ` (e.g., `ade work`, `ade run out of`). Output BOTH the PSV code block and the 16:9 context illustration in the same turn.
  - **Image-Only Follow-up Mode (`image`)**: The user inputs `image` (or requests an illustration for a specific row) to generate or regenerate the 16:9 illustration without repeating the PSV block.

## Steps

### 1. Construct PSV Data (10 Fields)

1.  **Format the Code Block**:
      - Enclose the PSV output in a single `text` code block with no conversational filler, greetings, or explanations outside the code block.
      - Always place these exact four lines at the top of the code block:

```text
#notetype: ActiveDailyEnglish
#deck: 2. Active Daily English
#separator: Pipe
#html: true
```

  - Use a single vertical pipe `|` between fields. Never use pipe characters `|` or literal line breaks inside any field value.
  - Start every data row with a single pipe `|` (representing the empty `Note ID` field), immediately followed by Field 2 (`Expression`).
  - If Field 10 (`Extra`) contains text, separate it with `|` and do not add a trailing pipe after it. If Field 10 (`Extra`) is empty, omit the pipe after Field 9 (`Answer`) so the line ends immediately after `Answer`.

2.  **Populate the 10 Fields in Strict Order**:
    1.  **Note ID**: Leave blank (represented solely by the leading `|`).
    2.  **Expression**: Strip the leading ` ade  ` prefix and enter the target phrase in its base/dictionary form (e.g., `stand up for`, `take something for granted`).
    3.  **Definition**: Write a concise, clear definition in English starting with the capitalized expression in quotes (e.g., `"Stand up for" means to...`).
    4.  **Nuance**: Explain in English when and how the expression is used, its register/tone (e.g., casual, formal, workplace), grammatical behavior (e.g., transitive/intransitive), and subtle differences from similar expressions.
    5.  **Synonym**: List 2-4 natural English synonyms or paraphrases separated by commas. Leave blank if no close equivalent exists.
    6.  **Text** *(Cloze Deletion Sentence)*:
          - **Simplicity, Realism & Quiz Quality**: Write a simple, realistic, and unambiguous sentence (or short two-sentence context for discourse/relational markers like `as it turns out`) grounded in real-life usage with clear logical clues.
          - **Grammatical Flexibility & Contiguity**: Freely inflect the target expression (past tense, passive voice, progressive aspect, adjusted possessives) to make the sentence natural. Keep separable phrasal verbs adjacent so the entire expression fits inside one cloze tag.
          - **Anki Cloze Format**: Wrap the exact inflected phrase using `{{c1::<inflected_phrase>::<underscores>}}`.
          - **Underscore Hint Rule**: Match the exact word count of `<inflected_phrase>` using groups of five underscores (`_____`) separated by single spaces (e.g., 2 words `ran out` -> `_____ _____`; 3 words `was called off` -> `_____ _____ _____`).
    7.  **Image**: Always leave blank in the PSV text (output only the delimiter, resulting in `||` between `Text` and `Hint`).
    8.  **Hint** *(Cloze Cue & Visual Context)*: Must start with ` <b>Situation:</b>  ` followed by a concrete description of the scenario, participants, and underlying intent or outcome. It must (1) help the learner deduce the missing cloze phrase conceptually without giving away the target words, and (2) provide clear visual details (setting, characters, actions) for the illustration.
    9.  **Answer** *(Full Sentence)*: Output the exact same text as `Text`, with the cloze markup removed and the target inflected phrase wrapped in `<b>...</b>` tags (e.g., `She <b>stood up for</b> her rights explicitly.`).
    10. **Extra** *(Optional)*: Include high-value supplementary notes such as grammar tips, common collocations, antonyms, or etymology. If nothing meaningful adds value, leave blank and omit the pipe after `Answer`.

### 2. Generate 16:9 Context Illustration (Simultaneously in the Same Turn)

In the same turn as constructing the PSV data, invoke the image generation tool to generate the illustration based on the `Text` and `Hint` fields:

  - **Aspect Ratio**: Always generate the image in a **16:9 widescreen aspect ratio** (specify `16:9` in both the tool parameters and prompt).
  - **Art Style**: Warm, hand-drawn storybook illustration style with soft, inviting textures and expressive characters. Strictly avoid photorealism or 3D CGI rendering.
  - **Composition**: Depict a clear, unambiguous visual representation of the exact scenario described in `Text` and `Hint`.
  - **Speech & Thought Bubbles**:
      - Include empty speech or thought bubbles ONLY if `Text` is clearly direct spoken dialogue (e.g., in quotation marks) or an explicit internal thought occurring in the moment.
      - Do NOT include speech or thought bubbles if `Text` is third-person narration, an objective statement of fact, or a general description.
      - Assign bubbles strictly to the active speaker or thinker, never to passive listeners.
  - **No Text Rule**: Keep all bubbles completely blank. Never generate any letters, words, or symbols inside bubbles or anywhere in the image.

### 3. Reference Examples

```text
#notetype: ActiveDailyEnglish
#deck: 2. Active Daily English
#separator: Pipe
#html: true
|run out of|"Run out of" means to use up the entire supply of something so that there is none left.|Implies an unplanned or unwanted depletion of a resource. Common in everyday speech regarding household items, time, or patience.|deplete, use up, exhaust|We {{c1::ran out of::_____ _____ _____}} milk this morning, so I need to go to the store.||<b>Situation:</b> Opening the refrigerator while making breakfast and realizing there is no milk left in the carton.|We <b>ran out of</b> milk this morning, so I need to go to the store.|Grammar Note: "Run" is irregular and changes to "ran" in the simple past.
|call off|"Call off" means to cancel a planned event, meeting, or activity.|Common in both casual and professional contexts for planned events. Frequently used in the passive voice when focusing on the canceled event rather than who canceled it.|cancel, scrub, abort|The outdoor concert {{c1::was called off::_____ _____ _____}} at the last minute due to a severe thunderstorm.||<b>Situation:</b> Concertgoers holding umbrellas outside an empty stadium stage after organizers canceled the event because of lightning.|The outdoor concert <b>was called off</b> at the last minute due to a severe thunderstorm.
```

## Gotchas

  - Always output both the `text` PSV code block and the generated 16:9 image in the same response when triggered by `ade [word/phrase]`. Do not let the "no conversational filler" rule prevent calling the image generation tool.
  - Verify the underscore count in `{{c1::...::_____}}`: each word in the inflected phrase must correspond to one `_____` block separated by spaces.
  - Never put a pipe between empty `Image` and `Hint` other than the standard delimiter (which looks like `||` right after `Text`).
