---
layout: post
title: "Convert Git EOL"
categories: junk
author: Bart Simpson
tags: git terminal bash
---

The easiest way to convert EOL from CRLF to LF of all files in git terminal

```bash
git ls-files -z | xargs -0 dos2unix
```
