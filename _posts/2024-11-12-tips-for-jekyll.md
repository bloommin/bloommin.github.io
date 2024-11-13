---
layout: post
title: Tips For Jekyll
date: 2024-11-12 14:21:03 +0800
published: true
categories: [Programming Tool, Jekyll]
tags: [tips, jekyll, github pages]
# The categories of each post are designed to contain up to two elements, and the number of elements in tags can be zero to infinity.
# TAG names should always be lowercase
author: min
toc: false
comments: false
math: false
mermaid: false
# Mermaid is a great diagram generation tool
media_subpath: /assets/img/2024-11-12
image:
  path: cover81.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAklEQVR4AewaftIAAACjSURBVAXB21LCMBRA0Z00LSS9qAO++IPO+O4XOyq2FhSQyeWc1rXM88vrOrSeSylkVdyy4CWT2o5FIpva06hg394/EVFYVvY+sOtadn1g6LZglKxCshZXO8f19Mutspznkf3TA4doCfqHuSY0bGjMio0xMdzf0TQ1H8cDRRK5CPI9U9aKXISf44Qbp4mvoUf9Ft8/croYXLWiwRNvEVM5xvnMPzW8U1Qk9N+QAAAAAElFTkSuQmCC
  alt: Tips For Jekyll
---

## Hyper Link

- If we add a link like this: `[link](some url)`, it will take us to the destination on the same page. 
- If you want to open it on a new tab, we can write it like this `[link](another url){:target="_blank"}`.
- To change links in header or footer, search `<a href` in project, then add `target="_blank"` before its `>`, finally, it will like this `<a href="some url" target="_blank">`. Now, you can open the link on a new tab.
