---
layout:     post
title:      "Its all about that money"
subtitle:   "My exploration of the stripe api"
date:       2015-06-14 12:00:00
author:     "Dru Sellers"
header-img: "img/post-bg-stripemodel.jpg"
published:  true
---

#So what does Stripe look like?

So, I have a basic model where I can track "identities", identities consist of a username and password ([bcrypted](http://rundis.github.io/blog/2015/buddy_auth_part1.html)) pair.

~~~
var user = {
  :username "drusellers"
  :password "bcrypt+sha512$32da7e602406d818c6768194$12$243261243132246c32674a576d514c75585255434f64444f4c59414b653843357a4e645547397279616c304a696f525656393166434862347a2e564b"
}
~~~

If we ignore for now how we are storing this model, lets figure out what the account model would look like. So I start reading through the [api docs](https://stripe.com/docs) and of course I'm a bit overwhelmed at first. But I know that this is just the first step in a long journey, so lets buckle down and get this done. ([a good overview](http://www.larryullman.com/series/processing-payments-with-stripe/) of the things)

#How do I charge a card a one time fee?

I'm planning to use [clj-strip](https://github.com/abengoa/clj-stripe) to do the actual work. So step one lets capture the card and link into to our identity. Oh, hmm, you capture the card using stripe.js and get a token back. Also, it looks like I need some plans. Ok, lets start with getting some plans created.

~~~
(defn register-plans [payments]
  (common/with-token (:secret-key payments)
    (common/execute (plans/create-plan "plan1" (common/money-quantity 500 "usd") (plans/monthly) "Starter"))
    (common/execute (plans/create-plan "plan2" (common/money-quantity 1000 "usd") (plans/monthly) "Professional"))))
~~~

Ok, now that I have some plans, lets make a customer for my application.

~~~
(common/with-token "<secret key>"
    (common/execute 
    	(customers/create-customer 
    		(customers/email "dru@drusellers.com"))))
~~~

Ok, now I get a customer object back and I need to track the :id of the stripe customer. So now my next step is what data do I want to keep from stripe? Let's keep the last4, type, expiration data so that we can easily draw this stuff later.

Let's create a new object that represents an 'organization', lets attach the stipe customer id to that. and then off of the organization lets hang the payment data.

~~~
organization
- stripe-customer-id
- [payment-ids]
~~~

~~~
(store-card payments org last4 type exp-month exp-year)
; {:name "org name"}
; {:last4 "1234" :type "Visa" :exp-month 12 :exp-year 2019}
~~~