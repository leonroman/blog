---
layout: post
title: "Azure DevOps pipeline - git could not read Username"
categories: junk
author: rleon
tags: agent azure devops git
---
## The issues

Keep getting the following error in the logs when the pipeline is trying to git push

```
fatal: could not read Username for 'https://tfs.local': terminal prompts disabled
```

## Solution

### Set checkout persistCredentials parameter

```
steps:
- checkout: self
  persistCredentials: true
```

### Configure Azure DevOps set permissions for user "Project Collection Build Service"

**Note:** The <u>user</u> and not the ~~group~~

Grant:
```
Contribute: Allow
Create Branch: Allow
Create Tag: Allow (Inherited)
Read: Allow (Inherited)
```

**Source:** https://learn.microsoft.com/en-us/azure/devops/pipelines/scripts/git-commands?view=azure-devops&tabs=yaml