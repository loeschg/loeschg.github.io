---
layout: post
title: Resources for Learning RxJava for Android
date: '2014-10-20 18:59:10 -0500'
categories:
- dev
tags:
- rxjava
- androiddev
- android
featured: true
comments: true
---

[RxJava](https://github.com/ReactiveX/RxJava/wiki/The-RxJava-Android-Module) is another library that's been cropping up all over the place in AndroidDev world. [Similar to my experience with Dagger](http://gregloesch.com/dev/2014/10/19/resources-for-learning-dagger-for-android.html), RxJava was (and still does feel like) a sizable mountain to climb. With the help of some posts, I feel like I've finally been able to overcome at least a good majority of the steep learning curve. I'll be the first to admit I still have a lot to learn.

<!--more-->

### JavaRx: Observable Stream Sequence Provider...whaaaat?

Cracking open RxJava without the right resources is really difficult because of the following: 

* RxJava more a programming paradigm than library (Functional Reactive Programming)
* RxJava doesn't really have a specific use case. It can solve a lot of problems in various ways.
* RxJava's naming conventions don't seem that natural to me and thus it's hard to read, especially when you don't have lambdas/closures. I'm looking at you Android... no Java 8. No, I don't have any better naming suggestions :D

It's also hard not to feel dumb and close the browser tab when you read this in the RxJava Intro section in the Wiki:

>RxJava is a Java VM implementation of Reactive Extensions: a library for composing asynchronous and event-based programs by using observable sequences.

Again, maybe you're smarter than me. 

### What worked for me

The first thing I recommend reading is a general intro to what RxJava is all about: Functional Reactive Programming. I'd recommend reading through [An intro to Functional Reactive Programming](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754), but don't get too bogged down in details. It can get dense quickly and thus discouraging. You just want to get a high level understanding of what FRP is about.

Next, **I'd highly recommend this 4 part series** by a guy named Dan Lew. RxJava finally clicked when I read these, and I'm grateful he took the time to write the posts! They're short, digestible, and have solid content. He also gives examples with and without closures/lambdas... a small but *very* helpful thing.

* [Part 1 - The Basics](http://blog.danlew.net/2014/09/15/grokking-rxjava-part-1/)
* [Part 2 - Operators](http://blog.danlew.net/2014/09/22/grokking-rxjava-part-2/)
* [Part 3 - Reactive with Benefits](http://blog.danlew.net/2014/09/30/grokking-rxjava-part-3/)
* [Part 4 - Reactive Android](http://blog.danlew.net/2014/10/08/grokking-rxjava-part-4/)

Next, I'd suggest reading an article that gives advice to devs just starting out with RxJava (in a mobile context). The tl;dr is "don't use it everywhere right away. Use it for something small like network requests."

* [RxJava for Android (and .NET)](http://blog.futurice.com/tech-pick-of-the-week-rx-for-net-and-rxjava-for-android)

[RxMarbles](http://rxmarbles.com/) is a great **interactive** site which helped/helps me visualize different aspects of RxJava (you can click and drag the marbles... I missed that the first time around :D). It's linked in the offical RxJava wiki, so you know it's good!

Lastly, the main documentation is always helpful :)

* [RxJava Wiki](https://github.com/ReactiveX/RxJava/wiki)

### Bonus: Retrofit + RxJava

For those of you using [Square's Retrofit library](http://square.github.io/retrofit/) for API calls - which I'd highly recommend if you're looking for a networking library - they actually already support returning Observables right out of the box with something like the following:

    @GET("/user/{id}/profile")
    Observable<Profile> getUserProfile(@Path("id") int id);

How about that!

### Final Thoughts

While I haven't played around much with RxJava, I'm excited to see the instances where this can be used to cleverly solve a problem. I'd love to hear the ways which you've found it to be useful in your development process if you're a regular RxJava user... though my guess is you're probably not if you're reading this post. 

Hope these resources help you in your journey to broadening your horizons and ultimately making you a better Android developer. As always, should you have any other suggested resources you think I should include, feel free to comment below!
