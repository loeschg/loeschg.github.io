---
layout: post
title: Android Gradle Learnings: Hooking into Android Tasks
date: '2015-08-08 19:00:00 -0400'
categories:
- dev
tags:
- gradle
- androiddev
- android
featured: true
comments: true
---

As Android developers, a good majority of us are pretty content with having basic understanding ("basic understanding" may even be a stretch) of [Gradle](#link), the build system we rely on every day. Having recently explored writing a custom task, I thought I'd share a couple quick lessons I learned along the way.

<!--more-->

<!-- TODO: Gradle basics... quick reference -->
> **Note:** While this post will share some arguably elementary Gradle concepts, it does assume you have some grasp. This is not an in-depth or even a comprehensive intro to Gradle. See my "Quick Gradle Reference" post to hopefully fill in the holes.

#### Some Context
In Android-Gradle plugin there's a task given to us called `lint`. This, as you'd expect, runs lint checks on our code and generates reports (that remind us of how many things we've swept under the rug while we were... um... "too busy shipping features").

My goal was to take certain actions with the generated lint report.

### Adding on to an already existing Android gradle task
Initially I crafted a simple bash script that was to be explicitly executed (as a part of our CI process). After doing this, it dawned on me that it'd probably make more sense to make this a gradle task because... well... it's more or less a build task.

#### This should be simple... right?

I'd done a little clicking through Gradle tutorials, so I figured this would be pretty straightforward.

Attempt 1:

    def executePostLint() {
      println 'execute post lint task'
    }

    // BROKEN!! (╯°□°）╯︵ ┻━┻
    lint << {
      executePostLint
    }

Running this results in an error. Gradle doesn't recognize `lint`.

```
* What went wrong:
A problem occurred evaluating project ':app'.
> Could not find property 'lint' on project ':app'.
```

This gets more confusing when you try to add something to the `assembleDebug` task and find that this same syntax works just fine:

    def executePostDebugAssemble() {
      println 'Do some stuff'
    }

    assembleDebug << {
      executePostDebugAssemble
    }

`assembleDebug` is run just fine, and our code is executed.

    (... other tasks ommitted for brevity)
    :app:assembleDebug
    Do some stuff

What? Why can I reference `assembleDebug` and not `lint`? Both show up when I run `gradle tasks`.

While not entirely sure of the specifics of why this is necessary, the lint task isn't available until after the evaluation/configuration step of gradle. (See https://docs.gradle.org/current/userguide/build_lifecycle.html#N1665A)

In order to hook into the `lint` task you need to do the following:

    afterEvaluate { project ->
      project.tasks.lint << {
          executePostLint()
      }
    }

Doing this gives us the result we want:

    Wrote HTML report to file:/Users/greg/AndroidStudioProjects/GradleProject/app/build/outputs/lint-results.html
    Wrote XML report to /Users/greg/AndroidStudioProjects/GradleProject/app/build/outputs/lint-results.xml
    execute post lint task

Turn out any variant/build flavor related task must also be referenced in the same manner.

    // WRONG
    assembleFlavor1Debug << {
      println 'adding step to the end of task!'
    }

    // CORRECT
    afterEvaluate { project ->
      project.tasks.assembleFlavor1Debug << {
        println 'adding step to the end of task!'
      }
    }
