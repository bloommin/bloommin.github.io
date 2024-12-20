---
layout: post
title: Explore C++ STL Part1 Introduction
date: 2024-11-12 09:19:23 +0800
published: true
categories: [Programming Language, C++]
tags: [source analysis, c++, stl]
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
  path: cover.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAklEQVR4AewaftIAAACwSURBVAXBy2rCQABA0TuTScbEGGkKalpxpdB+hiD4+V101VWhUARbE83DyaszPUcc9wf3efohS2IK09L2PQIL1rFZJvzeOkYhUbfBEXmSNFuw9gNOecXDNOBcNnRSUJkvwjBEbncvrOcz2qam6w1PjxPq4Q8fS3EteF3OWaUR6uP9DVOVJEZzzR2xhu8KYs/yHGm6uuFCgJKexAkJwqM0LcXdon2fUSrSiWIYBKOO+AesJEfFe038eAAAAABJRU5ErkJggg==
  alt: Explore C++ STL Part1 Introduction
---

If you are familiar with C++, you must be aware of its **STL**(standard template library) which consists of **Containers**, **Algorithms**, **Iterators**, **Function Objects** and **Adapters**, it's powerful but also difficult for a learner like me to master. So I am reading a book named "The Annotated STL Sources" written by Hou Jie to help me understand it, and I will record what I learned here.

## Get Sources

There are many implemtations of C++ STL, the book choose SGI's version because it is widely used and highly readable.

You can get sources [here](https://sgistl.github.io/download.html){:target="_blank"}.

## Run Sources

Though the sources are easy to read, we still need to run and debug it for deeper understanding. However, if you download and run it, you will find it having so many errors! Its headers are designed for system use, so there are a lot of name conflicts, thanks to C++'s namespace, I don't have to change a lot.

[Here](https://github.com/bloommin/sgi_stl){:target="_blank"} is the runnable code.

## Enjoy Sources

Now you can run and debug SGI STL happilly!