---
layout: post
title: Tips For Git
date: 2024-11-12 14:29:15 +0800
published: true
categories: [Programming Tool, Git]
tags: [tips, git]
# The categories of each post are designed to contain up to two elements, and the number of elements in tags can be zero to infinity.
# TAG names should always be lowercase
author: min
toc: true
comments: false
math: false
mermaid: false
# Mermaid is a great diagram generation tool
media_subpath: /assets/img/2024-11-12
image:
  path: cover7.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAklEQVR4AewaftIAAACYSURBVBXB2QrCMBBA0TuTia0tblD9S1999k9FVMS1YEXUpIl4jixX65xTQlV4tg/GwxKngjpFRPgTUSzGSN8nVATMU9UVw2JACIGu68gZRBULoae932iaBmeOzXbHtPaYeUSNlCG+PxgiiCq364X5fEHyBfvDEc1fcoo4MyazBvXmGI0nZHGcLyfMHFZX9CSsrLCy5vX+8AOAsDlbAu1ZqgAAAABJRU5ErkJggg==
  alt: Tips For Git
---

Here are something about git I didn't know before, and I write them down for reviewing lately.

## Git Diff

- `git diff`

> To see what you’ve changed but not yet staged

- `git diff --staged` or `git diff --cached`

> To see what you’ve staged that will go into your next commit

If you are not very aware of **staged**, check out [this](/posts/essential-git/#files-in-working-directory) to help get a deeper understanding.

## Git Reset

- `git reset`

> Reset current **HEAD** to the specified state.

- `git reset --soft`

> Does not touch the **index** file or the **working tree** at all (but resets the **HEAD** to <commit>, just like all modes do). This leaves all your changed files "Changes to be committed", as git status would put it.

- `git reset --mixed`

> Resets the **index** but not the **working tree** (i.e., the changed files are preserved but not marked for commit) and reports what has not been updated. This is the default action.
> 
> If -N is specified, removed paths are marked as intent-to-add (see git-add[1]).

- `git reset --hard`

> Resets the **index** and **working tree**. Any changes to tracked files in the **working tree** since <commit> are discarded. Any untracked files or directories in the way of writing any tracked files are simply deleted.

If you are not clear with **HEAD**, **working tree** or **index**, you may want to read [this](/posts/essential-git/#difference-between-head-working-tree-and-index).