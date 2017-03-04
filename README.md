# Grouped SVG Sprite

### Create an SVG Sprite With the Object Element

by Daniel Blake

## Best of Both Worlds

Inline SVG Sprites work great when the origin code is in the actual HTML document. The `<use>` is a super efficient way to call up the icon you need to display in your sprite. The problem lies in using *external* files for the inline sprite. And aside from zero support in Internet Explorer[(1)](#refs), interactivity is extremely limited when using external SVGs this way.

Objects, on the other hand, are fantastic when it comes to interactivity and manipulation for an external SVG. You have complete control with css which that extends down to the individual paths, rects, lines, etc. Compatibility is better, an `<object>` linking to an external SVG is supported by IE 9+[(2)](#refs)[(3)](#refs). The only problem is that you can't implement `<use>` and display the icon you want from a single SVG file like you can with the Inline SVG Sprite.

Now if only there was a way to could combine the concept of Inline SVG Sprites and the control of the object element...

Well, there is! Using a concept I like to call 'Grouped SVG Sprite', we can achieve that. So, let's see how that's done.

## The External SVG

Here is our simple svg that we want to become a sprite:

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100">
    <g id="square">
        <rect x="20" y="20" width="60" height="60"/>
    </g>
    <g id="circle">
        <circle cx="50" cy="50" r="30"/>
    </g>
    <g id="triangle">
        <polygon points="50 20 15 80 85 80 50 20"/>
    </g>
</svg>
```

The groups `#square`, `#circle`, and `#triangle` contain what we want to be our icons. If you're familiar with inline svg sprites, this will look similar: instead of using the `<symbol>`, we'll be using the good ol' fashioned `<g>`.

The next step is to set those groups to `display: none`. The following are a couple of ways to this.

Presentation Attributes:
```xml
...

    <g id="square" display="none">...</g>

    <g id="circle" display="none">...</g>

    <g id="triangle" display="none">...</g>

...
```

Or, all at once with Inline CSS:

```xml
...

    <defs>

        <style>

            g { display: none }

        </style>

    </defs>

...
```

Finally, add a link to your main css before the `<svg>`:

```xml
<?xml-stylesheet type="text/css" href="your.css"?>
<svg>...</svg>
```

It's important to note, that when you want to manipulate and interact with the icon, in your linked css you will use the ID of the group, **not** the ID of the object in the HTML; which you'll see what I'm talking about next.

## The HTML

The html is pretty simple as well; it's just a matter of correctly naming the ID of the object that is associated with the ID of the group you want to show.

Setup your objects to have their `data` attribute pointing to the external svg file:

```html
<object type="image/svg+xml" data="sprite.svg">...</object>
```

Then give the objects their proper IDs to represent the icon they will be showing. To do that, simply use `lso-` + the group ID:

```html
<object id="lso-square" type="image/svg+xml" data="sprite.svg">...</object>

<object id="lso-circle" type="image/svg+xml" data="sprite.svg">...</object>

<object id="lso-triangle" type="image/svg+xml" data="sprite.svg">...</object>
```

And that's it for the HTML!

<!-- The JavaScript -->

## The JavaScript

What?! JavaScript?? I know, I know. But trust me, this script is painless and really small; and the payoff is completely worth it.

First, we'll create a function called groupedSVG:

```js

function groupedSVG() {



};
```

Then, using `querySelectorAll` we'll create an array to collect all of the objects that have an ID that begins with `gss-`:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

};
```

Now, we need to loop through the array of `objs` to reach each object individually:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.lenth; i++) {



    }

};
```

Then we'll create a variable that takes the current object's ID, turns that into a string, and replaces the prefix `gss-` with the ID selector `#`:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.lenth; i++) {

        var objID = objs[i].id.toString().replace('gss-','#');

    }

};
```

Next we'll use `contentDocument` to access the svg inside current object and create a variable with that:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.lenth; i++) {

        var objID = objs[i].id.toString().replace('gss-','#'),
        svgDoc = objs[i].contentDocument;

    }

};
```

Then, we'll create the last variable that will find the matching group ID in the svg to the object's ID:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.lenth; i++) {

        var objID = objs[i].id.toString().replace('gss-','#'),
        svgDoc = objs[i].contentDocument,
        gID = svgDoc.querySelector(objID);

    }

};
```

And finally, we'll tell that group to show itself!

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.lenth; i++) {

        var objID = objs[i].id.toString().replace('gss-','#'),
        svgDoc = objs[i].contentDocument,
        gID = svgDoc.querySelector(objID);

        gID.style.display = 'block';

    }

};
```

All that's left is to call this function; and this function **has** to be called when the document finishes loading. Otherwise, the JavaScript will access an empty object. You can do that one of two ways:

```js
window.onload = groupedSVG;
```

or

```js
window.addEventListener('load',groupedSVG,false);
```

And there you have it! Now you have a Grouped SVG Sprite, which means you have a sprite with the best interactive capabilities and manipulation by using css.

### References
{:#refs}

(1) [&lt;use&gt;  MDN](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/use)<br>
(2) [&lt;object&gt;  MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/object)<br>
(3) ['SVG'  caniuse](http://caniuse.com/#search=svg)
