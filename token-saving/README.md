# token-saving

A Claude Code skill that reduces token consumption when working in non-English languages. It uses Gemini flash-lite to transparently translate your prompt into English before Claude processes it, then translates Claude's response back into your language — so you interact entirely in your native tongue while Claude works in the more token-efficient English.

## Why this saves tokens

Claude's tokenizer is optimised for English. Non-Latin scripts (Chinese, Japanese, Korean, Arabic, etc.) typically cost **2–3× more tokens** per character than equivalent English text. By routing prompts through Gemini for translation first, you reduce the token load on Claude for both input and output — especially noticeable on long conversations or large prompts.

---

## Prerequisites

1. **Claude Code** — CLI, desktop app, or IDE extension installed and signed in.
2. **gemini-cli MCP server** — the `mcp__gemini-cli__chat` tool must be available in your Claude Code session. Install it by adding the gemini-cli MCP server to your Claude Code settings:
   ```json
   {
     "mcpServers": {
       "gemini-cli": {
         "command": "npx",
         "args": ["-y", "@google/gemini-cli-mcp"]
       }
     }
   }
   ```
   Then authenticate with your Google account:
   ```
   ! gemini
   ```
   Follow the login prompt. Once authenticated, the `mcp__gemini-cli__chat` tool will be available.

---

## Installation

Copy the skill directory into your Claude Code global skills folder:

```bash
cp -r token-saving ~/.claude/skills/token-saving
```

Or clone/download this skill directly:

```bash
mkdir -p ~/.claude/skills/token-saving
curl -o ~/.claude/skills/token-saving/SKILL.md \
  https://raw.githubusercontent.com/your-repo/token-saving/main/SKILL.md
```

Restart Claude Code (or start a new session) for the skill to be picked up.

---

## Usage

```
/token-saving <your prompt in any language>
```

The skill will:
1. Send your prompt to **Gemini 2.5 flash-lite** to detect the language and translate it to English.
2. Pass the English prompt to **Claude** and generate a response.
3. Send Claude's English response back to **Gemini 2.5 flash-lite** to translate it into your original language.
4. Return only the translated response — the intermediate English steps are invisible to you.

---

## Examples

**Traditional Chinese**
```
/token-saving 請幫我寫一個用 Python 讀取 CSV 檔案並計算每欄平均值的函式
```
> Claude receives: *"Please write a Python function that reads a CSV file and calculates the average value of each column."*
> You receive the answer in Traditional Chinese.

**Japanese**
```
/token-saving Dockerfileを最適化してイメージサイズを小さくする方法を教えてください
```
> Claude receives: *"Please tell me how to optimize a Dockerfile to reduce the image size."*
> You receive the answer in Japanese.

**Spanish**
```
/token-saving ¿Cuáles son las diferencias entre REST y GraphQL?
```
> Claude receives: *"What are the differences between REST and GraphQL?"*
> You receive the answer in Spanish.

---

## How it works

```
User prompt (any language)
        │
        ▼
  Gemini flash-lite
  (detect language + translate → English)
        │
        ▼
     Claude
  (processes English prompt)
        │
        ▼
  Gemini flash-lite
  (translate response → original language)
        │
        ▼
User response (original language)
```

The translation model used is `gemini-2.5-flash-lite`, chosen for its speed and low cost, keeping the overhead of the translation steps minimal.
