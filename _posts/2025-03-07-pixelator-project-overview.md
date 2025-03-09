---
layout: post
title:  "Project overview #1 - Pixelator 2.0"
date:   2025-03-07 17:22:40 +0100
categories: personal-projects web
---

Pixelator is a **free online editor for animated sprites and pixel art** heavily inspired by 
[piskelapp.com](https://www.piskelapp.com){:target="_blank"}. You can check out the
demo at [mihna123.github.io/pixelator-2.0](https://mihna123.github.io/pixelator-2.0){:target="_blank"}

This is a passion project of mine. I used to make some games back in the day
and I used tools like **Pixelator** a lot. The past couple of weeks have been
a little bit uneventful soo I started building.

The tech stack is quite simple as the only dependency that I used was 
**tailwindcss**. This was a mistake in retrospective, tailwind is great when
you are working with components _(like React or Angular)_, but when you need
to repeat the same old `w-full` a million times it gets kinda old. My html
is pretty ugly too if you take a look at it:

{% highlight html %}
<button data-tool="bucket" title="Bucket tool"
    class="tool-button w-10 h-10 flex justify-center items-center bg-gray-900 text-white m-1 hover:bg-gray-800 p-2">
    <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 16 16">
        <path fill="currentColor" fill-rule="evenodd"
            d="M11.773 7.412c-.064.064-.27.249-1.017-.027c-.75-.277-1.706-.928-2.695-1.918c-.99-.99-1.64-1.945-1.918-2.695c-.276-.747-.091-.953-.027-1.017c.064-.064.27-.249 1.017.027c.094.035.19.075.29.121c.7.324 1.54.93 2.405 1.797c.99.99 1.641 1.945 1.918 2.695c.276.747.091.953.027 1.017M7 6.528c.85.85 1.738 1.535 2.581 1.972H1.694v-.027a1.292 1.292 0 0 1 .1-.507l2.802-4.33c.056-.087.114-.174.172-.26C5.16 4.383 5.956 5.485 7 6.529Zm3.89-3.889c2.147 2.148 3.24 4.537 1.944 5.834a12.976 12.976 0 0 1-2.127 1.719L6.352 13.01s-1.945 1.296-4.537-1.296C-.778 9.12.518 7.175.518 7.175L3.336 2.82A12.98 12.98 0 0 1 5.056.694C6.351-.602 8.74.491 10.888 2.64Zm1.86 12.36c.966 0 1.75-.765 1.75-1.71c0-1.234-1.17-2.76-1.512-3.178a.304.304 0 0 0-.237-.111a.31.31 0 0 0-.24.112c-.34.422-1.511 1.96-1.511 3.178c0 .944.784 1.71 1.75 1.71Z"
            clip-rule="evenodd" />
    </svg>
</button>
... and the same button a million times over
{% endhighlight %}

But I must say that I learned a lot about buffers, `jsdoc` and the `canvas` element.

# Features

Here are the features (so far):

## Pen tool

Good old pen tool, comes in 4 different brush sizes and all 
the colors you can imagine!

<p align="center">
    <img style="text: centered" src="{{"/assets/images/pixelator-pen.gif" | prepend: site.url}}" width="450" alt="Pixelator pen showcase"/>
</p>

## Line tool

Makes it easy to draw straight lines, duh

<p align="center">
    <img style="text: centered" src="{{"/assets/images/pixelator-line.gif" | prepend: site.url}}" width="450" alt="Pixelator pen showcase"/>
</p>


## Rectangle tool 
You can even draw a rectangle of your choice! Be carefull
tho, it is a little bit buggy _(if you can fix it I would be gratefull)_

<p align="center">
    <img style="text: centered" src="{{"/assets/images/pixelator-square.gif" | prepend: site.url}}" width="450" alt="Pixelator pen showcase"/>
</p>

## Bucket tool

So you don't have to manualy fill in all those sprites.

<p align="center">
    <img style="text: centered" src="{{"/assets/images/pixelator-bucket.gif" | prepend: site.url}}" width="450" alt="Pixelator pen showcase"/>
</p>

## Eraser tool
You will need this one

<p align="center">
    <img style="text: centered" src="{{"/assets/images/pixelator-remove.gif" | prepend: site.url}}" width="450" alt="Pixelator pen showcase"/>
</p>

## Frame manipulation

Add, copy, delete and move frames as you wish! You 
can see the animation on the right preview screen.

<p align="center">
    <img style="text: centered" src="{{"/assets/images/pixelator-frames.gif" | prepend: site.url}}" width="450" alt="Pixelator pen showcase"/>
</p>


## Exporting

You can export your beautiful sprite to a png format!

# Source

You can contribute and find source on 
[Github](https://github.com/mihna123/pixelator-2.0){:target="_blank"}
