---
layout: post
section: tutorial
title:  How to Sass up your Colors
date:   2017-01-17
categories: front-end, sass
permalink: /tutorial/how-to-sass-up-your-colors
redirect_from:
  - /how-to-sass-up-your-colors
---

Managing colors is an important part of every project. It makes sense to invest a little time to set ourselves and our teams up with an easy way to define and maintain colors which will save us time and energy later. Using a combination of Sass Maps and functions, we can set ourselves up for any color scheme we might encounter.

Check out the code below, then keep reading for more about how and why I’ve chosen this approach. If you are familiar with Sass, you probably understand what’s going on. Maybe you’re already using something similar. If you think you have something better, [hit me up](mailto:hello@alexgetty.co) and tell me about it!

## The Code
<script src="https://gist.github.com/TheDataDesigner/c739b8a982b7e6517ccc63f114f72c3f.js?ts=2"></script>

## The Breakdown

### Using a sass map to define our colors

Maps were introduced in March 2014 with [Version 3.3.0](https://github.com/sass/sass/blob/master/doc-src/SASS_CHANGELOG.md#330-7-march-2014) of Sass. They enable you to organize variables into named groups and access them dynamically, which is a perfect way to manage those 78 grays that we designers love to keep slipping into our designs. This concept should be familiar to you if you’ve worked with pretty much any programming language before.

In it’s simplest form, a Sass map can be used like this:

{% highlight sass linenos %}
// test
$colors: (
	primary: #F9CB2F,
	secondary: #756E7D
)

a {
	color: map-get($colors, primary);
}
{% endhighlight %}

This just uses a single-layered sass map to define some colors, then the built-in function `map-get` to find the value for that key in the map. There are a bunch of other built-in functions that allow you to do some pretty cool stuff with Sass maps, and maybe I’ll write about some of those later on, but right now we really only need to know `map-get`.

### Hiding all the ugly stuff

Using `map-get` directly in our styles doesn’t really make things easier because we still have to remember the color map and key names and it’s actually more verbose than just defining all your color variables the old way, `$color-primary`, so let’s pull that part out and make write a function to hide some of that ugly stuff:

{% highlight sass linenos %}
@function color($key) {
  @return map-get($colors, $key);
}
{% endhighlight %}

Back in our styles, we can use our shiny new function to get our color. As you can see, it’s much cleaner than before:

{% highlight sass linenos %}
a {
	color: color(primary);
}
{% endhighlight %}

This still isn’t much better than writing out `$color-primary`, though. The real value in this approach becomes clear when you’re working with a lot more colors and variations of those colors.

### A Map Inside a Map

Chances are, you’re trying to wrangle a lot more than a few colors like our example above, so it’s time to step things up and make this thing actually useful.

The great thing about Sass maps is that we can nest them to create additional layers of organization. Instead of defining a key in a map with a value, you just another map.

Let’s pull down the map from the original code sample and see what we have to do to our function to get this working.

{% highlight sass linenos %}
$colors: (
  primary: (
	  light: #67A1E9,
	  base: #428CE8,
    dark: #3478CD
  ),
  status: (
    success: #50E39A,
    inactive: #B7BCC5,
    warning: #F8C71C,
    error: #FF388E,
    disabled: #B7BCC5
  ),
  neutral: (
    lightest: #FFFFFF,
    lighter: #F8FAFE,
    light: #E7EAF1,
    base: #B7BCC5,
	  dark: #494C50,
    darker: #1E2124,
    darkest: #000000
  )
);
{% endhighlight %}

Now that we have all our colors defined in all their glorious mapception, it’s time to modify our `color` function this. Here’s the function from the original example and a breakdown of how it works:

{% highlight sass linenos %}
@function color($palette, $shade: base) {
  @return map-get(map-get($colors, $palette), $shade);
}
{% endhighlight %}

Our color function now accepts two parameters. The first, `$palette` is referring to the first level of the `$color` map. The second defines the shade within that palette.

We want the shade the default to `base` so that when we’re using that color, we don’t need to specify it every time. This is done by adding the default value to the parameter in the function declaration shown above.

We’re still using the `map-get` function, but this time we’re nesting two of them inside each other in order to traverse both layers of our color map.

The inside one, `map-get($colors, $palette)`, looks for our color map, then selects the palette within the map based on the value of the first parameter in our function. That entire palette map is returned by the inside `map-get`, which becomes the target map for the outside `map-get`, which then selects the shade within that palette.

Incase you need to use transparency in your designs, you can throw another version of the getter function in there as well:

{% highlight sass linenos %}
@function color-rgba($alpha, $palette, $shade: base) {
  @return rgba(map-get(map-get($colors, $palette), $shade), $alpha);
}
{% endhighlight %}

This does the same thing as our `color()` function, passes in an addition $alpha parameter and wraps our returned value in a `rgba()` function which converts the hex value into an reba value.

So there you have it, you can now add your own palettes and shades to your color map and call them wherever you need it!

{% highlight html linenos %}
h1 {
	color: color(neutral, darker);
}

button {
	background-color: getColor(primary);
}

.card {
	border: 1px solid getColor(neutral);
}

.error {
	color: color(status, error);
}
{% endhighlight %}


### Wrapping it up
As with everything in the web design word, this is a work in progress. I have no doubt new technologies or techniques will emerge that put this to shame. If you think you have a better way of doing it, [let me know](mailto:hello@alexgetty.co) and I'll throw a link to your technique at the end of the article here.
