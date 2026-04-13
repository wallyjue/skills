---
name: token-saving
description: Saves Claude tokens by translating non-English prompts into English using Gemini flash-lite before sending to Claude, then translates Claude's response back into the user's original language. Use when the user invokes /token-saving or wants to reduce token usage when writing prompts in non-English languages.
argument-hint: <prompt in any language>
allowed-tools: [Bash]
---

# Token-Saving Skill

Translate the user's prompt into English using Gemini, process it with Claude, then translate Claude's response back into the user's original language.

## Input

The user's original prompt (may be in any language):

$ARGUMENTS

## Instructions

Follow these steps exactly:

### Step 1 — Detect language and translate with Gemini flash-lite

Use the Bash tool to run the following command, substituting `$ARGUMENTS` into the prompt:

```bash
gemini --model gemini-2.5-flash-lite -p "Detect the language of the following text and translate it into English. Respond using EXACTLY this format with no extra commentary:
LANGUAGE: <detected language name in English>
TRANSLATION: <translated text>

Text to translate:
$ARGUMENTS"
```

### Step 2 — Extract language and translation

Parse Gemini's response to extract:
- **Original language**: the value after `LANGUAGE:` (e.g. `Traditional Chinese`)
- **Translated text**: the value after `TRANSLATION:` (everything after that label, stripped of leading/trailing whitespace)

Remember both values — you will need the original language in Step 4.

### Step 3 — Process the translated prompt

Use the extracted English translated text as if the user had typed it directly. Generate your response in English as normal.

### Step 4 — Translate the response back with Gemini flash-lite

Take your English response from Step 3 and use the Bash tool to run the following command, substituting in the original language and your English response:

```bash
gemini --model gemini-2.5-flash-lite -p "Translate the following text into <ORIGINAL_LANGUAGE>. Output ONLY the translated text with no extra commentary, labels, or formatting:

<YOUR_ENGLISH_RESPONSE>"
```

### Step 5 — Output the translated response

Output only the translated response from Step 4. Do not show the intermediate English response, the detected language, or any step labels — just the final translated text as your reply.
