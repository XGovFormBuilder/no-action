# no-action


> Please be aware that there's someone going around raising malicious PRs on repos - we just got one on one of ours trying to introduce bitcoin mining into our Github Actions workflow

## Problem

GitHub actions or workflows will run based on events. When a repo is forked, and a PR is requested against the base repo, the workflow which has been introduced will run on the base repo's runners. 

AFAIK - you cannot prevent this from happening. It should also not harm you, for the most part it might run up your credits and/or put your other workflows in a queue. 

On the pull_request event, head repos (with the new change), will not have access to secrets if you have defined them in the gh secrets area. 

## Fix
To prevent a PR with an action which will run a workflow
use [cancel.yml](https://github.com/XGovFormBuilder/no-action/blob/main/.github/workflows/cancel.yml) in your own repo 


This is what happens:
1. PR is opened from a fork, with a workflow that runs some mining actions
2. for simplicity’s sake.. I’ll say all workflows that can be run, will be run. This means xgovformbuilder repo, as well as any from the fork
3. The cancel.yml workflow is set to start when any other workflows start (so the ones in 2.)
4. if in the github action context, we see that the event named “workflow_run”, has the value head_repository.fork == true
  a. then we run the action that makes an API request to cancel that workflow
  b. if not, we don’t run any cancels, it hasn’t affected our current pipeline in any way
  
  
## Try it!

1. Fork this repo
2. disable the cancel.yml workflow
3. Make changes to `https://github.com/XGovFormBuilder/no-action/blob/main/.github/workflows/good.yml` which simulates a CI action
4. Open a PR against this repo
5. Watch the actions tab for this repo: https://github.com/XGovFormBuilder/no-action/actions

You should see that, the CI action runs in your repo, but after about ~40s, your workflow will be cancelled on this repo.
