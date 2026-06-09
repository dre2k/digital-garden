---
title: Gemma 4 with Ollallallama
tags:
  - ai_slop
permalink: gemma_with_ollama
---

## My stuff

- 2023 14-inch MacBook Pro (18gb ram)
- Installing `gemma4:e4b`

## Steps

Install `Ollama` - https://ollama.com

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Pull model:

```bash
ollama run gemma4:e4b
```

Install `Continue` on VSCode

I'm using this setting: 

```yaml
name: Local Config
version: 1.0.0
schema: v1
  
models:
  - name: Gemma 4 (Fast Mode)
    provider: ollama
    model: gemma4:e4b
    roles:
      - chat
      - edit
    systemPrompt: "Respond directly to the user. Do not include any <|think|> or reasoning tokens, and do not use a thought channel. Output only the final response."
  - name: Gemma 4 (Deep-Think Mode)
    provider: ollama
    model: gemma4:e4b
    roles:
      - chat
    systemPrompt: "You are a software engineering assistant. Use your internal reasoning channel to think deeply before answering."

  

tabAutocompleteModel:
  name: Gemma Autocomplete
  provider: ollama
  model: gemma4:e4b

systemPrompt: "Respond directly. No thinking blocks."
```



Since models are local, I feel better about marrying it with Obsidian 
- Install `Copilot` (community plugin)
- Add the ollama model (`gemma4:e4b` in settings, then select it)

For Q&A Feature, use model for creating embedding of all notes

```bash
ollama run nomic-embed-text
```

then add it under 'embedding models' in `Copilot` settings