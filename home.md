---
permalink: /
layout: base
title: Grouped SVG Sprite
description: Grouped SVG Sprite - Create an SVG Sprite With the Object Element
subtitle: Create an SVG Sprite With the Object Element
image:
   src: gss.svg
   alt: grouped svg sprite icon
---

## Best of Both Worlds

Inline <abbr title="Scalable Vector Graphics">SVG</abbr> Sprites work great when the source code is in the _actual_ HTML document. The `<use>` element is an efficient way to reference the icon you want and call it to display. The problem lies when using _external_ files for the inline sprite; especially if you have a huge SVG document and you are not looking to bloat your <abbr title="Hypertext Markup Language">HTML</abbr> document with all that SVG code. Aside from _zero_ support in Internet Explorer[(1)](#refs), interactivity is limited when using external SVGs as inline SVG sprites.

On the other hand, `<object>` elements are fantastic when it comes to interactivity and manipulation for an external SVG. You have a pretty good amount of control with CSS that also includes some control over the inner elements `<path>`, `<rect>`, `<line>`, etc. Compatibility is also better; an `<object>` linking to an external SVG is supported by IE 9+[(2)](#refs)[(3)](#refs). The only problem is that you can't implement `<use>` and display the different icons you want from a single SVG file like you can with the Inline SVG Sprite.

Now if only there was a way to could combine the concept of Inline SVG Sprites with the control of the object element on external SVGs...

Well, there is! Using a concept I like to call 'Grouped SVG Sprites', we can achieve that. So, let's see how that's done.

## The External SVG

Here is our simple SVG that we want to become a sprite:

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

The groups `#square`, `#circle`, and `#triangle` contain what we want to be our icons. If you're already familiar with Inline SVG Sprites, this will look very similar: instead of using `<symbol>`, we'll be using the good ol' fashioned `<g>`.

The first step is to set those groups to `display: none`. The following are just a couple of ways to do this.

Using presentation attributes:
```xml
...

    <g id="square" display="none">...</g>

    <g id="circle" display="none">...</g>

    <g id="triangle" display="none">...</g>

...
```

Or, using inline <abbr title="Cascading Style Sheets">CSS</abbr>:

```xml
...

    <defs>

        <style><![CDATA[

            #square, #circle, #triangle { display: none }

       ]]></style>

    </defs>

...
```

Then, add an <abbr title="Extensible Markup Language">XML</abbr> stylesheet link to reference your main CSS before the opening `<svg>`:

```xml
<?xml-stylesheet type="text/css" href="your.css"?>
<svg>...</svg>
```

It's important to note here that when you want to manipulate and interact with the icon, in your CSS you will use the ID of the group, **not** the ID of the object in the HTML (you'll see what I'm talking about next).

## The HTML

The HTML is pretty simple as well; it's just a matter of correctly naming the ID of the object that is associated with the ID of the group you want to show.

First, setup your objects to have their `data` attribute pointing to the external SVG file:

```html
<object type="image/svg+xml" data="sprite.svg">...</object>
```

Then give the objects their proper IDs to represent the icon they will be showing. To do that, in this case, I'll prefix `gss-` to the group ID:

```html
<object id="gss-square" type="image/svg+xml" data="sprite.svg">...</object>

<object id="gss-circle" type="image/svg+xml" data="sprite.svg">...</object>

<object id="gss-triangle" type="image/svg+xml" data="sprite.svg">...</object>
```

And that's it for the HTML!

<!-- The JavaScript -->

## The JavaScript

What?! JavaScript?? I know, I know. But trust me, this script is painless and really small; and the payoff is completely worth it.

To start off, we'll create a function called `groupedSVG`:

```js

function groupedSVG() {  // Step One



};
```

Then, using `querySelectorAll` we'll create an array called `objs` to collect all of the objects that have an ID that begins with `gss-`:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');  // Step Two

};
```

Now, we need to loop through the array of `objs` to reach each object individually:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.length; i++) {  // Step Three



    }

};
```

Then we'll create a variable called `objID` that takes the current object's ID, turns that into a string, and replaces its prefix `gss-` with the ID selector `#`:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.length; i++) {

        var objID = objs[i].id.toString().replace('gss-','#');  // Step Four

    }

};
```

Next we'll use `contentDocument` to access the SVG mini document inside current object and with that create a variable called `svgDoc`:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.length; i++) {

        var objID = objs[i].id.toString().replace('gss-','#'),
        svgDoc = objs[i].contentDocument;  // Step Five

    }

};
```

