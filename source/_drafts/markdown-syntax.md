---
title: markdown-syntax
tags:
---

* [Links](#Links)
* [Reference Links](#Reference-Links)
* [Basic Text Formatting](#Basic-Text-Formatting)
* [Headers](#Headers)
* [Headers](#Headers)
* [Headers](#Headers)
* [Lists](#Lists)

## Links
~~~
[like this](http://someurl)
~~~

[like this](http://someurl)

~~~
[like this](http://someurl "this title shows up when you hover")
~~~

[like this](http://someurl "this title shows up when you hover")

~~~
You can also put the [link URL][1] below the current paragraph
like [this][2].

   [1]: http://url
   [2]: http://another.url "A funky title"
~~~

You can also put the [link URL][1] below the current paragraph
like [this][2].

   [1]: http://url
   [2]: http://another.url "A funky title"

## Reference Links
~~~
Or you can use a [shortcut][] reference, which links the text
"shortcut" to the link named "[shortcut]" on the next paragraph.

[shortcut]: http://goes/with/the/link/name/text
~~~

Or you can use a [shortcut][] reference, which links the text
"shortcut" to the link named "[shortcut]" on the next paragraph.

[shortcut]: http://goes/with/the/link/name/text

## Basic Text Formatting
~~~
*this is in italic*  and _so is this_

**this is in bold**  and __so is this__

***this is bold and italic***  and ___so is this___
~~~

*this is in italic*  and _so is this_

**this is in bold**  and __so is this__

***this is bold and italic***  and ___so is this___

~~~
<s>this is strike through text</s>
~~~

<s>this is strike through text</s>

~~~
A carriage return
makes a line break.

Two carriage returns make a new paragraph.
~~~

A carriage return
makes a line break.

Two carriage returns make a new paragraph.

## Blockquotes

~~~
> Use it if you're quoting a person, a song or whatever.

> You can use *italic* or lists inside them also.
And just like with other paragraphs,
all of these lines are still
part of the blockquote, even without the > character in front.

To end the blockquote, just put a blank line before the following
paragraph.
~~~

> Use it if you're quoting a person, a song or whatever.

> You can use *italic* or lists inside them also.
And just like with other paragraphs,
all of these lines are still
part of the blockquote, even without the > character in front.

To end the blockquote, just put a blank line before the following
paragraph.


## Preformatted Text
~~~
This line won't *have any markdown* formatting applied.
I can even write <b>HTML</b> and it will show up as text.
This is great for showing program source code, or HTML or even
Markdown. <b>this won't show up as HTML</b> but
exactly <i>as you see it in this text file</i>.

Within a paragraph, you can use backquotes to do the same thing.
`This won't be *italic* or **bold** at all.`
~~~

    This line won't *have any markdown* formatting applied.
    I can even write <b>HTML</b> and it will show up as text.
    This is great for showing program source code, or HTML or even
    Markdown. <b>this won't show up as HTML</b> but
    exactly <i>as you see it in this text file</i>.

Within a paragraph, you can use backquotes to do the same thing.
`This won't be *italic* or **bold** at all.`

## Lists
~~~
* an asterisk starts an unordered list
* and this is another item in the list
+ or you can also use the + character
- or the - character

To start an ordered list, write this:

1. this starts a list *with* numbers
+  this will show as number "2"
*  this will show as number "3."
9. any number, +, -, or * will keep the list going.
    * just indent by 4 spaces (or tab) to make a sub-list
        1. keep indenting for more sub lists
    * here i'm back to the second level
~~~

* an asterisk starts an unordered list
* and this is another item in the list
+ or you can also use the + character
- or the - character

To start an ordered list, write this:

1. this starts a list *with* numbers
    +  this will show as number "2"
    *  this will show as number "3."
9. any number, +, -, or * will keep the list going.
    * just indent by 4 spaces (or tab) to make a sub-list
        1. keep indenting for more sub lists
    * here i'm back to the second level

## Tables
~~~
  First Header  | Second Header
  ------------- | -------------
  Content Cell  | Content Cell
  Content Cell  | Content Cell
~~~

  First Header  | Second Header
  ------------- | -------------
  Content Cell  | Content Cell
  Content Cell  | Content Cell

~~~
 First Header   | Second Header
  -------------  | -------------
  *Content Cell* | Content Cell
  Content Cell   | Content Cell
~~~

 First Header   | Second Header
  -------------  | -------------
  *Content Cell* | Content Cell
  Content Cell   | Content Cell

## Headers
~~~
This is a huge header
==================

this is a smaller header
------------------
~~~

This is a huge header
==================

this is a smaller header
------------------

## Horizontal Rule
~~~
----------------
~~~

----------------

~~~
* * *
~~~

* * *

~~~
you will get a header
---
~~~

you will get a header
---

## Images
~~~
![alternate text](https://sourceforge.net/images/icon_linux.gif)
~~~

![alternate text](https://sourceforge.net/images/icon_linux.gif)

~~~
![tiny arrow](https://sourceforge.net/images/icon_linux.gif "tiny arrow")
~~~

![tiny arrow](https://sourceforge.net/images/icon_linux.gif "tiny arrow")

## Escapes and HTML
~~~
* this shows up in italics: *a happy day*
* this shows the asterisks: \*a happy day\*
~~~

* this shows up in italics: *a happy day*
* this shows the asterisks: \*a happy day\*

~~~
<b>this will be bold</b>
you should escape &lt;unknown&gt; tags
&copy; special entities work
&amp;copy; if you want to escape it
~~~

<b>this will be bold</b>
you should escape &lt;unknown&gt; tags
&copy; special entities work
&amp;copy; if you want to escape it

## More Headers
~~~
# this is a huge header #
## this is a smaller header ##
### this is even smaller ###
#### more small ####
##### even smaller #####
###### smallest still: `<h6>` header
~~~

# this is a huge header #
## this is a smaller header ##
### this is even smaller ###
#### more small ####
##### even smaller #####
###### smallest still: `<h6>` header

## Table of Contents
* [Chapter 1](#chapter-1)
* [Chapter 2](#chapter-2)
* [Chapter 3](#chapter-3)