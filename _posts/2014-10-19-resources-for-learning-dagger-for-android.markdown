---
layout: post
title: Resources for Learning Dagger for Android
date: '2014-10-19 21:10:10 -0500'
categories:
- dev
tags:
- dagger
- androiddev
- android
featured: true
comments: true
---

[Dagger](http://square.github.io/dagger/) is all the rage right now in the Android Development world, and it's for good reason. Dagger is a good, lightweight dependency injection tool which will likely help you better architect your app. Like many other valuable technologies, **the learning curve can be a little steep** - especially if you've never used a dependency injection tool before - but I think you'll find it's well worth your time to learn it. 

While I'm still definitely in the process of grokking the library/paradigm, I figured it could be valuable to put together a list of articles, slides, and presentations that helped get me over the hump. Let's all feel smarter together! \o/

<!--more-->

### My Challenges with Dagger

I made multiple attempts at understanding Dagger, many half-hearted, and - as much as my pride doesn't want to admit it - some full-hearted attempts! I just couldn't get the engine started.

Dagger's primary example on Square's site is about a coffee maker, and I honestly had trouble even understanding the high level example. I'm not entirely sure, but I think I only began to understand the example after I learned the basics of Dagger... a little backward, I know. I mean... having the terms "thermosiphon", "Object Graph", and "Directed Acyclic Graph" thrown around in an introduction example isn't exactly like a welcoming hug. Might be an intelligence issue :)

### Dagger resources: what worked for me

Jake Wharton gave a fantastic presentation at Devoxx 2013 which provides a good quick overview of what dependency injection is, why it's helpful, and most importantly for me, **how Dagger can be used in an Android application.** Dependency injection has been around for awhile, but it's helpful to see how it can be used within an Application, Activity, and/or Fragment lifecycle. It's a little lengthy but definitely worth the watch. I learned a thing or two about general app architecture, too.

* [Architecting Android Applications with Dagger - Jake Wharton - Devoxx 2013](https://www.parleys.com/play/529bde2ce4b0e619540cc3ae/chapter0/about)
* [Speaker deck](https://speakerdeck.com/jakewharton/2014)

Antonio Leiva also has a pretty quick and digestible 3-part blog series on Dependency Injection with Android. It also can be a good quick intro to the Model View Presenter (MVP) pattern, which seems to be gaining some traction in the Android world.  

1. [Part 1 - What's Dependency Injection and what's Dagger?](http://antonioleiva.com/dependency-injection-android-dagger-part-1/)
2. [Part 2 - Intro to Dagger](http://antonioleiva.com/dagger-android-part-2/)
3. [Part 3 - Digging a little deeper with object graphs](http://antonioleiva.com/dagger-3/)

### Bonus: MVP resources

The Model View Presenter (MVP) pattern seems to fit Android a bit better than the typical Model View Controller paradigm. I'm giving it a go on a current project, and while I think there's a bit more class creation per view, it's helped me with my unit testing. 

* [Android MVP - Antonio Leiva](http://antonioleiva.com/mvp-android/)
* [Architecting Android... the clean way?](http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/)


### But... Dagger 2.0 is coming!

As some of you are aware, the Dagger 2.0 release is supposedly just around the corner. While you're probably tempted to put off the learning Dagger until this release happens, I'd encourage you to dive in right away! Grokking when and why to use dependency injection is a bigger hurdle than Dagger syntax IMO. Dagger 2.0 is also supposed to have a pretty straightforward migration process.

### In Summary

If you're like me, not only will you experience the general benefits of having better decoupled/modular code when using Dagger for Android, but you'll find yourself thinking and architecting your applications in a cleaner way, too. 

Hopefully these articles are as helpful as they were to me. Should you have any other suggestions, feel free to comment below! 

