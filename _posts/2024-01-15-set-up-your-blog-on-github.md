---
layout: post
title: "Set Up Your Blog On Github"
date: 2024-01-15 08:30:28 +0800
published: true
categories: [Programming Tool, Jekyll]
tags: [tutorial, blog, jekyll, github pages]
# The categories of each post are designed to contain up to two elements, and the number of elements in tags can be zero to infinity. 
# TAG names should always be lowercase
author: min
toc: true
comments: false
math: false
mermaid: false
# Mermaid is a great diagram generation tool
media_subpath: '/assets/img/2024-01-15'
image:
  path: cover.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAFCAYAAAB8ZH1oAAAAAklEQVR4AewaftIAAAC0SURBVBXBy2rCQBiA0S9DAjFYU8eoVQR39Q2kvoXLrvvIggiCLlop1NvES+wk/yQpPcebv3/U2T0jCHz+KeUxmbxyv91oxc8cf3astyt8cQ6ddNAdDXVFbnPipyaqKpHCQqB46Q3wvbpifziQXi9IUeBE6CWazFo2ywW6mzAcjFFhI+KaGi5nQymORGtsXnD82pIag338IuLwAwXTtxmSW5wIYRRR1h5xf0T4/Yk57WnGbf4ATflT50RY+A8AAAAASUVORK5CYII=
  alt: Set Up Your Blog On Github.
---

There have been a lot of instructions about how to set up a blog on Github, but it still cost me 3 nights to finish. So I believe that recording what I had done when built this blog may help others who meet the same issues.

## Run Your Blog Locally

Whether to test or to see what you've changed faster, it is recommended to run your blog locally, below is how to set up it.

### Install Dependencies for Jekyll

My system is Ubuntu-22.04.3, if yours is mac, windows or other linux, here is the place for you to get more information [Jekyll Docs: installation](https://jekyllrb.com/docs/installation/){:target="_blank"}.

#### 1. Install Ruby:

```shell
sudo apt update
sudo apt-get install ruby-full build-essential zlib1g-dev
```

> Avoid installing RubyGems packages (called gems) as the root user. Instead, set up a gem installation directory for your user account. 
{: .prompt-tip }

It is a good suggestion, though I did not follow.

#### 2. Add environment variables to configure the gem installation path

Add variables below to `.zshrc` if you use `zsh` or `.bashrc` if you use `bash`.
```shell
# Install Ruby Gems to ~/gems
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```

#### 3. Install Jekyll and Bundler
```shell
gem install jekyll bundler
```

#### 4. Change Sources if You Are in China
- For gem
```shell
gem sources --add https://mirrors.tuna.tsinghua.edu.cn/rubygems/ --remove https://rubygems.org/
```
- For bundler
```shell
bundle config mirror.https://rubygems.org https://mirrors.tuna.tsinghua.edu.cn/rubygems
```

#### 5. Trouble Shooting

- Bundler::PermissionError

I got the error below when excuse `gem install jekyll bundler`:
```shell
Bundler::PermissionError: There was an error while trying to write to `/xxx/cache/xxx.gem`. It is likely that you need to grant write permissions for that path.  
```
Using `gem install jekyll bundler --user-install` instead helped for me.

- zsh: command not found: jekyll

When I wanted to use `jekyll` command, I ran into this error `zsh: command not found: jekyll`.
I fixed it by adding this line to `.zshrc`.
```shell
export PATH=$PATH:/home/YOUR_USER_NAME/.local/share/gem/ruby/3.0.0/bin
```

### Choose a Jekyll Theme

You can pick up a theme from here [Jekyll Theme](https://jekyllrb.com/docs/themes/){:target="_blank"}.

Or just generate a default theme using `jekyll new YOUR_BLOG_NAME`

Visit [Jekyll Doc](https://jekyllrb.com/docs/){:target="_blank"} for more information.

## Set up Your Blog on Github

I choose Chirpy as my theme, if you are the same, you can visit [Getting start with Chirpy](https://chirpy.cotes.page/posts/getting-started/){:target="_blank"} for more information.

Or, you can follow other theme's instruction or just visit <https://pages.github.com/>{:target="_blank"}.

Have a nice day!