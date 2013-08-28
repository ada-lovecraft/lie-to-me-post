---
layout: post
title: "Organizing Angular"
description: ""
category: development
tags: [angular, js, ui, javascript]
---
{% include JB/setup %}
I'm currently working on a large enterprise level project that has a -massive- ui. After much discussion, we decided to break ground working with Angular (which came with it's own set of challenges). I guess I should mention that we're using [Brunch](http://brunch.io) with [Brunch on Asteroids](https://github.com/exlee/brunch-on-asteroids) to bootstrap the site. It's been a bit rough getting up to speed using this framework and deciding how to organize everything. After some debate, we decided to use the following organization for the app.

{% highlight bash %}
- app
	- assets
		- img
	- partials
		- jade-includes
			* mixins.jade
		* layout.jade
		* foo.jade
	- scripts
		- controllers
			* FooController.coffee
		- directives
			* FooDirective.coffee
		- filters
			* FooFilter.coffee
		- repositories
			* FooRepository.coffee
		- services
			* FooService.coffee
	- styles
		* app.less
- vendor
	- js
		* angular.min.js
		* jquery.min.js
	- styles
		- bootstrap
			* bootstrap.less
{% endhighlight %}

There is, of course, more to it than that, but that's our basic skeleton.

---

## Partials:
Partials are used by angular to be the "view" in MVC. We're using [Jade](http://jade-lang.org) because it's more expressive and extensible than straight HTML.

For example, with jade, we can write:
{% highlight jade %}
.panel.panel-default
	.panel-heading
		.panel-title
			h3.icon-text
				.icon.glyphicon.glyphicon-calendar
				.text Calendar
	.panel-body
		input(type="date")
{% endhighlight %}

and it will compile down to
{% highlight html %}
<div class="panel panel-default">
  <div class="panel-heading">
    <div class="panel-title">
      <h3 class="icon-text">
        <div class="icon glyphicon glyphicon-calendar"></div>
        <div class="text">Calendar</div>
      </h3>
    </div>
  </div>
  <div class="panel-body">
    <input type="date"/>
  </div>
</div>
{% endhighlight %}

Jade also has the ability to create mixin functions that you can reuse over and over

---

## Services vs. Repositories
This is one of the biggest discussions we had while creating the framework for the site. We decided (and correctly so) that repositories are the persistence layer, and services are the middleware that exist between the controllers and the repositories that transform the raw data that the repositories return. Our repositories mimic the api perfectly.

For example:

We have an api call to:

	/foo/list/:id

In the FooRepository.coffee file, we have an angular service (not to be misconstrued with the -idea- of services) that has a method that looks like the following:

{% highlight coffeescript %}
list: (id) ->
	$http.get('foo/list/' + id)
{% endhighlight %}



In the FooService.coffee file, we have an angular service that has a method that looks like the following

{% highlight coffeescript %}
get: (id) ->
	deferred = $q.defer()
	$FooRepository.list(id).then (response) ->
		fooObject = response.foo
		deferred.resolve fooObject
	deferred.promise
{% endhighlight %}

Our service transforms the data that is returned from the repository into an object that the controller expects.

Once we got this framework in place, our velocity ramped up a -huge- amount.