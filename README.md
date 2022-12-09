# Security Workflows

Security Workflows contains example [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) which focus on defining code scanning steps and tooling for an organization. 

This repository and the contained workflows are intended to be used as examples.

## Why use reusable workflows?

Let's take a look at the three main reasons you might want to use a reusable workflow to manage code scanning in your environment:
* Centralization: All configurations are managed in a single location with auditing and change management associated with every modification to the configuration files.  
* Simplicity: All of the complexity is extracted away from developers.  Dev only need to worry about a single, simple caller workflow.  All the complex stuff is built into the reusable workflows stored in this repo.  Additionally, as changes are needed in your AppSec strategy, those changes are managed in one single location, instead of across hundres (thousands) of repos. 
* Flexibility: Need to add, remove, or update a tool?  No prob 👍.  Modify the centralized workflow and all your repositories will take that change on their next PR. 

## When are reusable workflows a bad idea?
If you only have code scanning enabled on a handfull of repos, it's probably best to manage those configurations separately.  A centralized code scanning workflow may be more complicated than what you need.  Remember... K.I.S.S.

If you only run CodeQL and no other code scanning tools, there's no need to add complexity with a centralized workflow.  

Finally, apps that have complex build steps or environment requirements for CodeQL are also probably not a good fit for a centralized workflow.  If your CodeQL setup process requires additional actions to configure Java versions, or really complex build commands, separate the CodeQL workflow into it's own file in the remote repo and continue to use the centralized workflow for the remaining tools.

## How do I use the workflow?
