---
layout:     post
title:      "Notes on Duct Configuration"
subtitle:   "So I don't forget"
date:       2015-12-20 12:00:00
author:     "Dru Sellers"
header-img: "img/post-bg-duct.jpg"
published:  true
---

I'm a big fan of the Clojure library
[Component](https://github.com/stuartsierra/component). The design behind it
has structured my thinking in more than just Clojure but has impacted my OO
programming in C# as well. But since I am still a very new Clojure developer I
keep looking for prior art to study when building out my application. I can't
remember how but I stumbled on [duct](https://github.com/weavejester/duct) a
project by the prolific Weavejester. Duct is a project to bootstrap a web
application for Clojure and is admittedly an experiment.

That said, I have really enjoyed that it implements for me the
[reloaded](http://thinkrelevance.com/blog/2013/06/04/clojure-workflow-reloaded)
developed by Stuart Sierra. I had to dig into the source several times to 
really understand what all `duct` was doing for me but in doing so I have,
of course, learned a little bit more about the ecosystem that Clojure exits
in.

Thanks to the power of Leiningen templaces getting started was super easy.

`lein new duct <your app name>`

When i fire up just a basic duct template for an app named `bob` I get the following:

{% highlight text %}
.
├── README.md
├── dev
│   ├── local.clj
│   └── user.clj
├── profiles.clj
├── project.clj
├── src
│   └── bob
│       ├── component
│       ├── config.clj
│       ├── endpoint
│       ├── main.clj
│       └── system.clj
└── test
    └── bob

{% endhighlight %}

Ok, so now the exploration begins as I try to wrap my head around how
I can best leverage this organizational structure. For the most part it doesn't
get in the way at all, but I do find myself struggling with how to structure
my configuration data.

> Duct seperates configuration and environment.

Amen, duct indeed does seperate things out as we can see below:

#### dev/local.clj

I'll be honest, I'm not sure what goes in here.

#### config.clj

Contains `bob.config/defaults` (with `meta-merge` `displace` metadata) as well
as containing `bob.config/environ` to pull in all keys that we are going to
source from the environment. Sweet, I like this a lot.

So, config provides two static points of configuration data, my defaults
and my environment settings. This makes inspecting them very easy. Sweet.

#### profile.clj

This is a file where you can store various profile releated data that will
get merged in thanks to `environ` and `lein`. I'm not using this feature
at all yet, but I can see the value of pulling out profile specific data
into one spot.

It should be noted that this file in *not* checked into source control.

#### project.clj

Finally we have the values in the project itself. Specifically is the
`:project/dev :env :port 3000` setting. For those that don't know (I didn't)
it appears that the key `:project` is used by `lein` to self modify the
project given a specific profile. In this case its saying that when we
have the dev profile active (such as in a REPL) we are going to set the
project key `:env` to `{:port 3000}` which `lein-env` in turn uses to set
the environment variable PORT to 3000. This is not my most favorite feature
as it expands the number of places I have to look for my configuration data, 
so I have removed this bit of configuration myself.

#### dev/user.clj 

Contains the `user/dev-config` which allows us to diffentiate between REPL 
configuration and `lein run` configuration. I disagree with the names but
I don't have better ones either. 

Here we see the first application of merging our configuration layers. In
this file we see:

{% highlight clojure %}

(def config (meta-merge config/defaults ;^:displace meta data
                        config/environ
                        dev-config))

{% endhighlight %}

I really like this approach, it builds out a series of layers in the config
data and then merges them all together. We will see this again in the
`main.clj`

However since `user.clj` is checked into source, I wonder if it wouldn't
be better named `team.clj` or some such. ...?

#### main.clj

Contains the `bob.main/prod-config` which is supposed to contain the production
configuration. It also contains the `bob.main/config` which is a copy of the
`user/config` above but instead of merging in `dev-config` brings in
`prod-config`. 

---

Now the final / next piece is to think about how I'm going to layer all of 
this configuration data because `meta-merge` does its merge on the top most key
so I can't replace the key `:port` in the hash-map `:http` I have to supply
everything.

Right now I'm thinking that my major configuration data is pretty simple.

{% highlight clojure %}

{
  ;I full expect the below to come from ENV var
  :http {:port 5000 :host "localhost"}

  :database {:classname "org.postgresql.Driver" ;should not change
             :subprotocol "postgresql"          ;should not change
             :subname "/localhost:5432/db-name" ;from ENV
             :user "db-user"                    ;from ENV
            }
}

{% endhighlight %}

So, if I have just this simple data and I try to merge it in with an 
environ that looks like:

{% highlight clojure %}

(def environ {
  :http {:port (some-> env :port Integer.)
         :host (some-> env :host)}
  :database {:subname (some-> env :subname)
             :user (some->env :db-username)}
})
{% endhighlight %}

If I don't supply the values in the ENV var, then meta-merge will clobber
my defaults with `nil` values. This honestly makes me feel like I'm missing
something. I'll continue to play with it and once I figure it out I'll post
an update there.
