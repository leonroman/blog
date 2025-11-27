---
layout: post
title: "Azure DevOps - agents inventory pipeline"
categories: azure devops pipeline
meta: "devops"
---

```yaml
trigger:
- master

pool:
  name: AWS_DEV

stages:
- stage: DEV
  displayName: 'DEV stage'
  jobs:
  - job: AgentPoolList
    displayName: 'AgentPoolList'
    steps:
    - task: Powershell@2
      displayName: 'AgentPoolList'
      inputs:
        targetType: 'inline'
        script: |
          $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
          $headers.Add("Content-Type", "application/json")
          $headers.Add("Authorization", "Basic OnhvbX********lrcWE=")

          $responsePool = Invoke-RestMethod 'https://<hostname>/<project>/_apis/distributedtask/pools?api-version=7.0' -Method 'GET' -Headers $headers

          $jsonPoolData = $responsePool | ConvertTo-Json | ConvertFrom-Json
          $poolsArray = $jsonPoolData.value
          foreach ($pool in $poolsArray){
            Write-Host "Pool: $($pool.name), Id: $($pool.id)"

            $responseAgent = Invoke-RestMethod "https://<hostname>/<project>/_apis/distributedtask/pools/$($pool.id)/agents?api-version=7.0" -Method 'GET' -Headers $headers

            $jsonAgentData = $responseAgent | ConvertTo-Json | ConvertFrom-Json
            $agentsArray = $jsonAgentData.value
            foreach ($agent in $agentsArray){
              Write-Host "  Agent: $($agent.name), Id: $($agent.id), Version: $($agent.version), osDescription: $($agent.osDescription), status: $($agent.status)"
            }
          }
```