---
layout: post
title: "Parsing Date in JavaScript"
date: 2014-02-10 18:30:00
categories: javascript development cross-browser
cover: '/assets/pictures/time-post.jpg'
comments: true
---

Recently I worked on something that should be a simple and straightforward task. Timestamp was sent as string from backend and it should converted it to _Date_ object in fronted. Format of the string looked like this - `2015-02-08 18:00:00`. No biggie, right?

I've checked [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) and saw that Date accepts `dateString` as a argument. Without digging further I've tried in _Chrome_ console.

{% highlight js %}
new Date('2015-02-08 18:00:00')
Sun Feb 08 2015 18:00:00 GMT+0100 (CET)
{% endhighlight %}

It worked, task was finished and sent for testing. Soon after I got it back because of the issues in _Safari_ and _Internet Explorer_. _Safari_ console reported `Invalid Date`.
First of all, let me make a digression here. I ended up fixing date format in backend, it only made sense to do so. Solving it in JavaScript was for fun.

Now, let's go back to the basics. I've reread the docs (this time for real) noticed that **T** letter was missing in date format. Standardized IETF format would be `2015-02-08T18:00:00`. So first solution could be something simple as using replace `'2015-02-08 18:00:00'.replace(' ', 'T')`.

Another solution I had in mind was to split the string into an array and pass it as argument to `Date` constructor. So simple `split` and `apply` should do the trick.

{% highlight js %}
var timestamp = new Date.apply(null, '2015-02-08 18:00:00'.split(/[\-\s\:]/));
Uncaught TypeError: function apply() { [native code] } is not a constructor
{% endhighlight %}


Ok, it's not going to be **that** easy. After a while, I figured out I that can use `bind` as a wrapper around constructor and pass in sliced arguments to the `Date` context constructor. That turned out something like this:

{% highlight js %}
var timestamp = new (_.bind.apply(null, [Date, null].concat('2015-02-08 18:00:00'.split(/[\-\s\:]/))));
Sun Mar 08 2015 18:00:00 GMT+0100 (CET)
{% endhighlight %}

Yay success, kinda! **Only** problem with this solution is month offseted by one, because in JavaScript months start from zero. Also I guess any sane person would spend a fair amount of time figuring this "voodoo" out. So it's definitely not something I'd like to see in production. At this point I've stopped exploring this problem and fixed the backend.

Let's clarify what this line actually does:

* I used [Underscore's bind](http://underscorejs.org/#bind) because of few reasons firstly – it was already loaded in the project and secondly – old browsers don't support native `bind`.
* [Split](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) accepts `RegEx` as a separator. So `.split(/[\-\s\:]/)` splits string on dash, space and colon. In our case we get `['2015', '02', '08', '18', '00', '00']`
* `[Date, null].concat…` this part is interesting. Bind accepts two or more parametars. First one is our `Date` constructor, second one is context which is in our case `null`. Rest of the arguments are passed into first function, our constructor.

It turns out that brilliant Axel Rauschmayer blogged about this back in 2011 - [Spreading arrays into arguments in JavaScript](http://www.2ality.com/2011/08/spreading.html).

I remembered what a friend of mine would say:
> Dude, don't be stupid, just use a library! [Moment.js](http://momentjs.com/) is awesome.

And I think he is probably right. But then again I feel like it's such overkill to include a 12kb library for such simple task.




