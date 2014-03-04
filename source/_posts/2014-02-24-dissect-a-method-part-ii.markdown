---
layout: post
title: "Dissect A Method - Part II"
date: 2014-02-24 20:48:10 -0500
comments: true
categories: Code-Examination Programming
---
About a week ago I created the "Dissect A Method" post.  Near the end of that post
I felt like I left things hanging.  So I'm going to try and clear things up.  I ended that post mostly because I wasn't comfortable with what is going on in the line of code that looks like this:

```
decimal.gsub!(/\d{3}(?!$)/,'\0,')
```

I did a some research - a little dive into the API, http://apidock.com/ruby/String/gsub and a trip over to a cool RegEx site, http://rubular.com/

If you would like follow along, go to the rubular site and enter the following into the text box for 'Your regular expression:' 

```
\d{3}(?!$)
```

In the text box for 'Your test string:' we will try a few different values.  Go ahead and test a few numbers, one at a time, e.g. 45789  AND  1024  AND  54 AND  5251350

Observe your results in the 'Match result:' text box.  So the beginning of our regex is composed of \d{3} which says that we are looking for 3 digits - any number 0 thru 9.  The next part is what had me stuck.  I seemed to recall that the $ meant End of line, and we can confirm that by looking at the RegEx quick reference on the rubular site.  

But what heck is this ?! stuff?  These two characters mean Negative lookahead.  So we are going to the end of the line of the string and then looking <em>backwards</em> to get a group of three digits.

Ok, so what does that last part mean, the '\0,' ?  Taking a look on the apidock site, I found an explanation - Interpolating.  For example:

```
# replace /ll/ with itself
'hello'.gsub(/ll/, '\0') # returns 'hello'
'hello'.gsub(/ll/, "\0") # returns 'he\000o'
```

Putting the pieces together, if matched, we would grab some digits, and replace them with the same digits <em>plus</em> a comma, thus helping us to create a pretty integer.

Using a combination of the rubular site, and irb, try out the example for the number 1249580.  As we will see in the irb shortly, the first digit will be extracted as the leader.  So in rubular, just enter the number 249580.  Notice how in rubular the digits 249 are highlighted.  These numbers will have themselves replaced with 249, once we do the interpolating.

Ok, lets take this to the irb!

```
2.1.0 :001 > decimal = Integer(1249580).to_s
 => "1249580" 
2.1.0 :002 > leader = decimal.slice!(0, decimal.length % 3)
 => "1" 
2.1.0 :003 > decimal.gsub!(/\d{3}(?!$)/,'\0,')
 => "249,580" 
2.1.0 :004 > decimal = nil if decimal.empty?
 => nil 
2.1.0 :005 > leader  = nil if leader.empty?
 => nil 
2.1.0 :006 > 
2.1.0 :007 >   [leader,decimal].compact.join(",")
 => "1,249,580" 
2.1.0 :008 > 
```

I hope this follow up post has helped to illustrate how all the components of our original method of dissection work.  Going through the research and stepping through a few examples has helped me a lot.  