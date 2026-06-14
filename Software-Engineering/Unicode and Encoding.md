---
id: dev-052-0000-0000-0000-000000000036
title: Unicode and Encoding
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781800000014
---
# Unicode and Encoding

**Links**: [[Tokenization]] | [[Internationalization]] | [[Data Serialization]] | [[Web Security]] | [[Programming Language Paradigms]]

## What is Unicode?

A universal standard assigning a unique number to every character.

```
U+0041 = 'A'       U+03A9 = 'Ω'      U+1F600 = '😀'
```

## UTF-8 Encoding

| Range | Bytes | Byte Pattern |
|-------|-------|-------------|
| U+0000 - U+007F | 1 | 0xxxxxxx |
| U+0080 - U+07FF | 2 | 110xxxxx 10xxxxxx |
| U+0800 - U+FFFF | 3 | 1110xxxx 10xxxxxx 10xxxxxx |
| U+10000+ | 4 | 11110xxx ... |

## Python

```python
text = "Hello 世界 🌍"
encoded = text.encode("utf-8")     # str → bytes
decoded = encoded.decode("utf-8")  # bytes → str

# Handle encoding errors
safe = text.encode("ascii", errors="replace")
```

## Common Pitfalls

- Never mix `str` and `bytes`
- Always specify encoding when reading files: `open("f.txt", encoding="utf-8")`
- Emoji can be multi-codepoint (ZWI sequences: "👨‍👩‍👧‍👦" is 7 codepoints)

**Next**: [[Internationalization]] — Building for global users