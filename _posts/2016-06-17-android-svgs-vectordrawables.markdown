---
layout: post
title: 'Should I be using SVGs and VectorDrawables in Android?'
date: '2016-06-18 13:15:00 -0400'
categories:
- dev
tags:
- vectordrawables
- androiddev
- android
featured: true
comments: true
---

Dealing with drawables and different screen densities is one of the more tedious parts of Android Development. These last few weeks I've been especially aware of this at HipChat, as I've been doing daily "dp" dances with one of our designers. While running Android's lint tool, some image resource inconsistencies surfaced (missing density versions, inconsistent dp measurements) on some of our older resources, so we've been needing to re-cut lots of assets. It's horrible. And it doesn't need to be that way.

<!--more-->

While mentioning this to a co-worker, he brought up VectorDrawables. This is a summary of our conversation:

> Me: "Ugh, managing image assets in Android is terrible"
>
> Co-worker: "Why don't you use VectorDrawables & SVGs?"
>
> Me: "Does it *actually* work for older APIs / minSDK 15? If not, it's dead to me."
>
> Co-worker: \*shrug\* \*wink\* Need to run!

*Note: My co-worker was a bit more helpful than this, but it makes the story better :)*

After some quick searching and asking on the Twittersphere, it seemed VectorDrawables had come a lot further than I realized. Another typical example of being left behind in software development.

While probably a bit late to the game, here's what I wish I knew a month ago.

### You can really use VectorDrawables back to API 7 (Android 2.1)

and AnimatedVectorDrawables back to API 11. I knew there was an announcement about VectorDrawables with the Support Library v23.2.0 release, but I thought it was fraught with gotchas. Turns out there are some, but I was mostly wrong.

If you're using the Support Library, AppCompat, and the Android Gradle plugin (2.+), you're pretty much there, though yes, you may need to take the time to update the aforementioned things.

Add the following to your app's build.gradle:

    android {
      defaultConfig {
        vectorDrawables.useSupportLibrary = true
      }
    }

