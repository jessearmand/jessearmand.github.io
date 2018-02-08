---
layout: post
title: "Git merge and rebase tips"
date: 2012-09-22
description: "About git merge and rebase"
categories: [git, git-merge, git-rebase, git-fetch]
---

A few tips to deal with merge conflicts:

Github Workflow
=================

New guy create a separate branch, make some changes, and create a pull request. Only the core developers are allowed to observe the pull request and merge the changes to main branch.

Good thing about pull request is, it could be discussed first, before the new guy is silently merging and pushing to the main branch.

Fetch, Merge
=============

Read [here](http://longair.net/blog/2009/04/16/git-fetch-and-merge/).

Basically, if both developers are working on the same file, they could peek at the remote changes before doing a merging.

Let's say the main developer who worked on the master branch would like to see a new feature on another branch:

    # git fetch origin master

Look at what have changed:

    # git diff origin/new_ui

Decided that it's okay

    # git merge --no-ff origin/new_ui

Note the --no-ff here:

The --no-ff flag causes the merge to always create a new commit object, even if the merge could be performed with a fast-forward. This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature

From a good answer on stackoverflow:
http://stackoverflow.com/questions/2850369/why-does-git-use-fast-forward-merging-by-default

But, this is a simple case... this is usually okay if there are no conflicting changes. If there is, it's usually better to use a fetch, rebase, merge workflow.

Fetch, Merge, Rebase
====================

An [article](http://jeffkreeftmeijer.com/2010/the-magical-and-not-harmful-rebase/) related to git flow

A good explanation is [here](http://www.randyfay.com/comment/1107#comment-1107) (read especially the example in comment-1107)

Quote:
When Is the Merge Workflow OK?

The merge workflow will do you no damage at all if you

- Only have one committer (or a very small number of committers, and you trust them all)
- You don't care much about reading your history
