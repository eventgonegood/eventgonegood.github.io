---
layout:     post
title:      "First, a solid platform."
subtitle:   "No sense in lifting on pillows"
date:       2015-06-05 12:00:00
author:     "Dru Sellers"
header-img: "img/post-bg-foundation.jpg"
---

#Platforms for support

So this is mostly my own hang up but I need to have a solid idea of how I am going to build this thing. One of my biggest questions I go back and forth on is should I build this application on [Heroku](http://heroku.com) (I really do love them) or do I build the application on [AWS](http://aws.amazon.com)? I like them both quite a bit, but granted I like them for different reasons. So, instead of just sprinting ahead and picking one I am forcing myself to take a few minutes to sit down and reason out why I should choose one over the other.

###AWS

What I like about Amazon is it gives me bare metal (yes, its a VM but roll with me) access. If I want to take one server and slap PostgreSQL, Neo4j, and 3 applications on it - I can. It will cost me some money (as much as I love the [calculator](http://calculator.s3.amazonaws.com/index.html) I still have no idea how much I would spend) but I'll have full control. Full control does mean more mental overhead. I have to watch the server. I'd have to learn up on monitoring tools, which I don't know yet and Heroku will give me.

###Heroku

Ok, so I don't have full control, but I get so much more application level monitoring and hosting support, that as a solo entrepreneur it seems [silly to under value](https://baremetrics.com/blog/build-it-buy-it) how much less ops time I will have to spend. Choosing Heroku lets me "focus on the app" - which at this early stage is not a bad way to think. I could have neo4j up and running on a free instance today as well as a postgres instance (which means I also get all of the cool Heroku PG Goodness).

Given that I am still a solo entrepreneur on this adventure, and I am not even sure how I will make money with this idea, it seem prudent to not waste time learning [Ansible](http://www.ansible.com/) and friends but to instead just ship it and go with Heroku. 

Ok, well. That was easy. :)
