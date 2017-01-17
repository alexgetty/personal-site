---
layout: post
section: journal
title:  How to Sass up your Colors
date:   2017-01-17
categories: front-end, sass
permalink: /tutorial/how-to-sass-up-your-colors
redirect_from:
  - /how-to-sass-up-your-colors
---

{% highlight scss %}

// Color Definition
$colors: (
  primary: (
    base: #F9CB2F
  ),
  neutral: (
    lightest: #FFFFFF,
    light: #999999,
    base: #666666,
    dark: #333333,
    darkest: #000000
  )
);

// Getter Functions
@function getColor($palette: primary, $shade: base) {
  @return map-get(map-get($colors, $palette), $shade);
}

@function getColor-rgba($alpha, $palette: primary, $shade: base) {
  @return rgba(map-get(map-get($colors, $palette), $shade), $alpha);
}
{% endhighlight %}

```html
<html>
</html>
```

```ruby
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
```
