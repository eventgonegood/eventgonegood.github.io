---
layout:     post
title:      "Clojure Project Structures"
subtitle:   "Lessons learned from the trenches"
date:       2015-12-26 12:00:00
author:     "Dru Sellers"
header-img: "img/post-bg-project.jpg"
---

As I continue to play with Clojure and ClojureScript my application is starting
to grow to a decent size. As it grows my usual desire for organization
starts to kick in, and I figured it was time to take a step back and look
at my mess of a project and see if I can't come up with something a bit
more sane then throwing everything in one directory.

Ok, so the first big thing that I have learned is that, unsurprisingly, cljc
code cannot see/use clj code. However, cljc code can see clj code. But when
cljc code tries to load a cljc file that reads in clj code you get an analysis
error saying that it couldnt' find the clj file. Ok, so this is good.

I've also been reading that having a seperate directory structure for the
cljc/clj/cljs code is unneeded. That you can put everything in one directory.
So I tired it, and it looks pretty nice, you really just need to organize your
namespaces the way you want which is all I need any way. Much nicer than having
a bunch of duplicate directory tree structures.

{% highlight text %}
~/
  src/
    egg/
      clients/
        leaderboard.cljs
        score-entry.cljs
      server/
        http.clj
  project.clj
{% endhighlight %}

##Multiple Javascript Applications

The application I'm building has a few different single page applications.
While trying to understand how to best build that out in ClojureScript land, I
stumbled on a
[post](https://groups.google.com/forum/m/#!topic/clojure/dj0T7alrT5A) that
gives a nice hint on how this might be acheived.

{% highlight clojure %}
:cljsbuild {
    :builds
        [{:id "leaderboard"
          :source-path "src/"
          :compiler {:output-to "resources/public/cljs/leaderboard.js"
                     :output-dir "resources/public/js/compiled/leaderboard.js"
                     :optimizations :advanced
                     :pretty-print true}}
         {:id "score-entry"
          :source-path "src/"
          :compiler {:output-to "resources/public/cljs/score-entry.js"
                     :output-dir "resources/public/js/compiled/score-entry.js"
                     :optimizations :advanced
                     :pretty-print true}}]}
{% endhighlight %}

And now I can build both with `lean cljsbuild once leaderboard score-entry`
which I then just alias in lein to `lein build-apps`. I'm still playing around
with how to best expose this to figwheel and friends but this feels like a
solid start. My guess is that `lein figwheel leaderboard` should work just
fine.


So, with that I have a nice clean way to start putting the application
together.  My scoring functions are pure data manipulations and can be written
in cljc which means I can use the same scoring algorhythms on both the server
and client side

My next big puzzle piece is how to I best work with Hiccup and Sablono to 
manage the various view parts of my application.
