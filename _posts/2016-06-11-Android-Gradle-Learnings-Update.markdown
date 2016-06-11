---
layout: post
title: 'Android Gradle Learnings (Update): Hooking into Android Tasks'
date: '2016-06-11 16:50:00 -0400'
categories:
- dev
tags:
- gradle
- androiddev
- android
featured: true
comments: true
---

In my previous post about my [Android Gradle Learnings]({% post_url 2015-08-19-Android-Gradle-Learnings %}), I mentioned some ways to create Gradle tasks which are dependent on other dynamically created Android build tasks (such as `lint` and some build/flavor variants such as `debug` or `release`). Since writing that, I've discovered an arguably easier way to accomplish this.

<!--more-->

### Some Context
Let me set up the situation.

Let's say I have a project with 2 Android modules, *:app* and *:lib*. The module *:lib* is a typical Android module. In *:app*, I have *flavor1* and *flavor2* build flavors.

**:app's build.gradle file**

    android {
      // lots omitted
      productFlavors {
        flavor1 { /* stuff ommitted */ }
        flavor2 { /* stuff ommitted */ }
      }
    }


As a result of this, when I run `gradle tasks` I see `connectedDebugAndroidTest` task (from *:lib*) and both `connectedflavor1DebugAndroidTest` and `connectedflavor2DebugAndroidTest` tasks (from *:app*). The same goes for other checks such as `unittest` and `lint`.

My goal was to run all of these tasks with a single gradle command *except for* the `flavor2` tasks (long story... irrelevant as to why). If I was just wanting to run all of them, this would be a simpler task.

### Creating a task dependent on generated tasks

One option is to do the following:

    task allTheChecks(type: GradleBuild) {
        tasks = [
                'connectedflavor1DebugAndroidTest', // starting here, handle flavor1
                'testflavor1DebugUnitTest',
                'lintflavor1Debug',
                'connectedDebugAndroidTest', // starting here, handles all :lib
                'testDebugUnitTest',
                'lintDebug'
        ]
    }

This does the trick, but you could argue it's not the best way since it's not transparent to the user what tasks are involved. If you run the task `gradle allTheChecks -m`  (this does a "dry run" so to speak of the gradle task), it doesn’t output all of the tasks that this `allTheChecks` task contains. It's viewed as a standalone task. This isn't super transparent.

After some asking around, it turns out there's a "Lazy dependsOn" feature of Gradle (see [Example 14.7 in Build Script Basics](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)). While a bit more verbose, it's more transparent to the consumer what's happening.

    task allTheChecks(dependsOn: [
            ':app:connectedflavor1DebugAndroidTest'
            ':app:testflavor1DebugUnitTest',
            ':app:lintflavor1Debug',
            ':flavor1:connectedDebugAndroidTest',
            ':flavor1:testDebugUnitTest',
            ':flavor1:lintDebug',
    ]) {
        description 'Convenience command to run all debug/flavor1debug checks (excluding :app's flavor2)'
    }

Doing a dry run now spits out the desired results:

    > ./gradlew allTheChecks -m

    :app:connectedflavor1DebugAndroidTest SKIPPED
    :app:testflavor1DebugUnitTest SKIPPED
    :app:lintflavor1Debug SKIPPED
    :flavor1:connectedDebugAndroidTest SKIPPED
    :flavor1:testDebugUnitTest SKIPPED
    :flavor1:lintDebug SKIPPED

    BUILD SUCCESSFUL

The downside is needing to explicitly prefix everything with the module name. If anyone knows a way around this, please let me know!

Until next time...
