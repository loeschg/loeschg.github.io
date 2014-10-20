---
title: Convert Column Number to Corresponding Excel Column Letters - Java
date: '2011-02-21 17:39:06 -0500'
layout: post
comments: true
categories:
- dev 
tags:
- java
- Excel
- columns
- rows
- numbers
- letters
- characters
- convert numbers to letters
---

I needed this method for a simple conversion between numbers and columns for an Excel spreadsheet manipulation in Java. Some example runs and results:

    getEquivColumn(0) = A
    getEquivColumn(1) = B
    getEquivColumn(26) = AA
    getEquivColumn(52) = BA

<!--more-->

{% highlight java %}
    public String getEquivColumn(int number){
        String converted = "";
        // Repeatedly divide the number by 26 and convert the
        // remainder into the appropriate letter.
        while (number >= 0)
        {
            int remainder = number % 26;
            converted = (char)(remainder + 'A') + converted;
            number = (number / 26) - 1;
        }

        return converted;
    }
{% endhighlight %}

Cheers!


<!--
status: publish
published: true
author:
  display_name: Greg Loesch
  login: greg
  email: loesch.greg@gmail.com
  url: http://gregloesch.com
author_login: greg
author_email: loesch.greg@gmail.com
author_url: http://gregloesch.com
wordpress_id: 663
wordpress_url: http://gregloesch.com/blog/?p=663
date_gmt: '2011-02-21 22:39:06 -0500'
comments: []
-->