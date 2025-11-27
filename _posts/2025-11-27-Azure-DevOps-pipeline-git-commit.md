---
layout: post
title: "Azure DevOps pipeline git commit"
categories: junk
author: rleon
tags: azure devops pipeline git
---

This article describes how to do git commit in Azure DevOps pipeline.
Some time ago the need to do commit in pipeline for Node.js projects arose.
This pipeline task was tested on Azure DevOps Server version 2022.2 on-premise with Linux agent.

```yaml
- script: |
    # Config git username and email
    git config --global user.name "Azure DevOps Agent"
    git config --global user.email "pipeline@azure.com"

    # Declare branch variables
    branchSource="$(Build.SourceBranch)"
    branchSourceName=${branchSource#"refs/heads/"}

    # Check if the branch exists locally and delete it
    if git show-ref "refs/heads/$branchSourceName" 2>/dev/null
    then
        echo "-----Git: delete existing local branch: $branchSourceName-----"
        git branch -D "$branchSourceName"
    fi

    # Create the branch
    git checkout -b "$branchSourceName"

    # Git add and commit all changes
    git add .
    git commit -m "🚀 By TFS: Bump version $(ArtifactVersion) [skip ci]"
    git push origin "HEAD:$branchSourceName"

  displayName: 'Azure|git|commit' 
```

#### Important:
- `[skip ci]` in the commit message prevents infinite pipeline loops to trigger.
- When pushing the changes, the pipeline will be in detached head, so the way to overcome this is to use the git push origin `HEAD:branch_name` syntax.
- Also you need to grant permissions to the build service.  More info here: [https://learn.microsoft.com/](https://learn.microsoft.com/en-us/azure/devops/pipelines/scripts/git-commands?view=azure-devops&tabs=yaml) 