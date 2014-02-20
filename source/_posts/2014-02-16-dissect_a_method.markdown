---
layout: post
title: "Dissect A Method"
date: 2014-02-16 22:20:25 -0500
comments: true
categories: Code-Examination Programming
---
I recently purchased a copy of "Confident Ruby" by Avdi Grimm.  I have not gotten
far in the book yet, but I am enjoying it.  In the chapter titled "Use built-in
conversion functions" I came across a block of code that made me pause.  Being a
new recruit of the Ruby Army, I only have a grasp on the basics.  The method that
I have below probably makes sense to an experienced Rubyist, and other programming
veterans, but to me it didn't.  I wanted to know exactly what each line of code
meant, and what was going on.

```
def pretty_int(value)
  decimal = Integer(value).to_s
  leader  = decimal.slice!(0, decimal.length % 3)
  decimal.gsub!(/\d{3}(?!$)/,'\0,')
  decimal = nil if decimal.empty?
  leader  = nil if leader.empty?
  [leader,decimal].compact.join(",")
end
```

First we are setting a variable, decimal, equal to the result of passing whatever we submitted for 'value' to the conversion function, Integer, which then gets converted to a string via the to_s method.

We can confirm that decimal is a string if we add a little code after that first line above:

```
  decimal = Integer(value).to_s
  puts "Class of decimal: #{decimal.class}"
```

Simulated:
```
2.1.0 :001 > decimal = Integer(400).to_s
 => "400" 
2.1.0 :002 > decimal.class
 => String 
2.1.0 :003 > 
```

Looking back at our original six lines of code, the first one is understandable.  The next two, the ones with slice! and gsub! I'm not so sure about.  The only thing I know for sure is that when we have a method with the bang character ! that means we are changing the value in place, it modifies the receiver.  Let's jump over these two lines of code for the moment.

The next two lines, the ones with the if condition, I understand.  What they are saying is that if decimal is empty (true), then set decimal equal to nil.  The same goes for leader, if leader is empty, set it to nil.  Methods that have a question mark ? should return a boolean - either true or false.

```
  decimal = nil if decimal.empty?
  leader  = nil if leader.empty?
```  

The last line of code in this method is bringing our data together, formatting the number to use a comma after the value of leader if we have a value in decimal as well.

Ok, so we nailed down the first line of the method and the last three.  Now what is going on in lines two and three?

```
  #...
  leader  = decimal.slice!(0, decimal.length % 3)
  decimal.gsub!(/\d{3}(?!$)/,'\0,')
```

It looks like we are trying to set a value of something to the leader variable by performing some actions on the decimal variable.  In the above code we are passing a starting position of 0 to the slice! method and an ending postion of decimal.length % 3.  Let's try this out using irb:

```
2.1.0 :011 > decimal = Integer(6234).to_s
 => "6234" 
2.1.0 :012 > decimal.length % 3
 => 1 
2.1.0 :013 > decimal.slice!(0,1)
 => "6" 
2.1.0 :014 > 
```
From the example above, leader would be set to a value of "6".  Let's try another example using a smaller number:

```
2.1.0 :016 > decimal = Integer(49).to_s
 => "49" 
2.1.0 :017 > decimal.length % 3
 => 2 
2.1.0 :018 > decimal.slice!(0,2)
 => "49" 
2.1.0 :019 > 
```

What the heck, let's throw one more example, this time using a bigger number:

```
2.1.0 :019 > decimal = Integer(1032578).to_s
 => "1032578" 
2.1.0 :020 > decimal.length % 3
 => 1 
2.1.0 :021 > decimal.slice!(0,1)
 => "1" 
2.1.0 :022 > 
```

Seems like this is making sense.  Lets put one more example up, this time tying things together so we can see both decimal and leader:

```
2.1.0 :001 > decimal = Integer(4794).to_s
 => "4794" 
2.1.0 :002 > decimal.length
 => 4 
2.1.0 :003 > decimal.length % 3
 => 1 
2.1.0 :004 > leader = decimal.slice!(0,1)
 => "4" 
2.1.0 :005 > decimal
 => "794" 
2.1.0 :006 > 
```

Ah!  So we now have leader set to "4" and decimal has been sliced down to "794".  Recall that the method name is pretty_int and the purpose of this method is to make numbers look pretty.  In other words, we want our numbers to have commas where appropriate.  So in the example of submitting 4794, we want our method to return a value of 4,794.  If our submitted number is smaller, we don't need the comma, and if the number is larger, say 1032578, we would like it to be formatted as:  1,032,578.  

We are almost there - just need to figure out how that other line of code works.  We can have a safe assumption that gsub will do a substitution.  But exactly how is this code suppose to work.  Lets look at it again:

```
  decimal.gsub!(/\d{3}(?!$)/,'\0,')
```

Let's drop back into irb and see if we can make some sense of this:

```
2.1.0 :001 > decimal = Integer(4793).to_s
 => "4793" 
2.1.0 :002 > leader = decimal.slice!(0, decimal.length % 3)
 => "4" 
2.1.0 :003 > decimal.gsub!(/\d{3}(?!$)/,'\0,')
 => nil 
2.1.0 :004 > decimal
 => "793" 
2.1.0 :005 > leader
 => "4" 
2.1.0 :006 > [leader,decimal].compact.join(",")
 => "4,793" 
2.1.0 :007 > 
```

I have a basic understanding of what is going on, but I'm still not very comfortable with Regular Expressions, which is what we are passing to the slice! method above.  How about another example?

```
2.1.0 :007 > decimal = Integer(1249580).to_s
 => "1249580" 
2.1.0 :008 > leader = decimal.slice!(0, decimal.length % 3)
 => "1" 
2.1.0 :009 > decimal.gsub!(/\d{3}(?!$)/,'\0,')
 => "249,580" 
2.1.0 :010 > [leader,decimal].compact.join(",")
 => "1,249,580" 
2.1.0 :011 > 
```

Think I'm almost there with my understanding.  I'm going to take a look at one more example using irb:

```
2.1.0 :011 > decimal = Integer(56752).to_s
 => "56752" 
2.1.0 :012 > leader = decimal.slice!(0, decimal.length % 3)
 => "56" 
2.1.0 :013 > decimal.gsub!(/\d{3}(?!$)/,'\0,')
 => nil 
2.1.0 :014 > decimal
 => "752" 
2.1.0 :015 > leader
 => "56" 
2.1.0 :016 > [leader,decimal].compact.join(",")
 => "56,752" 
2.1.0 :017 > 
```

Once the slice! operation has completed, we have part of our number stored in leader and the remainder in decimal.  For every group of more than 3 numbers in decimal, we will do a substitution and insert a comma.  I know what is happening, but I'm still having a hard time properly describing what is going on, and how it knows where to place the commas.  I'll need to bone up on RegEx!

