---
layout: post
title: "Everyone's A Little Bit Stupid, Sometimes"
date: 2020-03-28 08:00:00-0600
categories: software programming
excerpt: We've all spent 3 hours debugging to discover we put a + where a - should be. In this post I share some of my facepalm moments.
---

It's really easy to be hard on yourself when you make a mistake, even easier when it's a ridiculously stupid mistake. You might think to yourself:

> Omg, I've been doing this for X years and I just spent hours trying to chase down this stupid bug caused by a misplaced paren! I'm a failure!

But here's a not-so-secret secret. We all do this. I've been doing this for 8 years and I just did it last week. [Scott Hanselman](https://www.hanselman.com/blog/ImAPhonyAreYou.aspx) has been in the industry for over 20 years and *he's* done it. You are not a failure!

To prove it I'll tell you about two of the times I've done this, both in the last month or so.

## None is None

One of the projects I've involved myself in is [Tuxemon](http://www.tuxemon.org), an open source monster-fighting rpg (a Pokemon clone) written in Python. My biggest contribution so far was a major rework of the Item subsystem. Initially the Item model had a list of strings to represent effects, each being simply the name of a method in the Item class that would be called via reflection (python's getattr function). There was no way to pass data to these functions and there was no way to restrict when the player could use the item other than by limiting which game states it could be used from (basically whether or not it could be used in combat).

I initially set out to simply create the "learn" function, so we could implement "TM" and "HM" items, which could give a monster a new technique. But then I thought "it wouldn't make sense for a fire monster to learn Ice Beam, I better just add conditional use too", and before you know it I was knee deep in a total subsystem refactor. Effects and Conditions would be represented by full classes, subclassed by specific implementations (LearnEffect, TypeCondition, etc.). This mimics the Event subsystem, so I had something to compare my work to.

After weeks of planning, debate, programming, and refactoring I was nearly finished. I was getting a single persistent bug though, my Item.user was always null (None in Python). I debugged it at the top and bottom (when the item was created and when the user was being referenced), which revealed that somewher in the stack Item.user was being lost. So I discovered every place an Item could be created and refactored them down and made sure all of them set the User correctly.

Still the error persisted.

So I followed all of the references, dropping debug lines all over the place. Every callback, every method, every time the user could have been manipulated or some parameter list might have been borked up, I added some debug printouts.

It all came down to this:

```python
class Item(object):
    def __init__(self, game, user, slug):

        logger.debug("user == None is {}", (user is None))
        logger.debug("game == None is {}", (game is None))

        self.game = game
        self.user = user
```

Here my debug lines told me everything was hunky dory, user and game existed and had valid values. What I didn't realize, for another **30 minutes**, was that that two lines below, still in in the init method, was this:

```python
        self.user = None
```

Some expletives may have been uttered.

I had left his behind from an earlier idea where the Item's user would be optional. I was so fixated I didn't even bother to look for any other references to the user member until I'd been at it for over 2 hours. I'm not an idiot and I'm not new to this. I _felt_ like an incompetent idiot right then, but 98% of the time I don't make mistakes like this, and I'm willing to be you're the same.

## Days of Present Future

My second story is actually about creating this blog. This site runs on Jekyll using a modified Hacker theme. I had allowed this site to languish, unpublished, in my git repo for 9 months before I finally asked a friend to proof-read my first post and About page. It took me another 4 months after that before I decided to finally pull the trigger. Unfortunately, the two posts stopped appearing on the site when I built it locally.

I knew there were some issues with the Hacker theme, so I looked into it. Turned out to be [a simple issue](https://github.com/pages-themes/hacker/issues/44) of not being compatible with latest major version of Jekyll, which had come out while I let this site gather digital dust. A quick update to the _config.yml fixed that. Except it didn't fix my site, my posts were still not showing up.

Next I went for old faithful, a simple google search. This turned up just a basic list of potential issues, which I was certain couldn't possibly be my problem. But I checked anyway. Lo and behold, my posts were dated for 20**21**-03-18.

![statue facepalming]({{site.url}\assets\images\facepalm.jpg "Facepalm")

All I had to do was fix those dates to the correct values and everything was fine. Well, I also had to include 'future: false' in the config because github-pages defaults to showing all posts regardless of date. For... reasons?

Anyway, you are not _uniquely_ stupid. Just remember; everyone's a little bit stupid, sometimes.
