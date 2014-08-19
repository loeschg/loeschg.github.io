---
layout: post
status: publish
published: true
title: Convert Column Number to Corresponding Excel Column Letters - Java
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
date: '2011-02-21 17:39:06 -0500'
date_gmt: '2011-02-21 22:39:06 -0500'
categories:
- tech
- programming
tags:
- java
- Excel
- columns
- rows
- numbers
- letters
- characters
- convert numbers to letters
comments: []
---
<p>Needed this method for a simple conversion between numbers and columns for an Excel spreadsheet manipulation in Java. Some example runs and results:</p>
<p>getEquivColumn(0) = A</p>
<p>getEquivColumn(1) = B</p>
<p>getEquivColumn(26) = AA</p>
<p>getEquivColumn(52) = BA</p>
<pre class="java">public String getEquivColumn(int number){
        String converted = "";
        // Repeatedly divide the number by 26 and convert the
        // remainder into the appropriate letter.
        while (number &gt;= 0)
        {
            int remainder = number % 26;
            converted = (char)(remainder + 'A') + converted;
            number = (number / 26) - 1;
        }

        return converted;
    }</pre>
<p>Cheers!</p>
