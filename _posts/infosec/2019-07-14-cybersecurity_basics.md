---
layout: post
title:  "Cybersecurity Basics"
date:   2019-07-14 11:38:45 +0800
categories: infosec
---

> Notes on basic cybersecurity knowledge

# Networking


# Misc

## File headers

- Commonly termed magic bytes / file signatures.
- Tells us the type of file at the beginning of a file
- A plain text file doesn't have a file header 
- 50 4B 03 04 (for a normal zip archive)
- 50 4B 05 06 (for an empty zip archive)
- 50 4B 07 08 (for a spanned zip archive)
- jpg files FF D8 FF DB or FF D8 FF E0 or FF D8 FF E1
- Uses pairs because 2 hex characters = 1 byte (8 bits) of data58 68 63 31 4a 69 36 43 64 7a