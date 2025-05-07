---
layout: post
title: "Convert Git EOL"
categories: junk
author: rleon
tags: windows terminal cmd runas
---

Run an application as another user

```bash
runas /user:domain\myuser cmd
```
In this case the password will be prompted.

To avoid this, you can store your password in a file.

```bash
c:\code\pass.txt
```

And run the runas command as below.

```bash
runas /netonly /user:domain\myuser cmd < c:\code\pass.txt
```