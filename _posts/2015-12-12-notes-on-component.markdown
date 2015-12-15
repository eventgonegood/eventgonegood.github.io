---
layout:     post
title:      "Notes on Clojure Component"
subtitle:   "So I don't forget"
date:       2015-12-12 12:00:00
author:     "Dru Sellers"
header-img: "img/post-bg-component.jpg"
published:  true
---

#What is Component?

[Component](https://github.com/stuartsierra/component) is a clojure library by [Stuart Sierra](http://stuartsierra.com/) that provides a way to construct a _system_ written in clojure.

#Breaking down the setup

Ok, so here is an example of an old application being built using the Component library. My goal here is to break it down so that I can remember just what the heck is going on. My C# brain tends to have a hard time when I come back to this given the lack of types.

{% highlight clojure linenos %}
(defn example-system [config-options]
  (let [{:keys [org ids app neo4j stripe http]} config-options]
    (component/system-map
     :neo4j (db/new-database neo4j)
     :comp (component/using      
            (endpoint-component r/login-endpoint)
            {:id :identities})
     :app (component/using
           (handler-component app)
           [:comp])
     :http (component/using
            (http/new-http http)
            {:ring-app :app})
     :payments (pay/new-payments stripe)
     :identities (component/using
                  (ids/new-identities ids)
                  {:id-db :neo4j})
     :organizations (component/using
                     (organizations/new-organizations org)
                     {:org-db :neo4j}))))
{% endhighlight %}

aob

{% highlight clojure linenos %}

(defn example-system [config-options]
  (let [{:keys [org ids app neo4j stripe http]} config-options]
  ;;..elided
  ))

{% endhighlight %}

Ok, so this is the beginning and what its doing is taking in the passed in argument `config-options` and using _destructuring_ to pick out each key and binding it to a variable for easy access.

{% highlight clojure linenos %}

:neo4j (db/new-database neo4j)

{% endhighlight %}

Neo4j is a low level component that stands on its own, we call the constructor function that we created and pass in its configuration data. *THERE IS NO MAGIC HERE, YET.* This is just calling a function which will return a record (a fancy hash) that has the configuration data all passed in.

{% highlight clojure linenos %}

:comp (component/using      
	(endpoint-component r/login-endpoint)
	{:id :identities})

{% endhighlight %}

Ok, this one is saying that my component `:comp` is build by calling `(endpoint-component r/login-endpoint)` and then its dependencies are bound via the:

{% highlight clojure linenos %}
:comp (component/using      
	;;.. elided
	{:id :identities})
{% endhighlight %}

So, the `using` function is going to look at the record and bind the instance named `:identities` in the system map to the key `id` in the component. Ok, I take that back - this one is "special" because I'm using `duct`s [`endpoint-component`](https://github.com/weavejester/duct/wiki/Components). So what this is doing is in the method that you get to build you endpoint

{% highlight clojure linenos %}
(defn login-endpoint [config]
  (context "/login" []
    (GET "/" []
      (io/resource "app/endpoint/login/form.html"))))
{% endhighlight %}

Here as said above you will find that in `config` there is a key named `id` that will have the values of `identity` from the system map.

##Update

I learned there is a much more sane way to organize this stuff, so using the same example above you can *MUCH* better express it as:

{% highlight clojure linenos %}

(defn example-system [config-options]
  (let [{:keys [org ids app neo4j stripe http]} config-options]
    (->
      (component/system-map
        :neo4j (db/new-database neo4j)
        :comp (endpoint-component r/login-endpoint)
        :app (handler-component app)
        :http (http/new-http http)
        :payments (pay/new-payments stripe)
        :identities (ids/new-identities ids)
        :organizations (organizations/new-organizations org))
      (component/system-using
       {:identities [:neo4j]
        :organizations [:neo4j]
        :http [:app]
        :app [:comp]
        :comp [:identities]}))))

{% endhighlight %}
