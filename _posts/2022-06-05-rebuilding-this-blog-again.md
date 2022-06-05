---
title: Rebuilding this blog... again!
subtitle: Welcome!
tags:
- Meta
- Programming
cover-img: assets/img/post/jekyll_logo.png
---

This is the forth incarnation of my personal blog.

The first one was on blogger when I was in highschool. 

The second one was on a self hosted installation of wordpress, but eventually my billing for the underlying VM failed silently and that was deleted off the internet. As far as life lessons go, losing a couple years worth of posts is a pretty cheap price to pay to internalize the value of frequent backups, but I digress.

The third one was built on github pages with Jekyll using the [Scriptor-Jekyll-Theme](https://github.com/JustGoodThemes/Scriptor-Jekyll-Theme). Overall I was very happy with the hosting, and the theme was *fine*. What didn't end up working for me was that most of the computers I own run windows and the dev experience to publish a post as I had set it up before required updating markup files in a linux environment (or WSL). 

Which brings us to lucky number 4: For the current incarnation, I kept the github pages hosting. I switched up the theme to [Beautiful Jekyll](https://beautifuljekyll.com/) and resolved to make the writing experience fit my setup a bit better. 
&nbsp;

### Step 1: Write, upload and edit posts in Windows
The [docs](https://jekyllrb.com/docs/installation/windows/) seemed to claim that this was possible: 
 > While Windows is not an officially-supported platform, it can be used to run Jekyll with the proper tweaks.


In practice, [RubyInstaller](https://rubyinstaller.org/) did install Ruby, but failed out on installing *gcc* and *make* so if you want to try this, you might as well install everything manually. 
### Step 2: Write posts in a WYSIWYG editor

Part of the reason I even got started down this project is that I came across [jekyll-admin](https://github.com/jekyll/jekyll-admin), a plugin that adds a local CMS interface for you to manage posts. 

The editor itself is pretty rudimentary, but I was dying for even an 80% solution to this problem. 

After spending a couple of hours this Sunday with it, tinkering with the theme's default parameters, the editor's settings and jekyll's strict mode for reference verification, the final experience isn't that far off from what I used to have in my wordpress setup. 

Which is amazing! Having a *free* staticially built site with a streamlined writing flow is the best of all worlds. 

But let's see how it goes!

P.S: I'll get around to tinkering with the color scheme and the CSS *eventually*
