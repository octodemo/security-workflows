# Security Workflows

Security Workflows is an example repository showing you how to use [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) for code scanning. 

This repository and the contained workflows are only intended to be used as examples.  

## Why use reusable workflows?

Let's take a look at the three main reasons you might want to use a reusable workflow to manage code scanning in your environment:
* Centralization: All configurations are managed in a single location with auditing and change management associated with every modification to the configuration files.  
* Simplicity: All of the complexity is extracted away from developers.  Dev only need to worry about a single, simple caller workflow.  All the complex stuff is built into the reusable workflows stored in this repo.  Additionally, as changes are needed in your AppSec strategy, those changes are managed in one single location, instead of across hundres (thousands) of repos. 
* Flexibility: Need to add, remove, or update a tool?  No prob 👍.  Modify the centralized workflow and all your repositories will take that change on their next PR. 

## When **not** to use reusable workflows?
If you only have code scanning enabled on a handfull of repos, it's probably best to manage those configurations separately.  A centralized code scanning workflow may be more complicated than what you need.  Remember... K.I.S.S.

If you only run CodeQL and no other code scanning tools, there's no need to add complexity with a centralized workflow.  

Finally, apps that have complex build steps or environment requirements for CodeQL are also probably not a good fit for a centralized workflow.  If your CodeQL setup process requires additional actions to configure Java versions, or really complex build commands, separate the CodeQL workflow into it's own file in the remote repo and continue to use the centralized workflow for the remaining tools.

## How does the reusable workflow work?
 [This](.github/workflows/code-scanning.yml) reusable defines the steps for multiple tools including:
 * CodeQL
 * Dependency Review
 * Anchore
 * tf-sec
 * OSSF Scorecards

At the start of the workflow, we define inputs.  These inputs define the languages for CodeQL, build steps for CodeQL (if needed), and the option to skip any of the tools included in the workflow.
``` yaml
on: 
  workflow_call:
    inputs:
        languages:
          required: true
          type: string
        skip-codeql: 
          required: false
          type: boolean
          default: false
        ...
        skip-anchore: 
          required: false
          type: boolean
          default: false
        skip-dependency-review: 
          required: false
          type: boolean
          default: false
        build-command: 
          required: false
          type: string
          default: ''
```
 The following sections are all separate jobs which are run as part of the workflow.  Each job represents a different tool that is executed.

## How do I call the workflow?
Calling the centralized workflow is simple.  In the repository where you want to introduce code scanning, create a new Actions workflow file in `.github/workflows` and name it `<name_you_choose>.yml`.  The workflow will look like this:
``` yaml
name: Centralized Code Scanning

on:
  push:
    branches: [ "main" ]
  pull_request:
    # The branches below must be a subset of the branches above
    branches: [ "main" ]
  schedule:
    - cron: '42 13 * * 1'

jobs:
  # This job calls the centralized code scanning workflow
  scanning:
    uses: octodemo/security-workflows/.github/workflows/code-scanning.yml@main
    secrets: inherit
    with:
      # This section is used to tell CodeQL which languages you need to scan in the repo
      languages: " [ "python", "javascript" ] "
      
 ```
 
 To skip a specific test from in the caller workflow, add a flag:
``` yaml
name: Centralized Code Scanning

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
  schedule:
    - cron: '21 17 * * 5'


jobs:
  scanning:
    uses: octodemo/security-workflows/.github/workflows/code-scanning.yml@main
    secrets: inherit
    with:
      languages: " [ 'go'] "
      skip-scorecards: true 
      
```
