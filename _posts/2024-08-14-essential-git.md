---
layout: post
title: Essential Git
date: 2024-08-14 13:37:01 +0800
published: true
categories: [Programming Tool, Git]
tags: [git]
# The categories of each post are designed to contain up to two elements, and the number of elements in tags can be zero to infinity.
# TAG names should always be lowercase
author: min
toc: true
comments: false
math: false
mermaid: false
# Mermaid is a great diagram generation tool
media_subpath: /assets/img/2024-08-14
image:
  path: cover.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAklEQVR4AewaftIAAACOSURBVE3BQQqCUBRA0fv0lQpl2MiJuI820T7aYrSE9hCNkgwhK9Df+z8Mgs6RarsLzgyiiDp9sckbNDZGZxz6itbnzFRR9zZGM4gCVZGxXpVomnB5Rtw6z+gNRFAmIkyOjeHaK8UiYd+XDD7mR5kEQOA+BE6WcX4IPguA8BVA+RMTqJcpc1W6d8zgPAhfHyz7OE8vG/oIAAAAAElFTkSuQmCC
  alt: Essential Git
---

I got most knowledge about git from [here](https://git-scm.com/doc){:target="_blank"}.

## Files in Working Directory

According to [this](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository){:target="_blank"}.

> Remember that each file in your working directory can be in one of two states: **tracked** or **untracked**. Tracked files are files that were in the last snapshot, as well as any newly staged files; they can be unmodified, modified, or staged. In short, tracked files are files that Git knows about.
>
> Untracked files are everything else — any files in your working directory that were not in your last snapshot and are not in your staging area. When you first clone a repository, all of your files will be tracked and unmodified because Git just checked them out and you haven’t edited anything.
>
> As you edit files, Git sees them as modified, because you’ve changed them since your last commit. As you work, you selectively stage these modified files and then commit all those staged changes, and the cycle repeats.

![life cycle](lifecycle.png){:lqip="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAECAYAAAC3OK7NAAAAAklEQVR4AewaftIAAACmSURBVBXBrQ8BYRwH8O/z3FO83BUvMxUXTBC1K7L/QpIoomkkzaZekzSN3UZQTGVT3SaYgHMv7vfD5yOc9XJaqpj78sLZWeR1tlrK3ujtohtYTe3tXzKnQr16PbtiMh4aad2oRVGEMPCzQsgcwA0imgshTGZ+dvsDWwEIPe/VEsBBSnkk5huYH5pSdybOA5TAjwLwoZhGAMcADAA9ADMASWJagRl/X72cRyRIE6qiAAAAAElFTkSuQmCC"}

## Difference Between HEAD, Working Tree And Index

All we need is written [here](https://www.geeksforgeeks.org/git-difference-between-head-working-tree-and-index/){:target="_blank"}.

> Git manipulates three trees in its normal operation:
>
> - HEAD: Last commit snapshot, next parent
> - Index: Proposed next commit snapshot 
> - Working Directory: Sandbox