# if-ie.styl [![Build Status][build]][build-link] [![NPM version][version]][version-link]
[build]: https://travis-ci.org/kizu/if-ie.styl.png?branch=master
[build-link]: https://travis-ci.org/kizu/if-ie.styl
[version]: https://badge.fury.io/js/if-ie.styl.png
[version-link]: http://badge.fury.io/js/if-ie.styl


## The [Stylus](http://learnboost.github.com/stylus) way to divide code for IE and for others

`if-ie.styl` is not a library — but a methodology of splitting the styles for IE and for other browsers. The profits of it are the reduced number of requests — one for each browser, the reduced size of each stylesheet — each browser would get only what it needs, and the faster and easier way to write hacks for  ie. More on the features below.

This is the addition to the great CSS Preprocessor [Stylus](http://learnboost.github.com/stylus).

## Table of Contents

1. [Install](#install)
2. [Usage](#usage)
3. [Features](#features)
    - [`ie` variable](#ie-variable)
    - [`zoom`](#zoom)
    - [`inline-block`](#inline-block)
    - [Redefining mixins](#redefining-mixins)
    - [`rgba-ie()`](#rgba-ie)
    - [clip support](#clip-support)
    - [simple nth-child support](#simple-nth-child-support)
    - [`{lte_ie9}` selector](#lte_ie9-selector)

## Install

1. Clone `if-ie.styl` somewhere to your project

   ``` sh
git clone git://github.com/kizu/if-ie.styl.git
```

2. Include it in your `.styl` project:

    ``` CSS
@import "if-ie.styl/if-ie"
```

    Note, that if you want to use [nib](https://github.com/visionmedia/nib/) or any other similar library, you should import `if-ie.styl` *after* them, so for a project with nib it would look that way:

    ``` CSS
@import "nib"
@import "if-ie.styl/if-ie"
```

3. Use it!

## Usage

Well, the basics of this lib are simple — you can use the `ie` variable in a way, where you could write hacks for it in a main stylesheet and then get two compiled versions — one for all modern browsers and other — only for ie.

That goes that way:

1. You create a master stylesheet, like `style.styl`, where you'd import the `if-ie.styl`, any other libs or files and where you'd write your main css.

2. Then you create the `style_ie.styl` near the main stylesheet with such content:

    ``` CSS
ie = true
@import "if-ie.styl/if-ie"
@import "style.styl"
```

    Note that `ie = true` must be declared _before_ imports, so [Redefining mixins](#redefining-mixins) would work.

3. Compile those `.styl` files to `style.css` and `style_ie.css`.

4. You include your styles like this in HTML's head:

``` HTML
<meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7,IE=edge" />

<!--[if gt IE 7]><!-->
  <link rel="stylesheet" href="style.css" />
<!--<![endif]--><!--[if lt IE 8]>
  <link rel="stylesheet" href="style_ie.css" />
<![endif]-->
```

There are two things to mention: 

- The main thing: there are conditional comments that tells each browser to get only one stylesheet. So, basically, we get only one request per browser, even for IE, yay!
- I use the meta to convert IE8 to the IE7 mode: IE8 is not ideal and it's much easier to have only one version if IE in testing. Also, IE7 renders CSS strangely faster than IE8. And there are a lot of other things too.


## Features

### `ie` variable

You can use the `ie` variable to filter some content, so you could easily say to ie or to all other browsers which properties or blocks of rules to use.

It goes easy as this:

``` Stylus
.foo
  box-shadow: 0 0 3px #000
  border: 1px solid #000 if ie
```

You can see the `if ie` part — it tells Stylus to use this rule only if there were `ie` variable set. This variable is `false` by default, but if you'd use the method described above, this variable would be true in `style_ie.styl`, so you'd get those styles only in IE.

And as this is just regular Stylus' variable, you can use negation with it to write down styles only for modern browsers, so, you can do this:

``` Stylus
if !ie
  @media (min-width: 500px)
    // Some responsive stuff goes here
```

and you won't get all those styles in the stylesheet for IE! Some bytes saved.

### `zoom`

`if-ie.styl` redefined the `zoom` property, so it would show up only in IE. Basic stuff, but now you won't get it for your normal browsers, so you could use it straight away in a regular strylesheet when you need to fix something with it!

### `inline-block`

Since IE supports `display: inline-block` only for initially inline elements, `if-ie.styl` provides a transparent mixin to make it work for every element.

So, when you would write `display: inline-block`, IE would get `display: inline; zoom: 1;` everytime. You could say that we won't need it for initially inline blocks, but hey! Look at this:

    display:inline-block;
    display:inline;zoom:1;

The fallback for IE is just 1 (one) extra byte, so it won't make things _that_ bad. Actually, that would make it so your code would be more reusable, 'because you won't need to worry if the element is block level from the start.

Also, `if-ie.styl` overrides the `display` in a way it won't break any other overrides any other plugin could have done to `display`. So you can safely use it with nib or any other set of mixins.

### Redefining mixins

With `if-ie.styl` you could use your CSS3 mixins in a way that wouldn't bloat the IE stylesheet. Some extra bytesaving here :)

So, if you have such mixin:

``` Stylus
border-radius()
  -webkit-border-radius: arguments
  border-radius: arguments
```

And then you want to use it:

``` Stylus
.test
  width: 10px
  border-radius: 5px
```

you would get it's content in modern browsers, but what would IE get? It would get this:

``` CSS
.test {
  width: 10px;
}
```

Right now almost all of the  [nib](https://github.com/visionmedia/nib/)'s mixins are covered. Also, the `text-shadow` is stripped from IE.

There are still a lot of similar things to do (like removing the vendored gradients in IE etc.), so stay tuned!

### `rgba-ie()`

And now, the best part (well, the part I needed in my project and the one that can be really useful) — the function `rgba-ie()`.

While IE don't understand the regular `rgba`, you could often want it to have some fallback. People often use multiple props like 

``` CSS
.foo {
  color: #808080;
  color: rgba(0,0,0,0.5);
}
```

but it's not an easy task to determite which color to use with that special `rgba`, and also there are a lot of CSS-minifiers that could strip such repeating properties.

`rgba-ie` to the resque! It is the copy of the plain `rgba` function CSS and Stylus provide, but with a twist: if there is a `ie` variable set, this function would fallback to the default background (`#FFF` for now, but it would be global var someday) or to any other color, specified as the last argument in the function. And all the normal browsers would get the plain `rgba`.

You can use it like this:

``` Stylus
.foo
  color: rgba-ie(0,0,0,0.5)
.bar
  background: rgba-ie(#FFDE00, .42, #19C261)
```

this would render to this in normal browsers (offtopic: see the way Stylus gives you a way to use hex in `rgba`? Awesome!):

``` CSS
.foo {
  color: rgba(0,0,0,0.5);  
}
.bar {
  background: rgba(255,222,0,0.42);
}
```

but to this in IE:

``` CSS
.foo {
  color: #808080;
}
.bar {
  background: #7ace38;
}
```

Yay, magic! You can try to create a layer with `rgba(255,222,0,0.42)` over a `#19C261` background and then use a colorpicker to see that it's actually the `#7ace38` color!

And, of course, you can redefine the default fallback to any other color using `$default_rgba_fallback` variable:

``` CSS
$default_rgba_fallback = lime
```

### Clip support

`if-ie.styl` adds support for `clip` property, more precisely, for the `rect()` value syntax. The spec says there must be commas between it's arguments, but IE understands only space-separated arguments. So, `if-ie.styl` handles this and you can write your `rect`s like you want — in the result there would be commas for modern browsers and spaces for IE. So, 

``` Stylus
.foo
  clip: rect(0, auto, auto, 0)
```

would become this in IE: 

``` CSS
.foo {
  clip: rect(0 auto auto 0);
}
```

### Simple `nth-child` support

_feature inspired by [this codepen](http://codepen.io/scottkellum/pen/isytK)_

You can use simple (with generic numbers) `nth-child` that would work in IE. To do this you need to call it in that way:

``` Stylus
{nth-child(".foo", 3)}
  background: blue
```

You can see that the nth-child function goes in the interpolation. The first argument is the selector, the second is the counter of the child. Using the `n` in the counter is not available yet.

This code would produce this in normal browsers:

``` CSS
.foo:nth-child(3) {
  background: #00f;
}
```

and this in IE:

``` CSS
:first-child + * + .foo {
  background: #00f;
}
```

As a word of warning: such selectors can be **bad** for performance, so use this nth-child substitution at your own risk!

### `{lte_ie9}` selector

Sometimes you would want to write some styles both for OE9 and for older IE. Since IE9 would get the normal styles, the usual way of doing so would require copy-and-pasting. However, `if-ie.styl` prodides a nice shortcut for doing so: just prepend your rules by `{lte_ie9}` selector! That selector would render to nothing in older IEs, but in styles for normal browsers it would render to the `.ie9` selector instead.

In that way you could use Modernizr, conditional comments or any other way to set that class only in `ie9` and everything would work smoothly.

Sample usage:

``` Stylus
{lte_ie9}
  .test
    width: 10px
```

Older IE would get just the `.test` selector, while IE9 would get `.ie9 .test`.

You can redefine the class used for determining ie9 by defining the `ie9_class` class before including `if-ie.styl` like this:

``` CSS
ie9_class = 'IE_9'
@import "if-ie.styl/if-ie"
@import "style.styl"
```

- - -

To be continued!

Id you'd like to follow on the if-ie.styl progress, [follow me on twitter](https://twitter.com/kizmarh/).

- - -

Copyright (c) 2012 Roman Komarov <kizu@kizu.ru>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
