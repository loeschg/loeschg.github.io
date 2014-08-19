---
layout: post
status: publish
published: true
title: Read One Character at a Time - Java Programming
author:
  display_name: Greg Loesch
  login: greg
  email: loesch.greg@gmail.com
  url: http://gregloesch.com
author_login: greg
author_email: loesch.greg@gmail.com
author_url: http://gregloesch.com
excerpt: "<div class=\"right\">\r\n<script type=\"text/javascript\"><!--\r\ngoogle_ad_client
  = \"ca-pub-8434327951333786\";\r\n/* GL-Medium Rect - Lead in */\r\ngoogle_ad_slot
  = \"1463896563\";\r\ngoogle_ad_width = 300;\r\ngoogle_ad_height = 250;\r\n//-->\r\n</script>\r\n<script
  type=\"text/javascript\"\r\nsrc=\"http://pagead2.googlesyndication.com/pagead/show_ads.js\">\r\n</script>\r\n</div>I
  was working on a parser for a programming project a few weeks ago for school in
  which reading an input file one character at a time proved to be the easiest route
  to take. While I found a few different resources online that provided some guidance,
  but I ran into a somewhat unexpected problem that was difficult to debug regarding
  detecting the end of stream (EOS)/end of file. This solution is not earth shattering
  (pretty intuitive actually), but I would have liked to have read something similar
  to this beforehand. It would have saved me a bit of time."
wordpress_id: 596
wordpress_url: http://greg-loesch.com/blog/?p=596
date: '2010-05-01 15:50:24 -0400'
date_gmt: '2010-05-01 19:50:24 -0400'
categories:
- life
- tech
- school
tags:
- java
- casting
- FileReader
- char
- programming
- Eclipse
- character
comments: []
---
<div class="right">
<script type="text/javascript"><!--<br />
google_ad_client = "ca-pub-8434327951333786";<br />
/* GL-Medium Rect - Lead in */<br />
google_ad_slot = "1463896563";<br />
google_ad_width = 300;<br />
google_ad_height = 250;<br />
//--><br />
</script><br />
<script type="text/javascript"<br />
src="http://pagead2.googlesyndication.com/pagead/show_ads.js"><br />
</script>
</div>
<p>I was working on a parser for a programming project a few weeks ago for school in which reading an input file one character at a time proved to be the easiest route to take. While I found a few different resources online that provided some guidance, but I ran into a somewhat unexpected problem that was difficult to debug regarding detecting the end of stream (EOS)/end of file. This solution is not earth shattering (pretty intuitive actually), but I would have liked to have read something similar to this beforehand. It would have saved me a bit of time.<a id="more"></a><a id="more-596"></a></p>
<p>To clarify, my goal was to take in and parse/analyze an input text file one character at a time (file specified via a command line argument). The simplest way I found in Java to read a character at a time was through a FileReader. There may be a faster/more efficient input method, but simplicity worked fine for the particular project. The following is the rough syntax of how to read from a file (saving the current character each time as a char) and loop through until the end of the file.</p>
<pre>char currentChar;  //This will store the current character

//Create file reader
FileReader inputStream = new FileReader("file path as a string");

while (currentChar != (char)-1)  // This is key!
{
  currentChar = (char)inputStream.read(); // Cast to a char

  /* Now perform task! */
}</pre>
<p>The issue I ran into had to do with the way the end of stream was recognized. When reading from a FileReader, the character integer value is returned, so a cast has to be done. The issue is that the EOF is signaled by the integer -1 and not an actual character.</p>
<p>At first, I just tried to compare the character to the integer value -1... basically just hoping for the best. I (stupidly) hoped -1 would be considered a character.</p>
<pre>while (currentChar != -1)</pre>
<p>This led to an infinite loop, as an actual character (recall I casted directly on the read) is never equal to a -1 integer value. Upon tracing in the Eclipse debugger, the cast to -1 returned an unrecognizable character... not -1.</p>
<p>I then edited my code so that I didn’t cast the input right away. This way I could easily check for the -1 int value. Therefore, currentChar was actually an integer value. Anytime I wanted to perform some sort of operation using this read character, I had to cast it. This lead to A LOT of casts (I was reading it in as a character for a reason) and just made for cluttered code.</p>
<p>As you can see above, I finally realized that I could simply cast (-1) to a character and check for equality that way in the while loop condition. This way I could cast immediately to a character when reading in and not worry about it from that point on.</p>
<p>Hope it helps!</p>