and you're good to go. (If still using the Gradle 1.5 plugin, take a look at Chris Banes' [AppCompat - Age of the vectors](https://medium.com/@chrisbanes/appcompat-v23-2-age-of-the-vectors-91cbafa87c88) for more details.)

### Ok, now what? Do I just drop an SVG in my /drawable folder?

It's not that easy, but Android Studio makes it pretty simple. It turns out, all<sup>1</sup> the [Material Icons](https://design.google.com/icons/) are available in Android Studio. Who knew? You did? Well you should have told me.

> ...all the Material Icons are available in Android Studio

Yep! You no longer have to use use [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/), do the back and forth with your designer, rename a bunch of misnamed pngs, or manually drag them into folders if they weren't organized perfectly (why *are* Android naming conventions so strict?). I think my designer co-worker would have hugged me had they been in the office when this was discovered.

### Show me the money!

The easiest way to get a single SVG into your project is by doing the following:

**EDIT**: *while the following is probably simplest, I ran into issues directly converting a custom (non-Google) SVG in Android Studio (e.g. all black drawable). I suggest using the [svg2android](http://inloop.github.io/svg2android/) online tool.*

Right-click your drawable folder -> New -> Vector Asset to bring up **Vector Asset Studio**. The wizard is pretty straightforward. You can select one of the Material Icons (and custom size it) or use your own SVG.

*Note: I'm using Android Studio 2.1.2. Also, I believe some SVG conversion to VectorDrawable features are not yet fully supported... somebody correct me in the comments if I'm wrong.*

![Example of adding a VectorDrawable in Android Studio](https://i.imgur.com/kNMHSM3.gif)

After you've created the VectorDrawable, you can set it as the src of any ImageView using the `app:srcCompat` attribute from AppCompat.

    <ImageView  
      android:layout_width="wrap_content"  
      android:layout_height="wrap_content"  
      app:srcCompat="@drawable/ic_pin_drop_black_24dp"
      />  

<s>I believe it's recommended to use the AppCompat versions where you can (e.g. [AppCompatImageView](https://developer.android.com/reference/android/support/v7/widget/AppCompatImageView.html)).</s>**EDIT**: It's come to my attention that AppCompatImageView is automatically used "in place" of ImageView should you include AppCompat as a dependency and inflate it through normal means... normal meaning not a subclass or creating without the inflater - e.g. `new ImageView()`. Should you have any custom ImageView subclasses, swap out ImageView for AppCompatImageView, and you should be all set - e.g. `public class AvatarView extends AppCompatImageView`.

### How about TextView Drawables and the like?

While I haven't done a deep exploration myself, I've read you're not able to use VectorDrawables anywhere you would normally use a drawable (feel free to comment with specific examples).

If you want to do this **programatically**, you can set it with `setImageResource()`

The other option when none of the above works is to wrap your VectorDrawable in a `layer-list` or `selector` like so:

    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_pin_drop_black_24dp"/>
    </selector>

and treat it like you would any other drawable/selector (source: [stackoverflow](http://stackoverflow.com/a/35800335/413254)).

**EDIT:** One thing to note with this technique. You'll likely need to add  `AppCompatDelegate.setCompatVectorFromResourcesEnabled(true);` in your custom Application class (or anywhere before the selector or layer-list is set on the view). Otherwise you may end up with something like the following:

```
android.view.InflateException: Binary XML file line #47: Error inflating class ImageView
  at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:704)
  ...
Caused by: android.content.res.Resources$NotFoundException: File res/drawable/some_drawable.xml from drawable resource ID #0x7f02005a
  at android.content.res.Resources.loadDrawable(Resources.java:1918)
  ...
Caused by: android.content.res.Resources$NotFoundException: File res/drawable/referenced_drawable.xml from drawable resource ID #0x7f02005b
  at android.content.res.Resources.loadDrawable(Resources.java:1918)
  ...
Caused by: org.xmlpull.v1.XmlPullParserException: Binary XML file line #1: invalid drawable tag vector
  at android.graphics.drawable.Drawable.createFromXmlInner(Drawable.java:877)
  ...
```

Note: this is required for 23.4.0 or above (was working in 23.2.0 then pulled in 23.3.0 then stuck behind this flag in 23.4.0 -- see Chris Bane's post linked at the bottom for more details)

### I have lots of SVGs... this sounds tedious

Should you have a bunch of SVGs, you can do a bulk conversion using the [svg2android](http://inloop.github.io/svg2android/) online tool.

If you prefer to have a bit more automated process, you may want to check out the victor Gradle plugin (https://github.com/trello/victor) by Dan Lew and the folks over at Trello. From my understanding, this is an early release, though I'm sure they'd love the feedback and help squashing any bugs!

### Go shed some APK weight and make your designer happier!

With this technique you can now replace bulky image files with lightweight VectorDrawables and make your designer happier in the process!

Feel free to drop any hints or corrections in the comments.

Until next time...

#### **More Resources**
* Chris Banes on "[AppCompat - age of the vectors](https://medium.com/@chrisbanes/appcompat-v23-2-age-of-the-vectors-91cbafa87c88#.7vzl3076t)"

* DevByte [Android Vector Graphics](https://www.youtube.com/watch?v=wlFVIIstKmA)

* [Android Support Library 23.2 release video (30s mark)](https://www.youtube.com/watch?v=7E2lNBM38IE&t=30s)

* AppCompat

\**hattip\** to [Brian Griffey](https://twitter.com/briangriffey), [Matt Wear](https://twitter.com/parallelcross), and [Brian Parent](https://twitter.com/brian_parent) for the help and pointers.

---
<sup>1</sup> Turns out some of the newest icons are not yet available in Vector Asset Studio such as "delete forever" and "euro symbol"
