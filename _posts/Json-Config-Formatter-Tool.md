---
layout: post
title: "Building a Json Config Formatter Tool (𝐉𝐒𝐎𝐍 𝐢𝐧𝐬𝐩𝐞𝐜𝐭𝐨𝐫/𝐂𝐋𝐈 𝐭𝐨𝐨𝐥).  in Python: What I Learned"
date: 2026-08-30
---

I've been learning Python by building projects rather than only following tutorials.

For this project, I wanted to build something that was a little more serious than a single Python script.

The result is a command-line JSON Inspector and DevTool that can format, validate, analyze, search, compare, and batch-process JSON files.

## The features I built

### 1. Formatting JSON
The most basic feature is formatting JSON. For example, the tool can pretty-print a JSON file:

```bash
python main.py data.json
```

### 2. Detecting duplicate JSON keys
Python's normal JSON parsing behavior can silently overwrite duplicate keys.
