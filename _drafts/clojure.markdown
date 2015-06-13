---
layout:     post
title:      "Why Clojure?"
subtitle:   "Isn't that a huge papercut?"
date:       2015-06-14 12:00:00
author:     "Dru Sellers"
header-img: "img/post-bg-clojure.jpg"
published:  true
---

#So, why didn't you use .Net?

I wanted to get off of the windows platform, and even with all of the recent work by the amazing people at Microsoft I just wanted to get outside of my comfort zone. So a new language paradigm (functional), a new platform (*nix), and a new language (Clojure)


Refactoring is usually just a matter of moving a method from one file to another for a move command. Since there are no containin 'classes'.

##Moving from bound methods to functions

bob.DoStuff() is the same as do-stuff(bob)

#structuring apps

https://www.booleanknot.com/blog/2015/05/22/structuring-clojure-web-apps.html

##Things that I have had to think through (aka papercuts)

- How do I structure Clojure code? Because its different from C# for sure.

##Minor Things

Some workflow: http://thinkrelevance.com/blog/2013/06/04/clojure-workflow-reloaded

###lein check

Gives at least SOME kind of compile time check which I'm used to having.

###cljfmt

One thing that I love about .Net is that I can use ReSharper to format my code with a single key press. I can just encode my standards in the R# file and ship it with the solution. This removes a mental burden from me by allowing me to keep my code consistent.

###liberator

I frigging love this library. There is also a similar one for .Net written in F# that looks pretty sweet.