---
layout: post
title:  "Lets build #1 - Matrix rain"
categories: tutorials web
author: mihna123
---

Have you ever thought to yourself: "How the hell do I make that damn matrix 
rain animation thing in JavaScript"? 

You probably have not, but anyways, I need this letter falling down animation 
for a **million user app landing page** that I am building right now. Since 
this should be a relatively easy thing to make I decided to make it a tutorial 
to hopefully help you if you want to build something similar.

# The research

Bu surfing the web I have found a couple websites for reference:
- [https://matrix.logic-wire.de/](https://matrix.logic-wire.de/)
- [https://www.quenq.com/matrix/index.html](https://www.quenq.com/matrix/index.html)
- [https://codepen.io/tommyho/pen/NWZRvwj](https://codepen.io/tommyho/pen/NWZRvwj)

It seems that all these examples use the canvas element to animate the letters.
We will go this way as well.

# Initialising the project

First things first, go to your project folder and create html css and js files
with `$ touch index.html script.js style.css`. After this paste this boilerplate
into the html and css:

{% highlight html %}
<!-- index.html -->
<!DOCTYPE html>
<html>

<head>
    <title>Matrix rain project</title>
    <link rel="stylesheet" href="style.css" />
</head>

<body>
    <canvas class="m-rain" width="1380" height="668">
        Your browser does not support the canvas element.
    </canvas>
    <script src="script.js" type="module" defer />
</body>

</html>
{% endhighlight %}

{% highlight css %}
/* style.css */
html,
body {
    padding: 0px;
    margin: 0px;
}

canvas {
    border: 3px solid black;
}

{% endhighlight %}

We just have a canvas element that indicates wether the browser supports such 
element. We will do most of our work inside the `script.js` file.

# Working with the canvas element

In order for us to draw something to the canvas element we will have to get 
the 2d canvas context. This object has all the methods we need to draw anything
we want.

{% highlight js %}

// script.js

/** @type {HTMLCanvasElement | null} */
const canvas = document.querySelector("canvas.m-rain");

// Set width and height to window
canvas.style.width = `${window.innerWidth}px`;
canvas.style.height = `${window.innerHeight}px`;

// Get the canvas context
const ctx = canvas.getContext("2d");
// Paint the entire canvas black
ctx.fillRect(0, 0, canvas.width, canvas.height);

{% endhighlight %}

# Rendering text

As we will need to render a lot of text on the screen, lets try to just render
the good old "Hello world!". The method to do so is `fillText()`.


{% highlight js %}
// script.js

// ...the code from before

// Set the fill style so the text is not black
ctx.fillStyle = "white";
// Make the font bigger
ctx.font = "34px sans";

ctx.fillText("Hello world!", 100, 100);

{% endhighlight %}

This will basically tell the canvas to render the "Hello world!" string at
coordinates (100, 100);

This is how the page should look like right now:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/matrix-01.png" | prepend: site.url}}" width="1000" alt="Screenshot of the project #1"/>
</p>

# Making things move

Okay so we now know how to render some text, but this is now why we came here,
we want to make some matrix rain god damnit! Let's animate some letters right
now.

Firstly we will some variables to keep track of all the letter positions, font
sizes and so on...


{% highlight js %}
// script.js

// Letter that we want to animate
const letter = "O";
/** @type {[Number,Number][]} 
* This is where we will store the x and y axis for our falling letter
*/
const letterPositions = [];
const fontSize = 8;

{% endhighlight %}

Then we will want a function that spawns the letter at a random position above
our canvas:

{% highlight js %}

//script.js

function randomSpawn() {
    // Random x
	const x = Math.round(Math.random() * canvas.width);
    // Above the canvas
	const y = -10;

	letterPositions.push([x, y]);
    // Keep the max letter count up to 500 for performance
    if (letterPositions.length > 500) {
        letterPositions.shift();
    }
}

{% endhighlight %}

Okay now we have our variables and a method that spawns a new letter into our
array. The last thing that we need for now is a `tick()` method that would act
as like a render method. The `tick()` needs to refresh the screen, then draw
all the letters from the array and then nudge the letters down a bit. I 
came up with something like so:


{% highlight js %}

// script.js

function tick() {
    // We refresh the page with a black that has a little transparency for a nice effect
	ctx.fillStyle = "#0000000a";
	ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Set up the white font
	ctx.fillStyle = "white";
	ctx.font = `${fontSize}px sans`;

    // For every letter draw it to the screen
	for (const pos of letterPositions) {
		ctx.fillText(letter, pos[0], ++pos[1]);
	}
}

{% endhighlight %}

Finnaly put both the `tick()` and `randomSpawn()` inside an interval. You can
play around with the time values for different effects.


{% highlight js %}

//script.js

setInterval(tick, 40);
setInterval(randomSpawn, 100);

{% endhighlight %}

Your page should look something like this:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/matrix-02.gif" | prepend: site.url}}" width="1000" alt="Recording of the project #1"/>
</p>

Its kinda mesmerizing huh? I caugth myself staring at this screen for way too 
long dude.

# The finishing touches

It may not seem like it, but we are actually done. I sugest that you try and
do the rest alone, since it is a nice exercise. The finished product should 
look like this:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/matrix-03.gif" | prepend: site.url}}" width="1000" alt="Recording of the project #2"/>
</p>

If you want to see the code here it is aswell:


{% highlight js %}

//script.js

/** @type {HTMLCanvasElement | null}*/
const canvas = document.querySelector("canvas.m-rain");
canvas.style.width = `${window.innerWidth}px`;
canvas.style.height = `${window.innerHeight}px`;

const ctx = canvas.getContext("2d");
ctx.fillRect(0, 0, canvas.width, canvas.height);

const letters = "马吾伊艾哦儿屁艾勒艾诶娜迪艾弗吉尺艾杰艾艾西吉比艾开";
/** @type {[Number, Number, Number][]} */
const letterPositions = [];
const fontSize = 18;
const numOfColumns = 20;
const maxLetters = 500;

function randomSpawn() {
	const x = Math.round(Math.random() * canvas.width);
	const y = -10;
	const s = Math.floor(Math.random() * 25 + 5);

	letterPositions.push([x, y, s]);
	if (letterPositions.length > maxLetters) {
		letterPositions.shift();
	}
}

function tick() {
	ctx.fillStyle = "#0000002a";
	ctx.fillRect(0, 0, canvas.width, canvas.height);
	ctx.fillStyle = "green";
	for (const pos of letterPositions) {
		ctx.font = `${pos[2]}px sans`;
		ctx.fillText(
			letters.split("")[Math.floor(Math.random() * letters.length)],
			pos[0],
			pos[1],
		);
		pos[1] += pos[2];
	}
}

setInterval(tick, 50);
setInterval(randomSpawn, 5);

{% endhighlight %}
