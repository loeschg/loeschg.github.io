---
layout: post
title: 'Android Gradle Learnings: Hooking into Android Tasks'
date: '2015-08-19 19:00:00 -0400'
categories:
- dev
tags:
- gradle
- androiddev
- android
featured: true
comments: true
---

As Android developers, a good majority of us are pretty content with having basic understanding ("basic understanding" may even be a stretch) of [Gradle](#link), the build system we rely on every day to do our jobs. Having recently explored writing a custom task, I thought I'd share a couple things I learned along the way.

<!--more-->

> <i class="fa fa-info-circle"></i> - While this post will share some arguably elementary Gradle concepts, it does assume you have some grasp of the basics. This is not an in-depth/comprehensive intro to Gradle :)

#### Some Context
In the Android-Gradle plugin we're provided a task called `lint`. This, as you'd expect, runs lint/static analysis checks on our code and generates reports (that remind us of how many things we've swept under the rug while we were "too busy shipping features").

My goal was to add on to this `lint` task and take action on the generated lint report - located at `ProjectRoot/app/build/outputs/lint-results.html` - (using [git-ratchet](https://github.com/iangrunert/git-ratchet) to "ratchet down" lint warnings if you must know).

## Adding on to an already existing Android build task
Initially I crafted a script which was explicitly executed as a part of our CI process. After (strugging through) writing the bash script, it dawned on me that it'd make more sense to make this a gradle task because... well... it's a part of our build process.

#### This should be simple... right?

I'd trudged my way through a few Gradle tutorials, so I figured this would be pretty straightforward.

Attempt 1:

    def executePostLint() {
      println 'execute post lint task'
    }

    lint << {
      executePostLint
    }

Running this resulted in an error. Gradle didn't recognize `lint`.

    > ./gradlew lint

    * What went wrong:
    A problem occurred evaluating project ':app'.
    > Could not find property 'lint' on project ':app'.


This got more confusing when I tried to add something to a different build task, `assembleDebug`, and found the same syntax worked.

    def executePostDebugAssemble() {
      println 'Do some stuff'
    }

    assembleDebug << {
      executePostDebugAssemble
    }

The task `assembleDebug` runs and my code is executed as hoped.

    (... other tasks ommitted for brevity)
    :app:assembleDebug
    Do some stuff

(╯°□°）╯︵ ┻━┻

This confused me. Why could I directly reference `assembleDebug` and not `lint`? Both showed up when I ran `gradle tasks`.

While not entirely sure of the specifics of *why* this is necessary, turns out the lint task isn't available until after the evaluation/configuration step of gradle. (See the Gradle docs - [Build Lifecycle Events](https://docs.gradle.org/current/userguide/build_lifecycle.html#build_lifecycle_events) - for more info)

In order to hook into any dynamically generated tasks, such as `lint`, you need to use the `afterEvaluate` method:

    afterEvaluate { project ->
      project.tasks.lint << {
          executePostLint()
      }
    }


Doing this gives us the result we want:

    > ./gradlew lint

    Wrote HTML report to file:/Users/greg/AndroidStudioProjects/GradleProject/app/build/outputs/lint-results.html
    Wrote XML report to /Users/greg/AndroidStudioProjects/GradleProject/app/build/outputs/lint-results.xml
    execute post lint task

Turns out any specific variant/build flavor related task (think `debug` and `release` for each build flavor) must also be referenced in the same manner.

    // WORKS
    assembleFlavor1 << {
      println 'adding step to the end of task!'
    }

    // DOESN'T WORK... (note Debug)
    assembleFlavor1Debug << {
      println 'adding step to the end of task!'
    }

    // CORRECT
    afterEvaluate { project ->
      project.tasks.assembleFlavor1Debug << {
        println 'adding step to the end of task!'
      }
    }

## Making a new task dependent on an existing Android Gradle Task

Now let's say you want to create a **new task** that's dependent on an existing build task (opposed to adding new functionality to an **existing** Android task).

For example, if you wanted to create a task that automatically uploaded a specific build flavor apk somewhere, you would obviously need to assemble your build flavor/variant first with something like `assembleFlavor1`.

While you could "add" onto the existing `assemble` task (like we did in the earlier example), this is probably not the best decision, as you don't want this upload to happen every time you assemble the flavor. A better choice would be to create a separate task, say `uploadFlavor1Apk` that is dependent on the `assembleFlavor1` task.

This is pretty straightforward. Create the task and declare a dependency on `assembleFlavor1`:

    task iDependOnAssembleFlavor1(dependsOn: assembleFlavor1) << {
      println 'I need assembleFlavor1 to run before me.'
    }

Doing a dry run of the `iDependOnAssembleFlavor1` task with the `-m` flag gives us the expected results. The task `assembleFlavor1` is run before `iDependOnAssembleFlavor1`:

    > ./gradlew iDependOnAssembleFlavor1 -m

    ... other tasks ommitted for brevity
    :app:assembleFlavor1 SKIPPED
    :app:iDependentOnAssembleFlavor1 SKIPPED

> <i class="fa fa-info-circle"></i> Doing "dry runs" using the `-m` flag while developing new build tasks will save you a lot of time, especially if you're dependent on time consuming build tasks.

Ok, now let's iterate on this task. Let's say we're actually only uploading the **release** version of Flavor 1. This means we only need to assemble the release version of Flavor1 (`assembleFlavor1` will assemble both debug and release versions... wasted CPU time!).

A naive (but very reasonable) attempt would be the following:

    task iDependOnAssembleFlavor1Release(dependsOn: assembleFlavor1Release) << {
        println 'I need assembleFlavor1Release to run before me.'
    }

Similar to our earlier `lint` exercise, this results in an error:

    FAILURE: Build failed with an exception.

    * What went wrong:
    A problem occurred evaluating project ':app'.
    > Could not find property 'assembleFlavor1Release' on project ':app'.

Gradle can't find this task because it's dynamically generated (after evaluation).

This means you need to use the `whenTaskAdded` method:

    tasks.whenTaskAdded { task ->
      if (task.name == 'assembleFlavor1Release') {
        iDependOnAssembleFlavor1Release.dependsOn(task)
      }
    }

The `whenTaskAdded` method accepts a closure which is run when a task is added. This allows you to grab the dynamically generated task and add the dependency.

All together now our build script looks like the following:

    task iDependOnAssembleFlavor1Release << {
        println 'I need assembleFlavor1Release to run before me.'
    }

    tasks.whenTaskAdded { task ->
        if (task.name == 'assembleFlavor1Release') {
            iDependOnAssembleFlavor1Release.dependsOn(task)
        }
    }

Running it gives us the desired result:

    > ./gradlew iDependOnAssembleFlavor1Release -m

    ... other tasks ommitted for brevity
    :app:assembleFlavor1Release SKIPPED
    :app:iDependOnAssembleFlavor1Release SKIPPED

    BUILD SUCCESSFUL

## Passing Properties via the command line

Another really helpful thing to know is how to dynamically pass properties to your task with `-P` (project properties) and `-D` (system property of JVM).

For example, consider the following task which just outputs the value of `prop1`.

    task taskWithProperties << {
        println prop1
    }

You can execute this task, passing in the `prop1` property with the following:

    > ./gradlew taskWithProperties -Pprop1="i was passed via the command line"

    :app:taskWithProperties
    i was passed via the command line

    BUILD SUCCESSFUL

Having an understanding of these few things will hopefully allow you to tweak your build process to better suit your needs. Remember: you have full access to groovy with the gradle build system! Now go forth and make your build process better :)

Until next time...
