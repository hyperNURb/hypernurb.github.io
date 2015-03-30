---
layout: post
title: "JavaScript Mixins"
date: 2015-03-29 11:15:00
categories: javascript development
cover: '/assets/pictures/mixins-post.jpg'
comments: true
---

Let's start by defining what a mixin is. I would explain it as _a way to share set of data (functions, methods, variables…) with objects_.

How does that look like? Let's say we have two functions `Clark` and `Bruce` and that we want to implement two methods `sayName` and `secretIdentity` to their prototypes.

This is how our functions would look like:

{% highlight js %}
function Clark() {
    this.name = 'Clark Kent';
    this.alterego = 'Superman';
}
Clark.prototype.fly = function () {
    this.fly = true;
};

function Bruce() {
    this.name = 'Bruce Wayne';
    this.alterego = 'Batman';
}
Bruce.prototype.talk = function () {
    return "I'm Batman";
};
{% endhighlight %}

Most common ways of adding of adding the methods these functions are:

#### 1. Extending objects

Extending object doesn't really fit our description of mixins, since we are **extending** instead **mixing**, though idea is the same. At least that's the way I see it.

{% highlight js %}
var Superhero = {
    sayName: function () {
        return this.name;
    },
    secretIdentity: function () {
        return this.alterego;
    }
};

// Here we extend function prototypes with object using jQuery.extend
$.extend(Clark.prototype, Superhero);
$.extend(Bruce.prototype, Superhero);
{% endhighlight %}

Biggest downside to this approach is that you'll either need `extend` function available from some library like _Underscore_ or _jQuery_ or you would have to write it.


#### 2. Functional mixin

They don't require any additional library and they are faster compared to extending. Also I find this implementation frequently overlooked.

{% highlight js %}
function Superhero () {
    this.sayName = function () {
        return this.name;
    };
    this.secretIdentity = function () {
        return this.alterego;
    };
}

// Here we extend function prototypes with using mixin function
Superhero.call(Clark.prototype);
Superhero.call(Bruce.prototype);
{% endhighlight %}


Which approach do you prefer?