Then, we'll create the last variable `gID` that will find the matching group ID in the SVG to the object's ID:

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.length; i++) {

        var objID = objs[i].id.toString().replace('gss-','#'),
        svgDoc = objs[i].contentDocument,
        gID = svgDoc.querySelector(objID);  // Step Six

    }

};
```

And finally, we'll tell that group to show itself by settings its diplay to `block`.

```js
function groupedSVG() {

    var objs = document.querySelectorAll('object[id^=gss-]');

    for (var i = 0; i < objs.length; i++) {

        var objID = objs[i].id.toString().replace('gss-','#'),
        svgDoc = objs[i].contentDocument,
        gID = svgDoc.querySelector(objID);

        gID.style.display = 'block';  // Step Seven

    }

};
```

All that's left is to call this function. This function **has to be** called when the document finishes loading. Otherwise, the JavaScript will access an empty object. You can do that one of two ways:

```js
window.onload = groupedSVG;
```

or

```js
window.addEventListener('load',groupedSVG);
```

And there you have it! Now you have a Grouped SVG Sprite, which means you have a sprite with the best interactive capabilities and manipulation by using css.

## Futher Reading:
## Making Your Grouped SVG Sprite Clickable

So, if you're trying to use the Grouped SVG Sprite as a clickable icon, you may quickly realize that you can't actually click on it. What's happening is that the object element creates a nested document inside itself, which is normal behavior for an object containing an SVG. So when you mouse over this object, you are basically escaping the main document and hovering into this newly created mini document.

Have no fear, you can set up your Grouped SVG Sprite to handle this problem!

### Setting Up a 'Clickable' Area

So, here is our Grouped SVG Sprite that's set with inline styling:

```xml
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100">
<?xml-stylesheet type="text/css" href="../css/master.css"?>

   <defs>

      <style><![CDATA[

         #square, #circle, #triangle { display: none }

      ]]></style>

   </defs>

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

First, we need to add the XML `xlink` namespace to our SVG document:

```xml
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 100 100">
```

Next, create a rectangle that is the exact size of the SVG's viewBox to ensure all of the SVG is tangible. Since we don't actually want to see rectangle, we can set the opacity to 0. We'll place it in the `defs` and give it a reference ID of `#click`:

```xml
...
   <defs>

      <style><![CDATA[

         #square, #circle, #triangle { display: none }

      ]]></style>

      <rect id="click" width="100" height="100" opacity="0"/>

   </defs>
...
```

Lastly, we'll reference those rectangles with `use` above the icon in their groups:

```xml
...
   <g id="square">
      <use xlink:href="#click"/>
      <rect x="20" y="20" width="60" height="60"/>
   </g>
   <g id="circle">
      <use xlink:href="#click"/>
      <circle cx="50" cy="50" r="30"/>
   </g>
   <g id="triangle">
      <use xlink:href="#click"/>
      <polygon points="50 20 15 80 85 80 50 20"/>
   </g>
...
```

### Setting Up the Links

To start, wrap each group in an anchor tag:

```xml
...
   <a><g id="square">
      <use xlink:href="#click"/>
      <rect x="20" y="20" width="60" height="60"/>
   </g></a>
   <a><g id="circle">
      <use xlink:href="#click"/>
      <circle cx="50" cy="50" r="30"/>
   </g></a>
   <a><g id="triangle">
      <use xlink:href="#click"/>
      <polygon points="50 20 15 80 85 80 50 20"/>
   </g></a>
...
```

Then, create `xlink:href` attributes that will share the same link that the icon in the HTML file has:

```xml
...
   <a xlink:href="destination/here"><g id="square">
      <use xlink:href="#click"/>
      <rect x="20" y="20" width="60" height="60"/>
   </g></a>
   <a xlink:href="destination/here"><g id="circle">
      <use xlink:href="#click"/>
      <circle cx="50" cy="50" r="30"/>
   </g></a>
   <a xlink:href="destination/here"><g id="triangle">
      <use xlink:href="#click"/>
      <polygon points="50 20 15 80 85 80 50 20"/>
   </g></a>
...
```

Finally, because this SVG document is a separete document, we **need** to declare a `target` attribute. Otherwise, the object will literally load the destination in itself. The only time you won't need to use a `target` is if you're using _special&nbsp;links_ such as `mailto:` or `tel:`.

Some target options are:<br>
A) `_blank` if you plan to have the link take the user to a different site.<br>
B) `_top` if the link is going to a reference ID on the HTML page. For that, you'll also need to reference it to the directory where that page resides; i.e. your page is at the root directory and your sprite is at `[root]/img/sprite.svg`, you'll want the href to point _back_ two directories `../../#referenceID`.

Here's an example:
```xml
...
   <a xlink:href="../../#someID" target="_top"><g id="square">
      <use xlink:href="#click"/>
      <rect x="20" y="20" width="60" height="60"/>
   </g></a>
   <a xlink:href="http://flexrgrid.com" target="_blank"><g id="circle">
      <use xlink:href="#click"/>
      <circle cx="50" cy="50" r="30"/>
   </g></a>
   <a xlink:href="mailto:some@email.com"><g id="triangle">
      <use xlink:href="#click"/>
      <polygon points="50 20 15 80 85 80 50 20"/>
   </g></a>
...
```

Once, you've applied the proper target attributes, you're done! And now you have a Grouped SVG Sprite that is clickable.


### References
{:#refs}

(1) [&lt;use&gt;  MDN](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/use)<br>
(2) [&lt;object&gt;  MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/object)<br>
(3) ['SVG'  caniuse](http://caniuse.com/#search=svg)
