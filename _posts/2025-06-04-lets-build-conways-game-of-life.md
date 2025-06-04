---
layout: post
title:  "Lets build #2 - Conway's game of life"
categories: tutorials web
athor: mihna123
---

I was wondering what should I make today, and I stumbled upon this little game
called "John Conway's game of life".

It seemed very interesting and I thought to myself: "How would I build 
something like this?"

# The game

Soo the game is initialy a large grid composed of cells. These cells can eather
be dead or alive based on some rules. If they are alive the cell is colored
black, when dead it is white.

The rules are as follows:

- If cell has less than two alive neighbours, it dies (as of starvation)
- If cell has three alive neighbours, it becomes alive (as of reproduction)
- If cell gas more than three alice neighbours, it dies (as of overpopulation)

This rather simple rule set can produce a very interesting gameplay as you will
see later. This logic set is actually turing complete, so you can essentialy 
build a computer inside this game. Some people actually built a game of life
inside game of life, which is pretty crazy.

# The building process

I decided that we are once again just gonna use the plain old **java script**
for this job.

I think that the simplest way of doing this is just to have a large grid of 
divs. Each div will represent a cell. When the div has a `"cell-on"` class, the
cell is alive.

Let's get going!

Firstly we will grab this html boilerplate and paste it into `index.html`:

{% highlight html %}

<!DOCTYPE html>

<html lang="en">

<head>
    <meta charset="UTF-8" />
    <title>John Convay's Game of Life</title>
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <meta name="description" content="" />
    <link rel="icon" href="favicon.png">
    <link rel="stylesheet" href="style.css">
</head>

<body>
    <h1>John Conway's Game of Life</h1>
    <div class="flex">
        <button class="tick-btn">tick</button>
        <button class="play-btn">play</button>
        <button class="stop-btn">stop</button>
    </div>
    <section class="game"></section>
    <noscript>Please enable java script or use another browser.</noscript>
    <script src="app.js"></script>
</body>

</html>


{% endhighlight %}

Then create the `style.css` file:


{% highlight css %}

body {
    background-color: gray;
    color: white;
}

div.cell {
    width: 30px;
    height: 30px;
    background-color: white;
}

div.cell:hover {
    background-color: gray;
}

div.cell-on {
    background-color: black;
}

.flex {
    display: flex;
}

.row {
    flex-direction: row;
}

.col {
    flex-direction: column;
}


{% endhighlight %}

This should be good, now onto making the actual cells.

# Making the cells work

I imagine a good way of doing this is to have a number of row flex divs, each 
containing a number of actual cells. This way we have a sort of matrix of divs,
each representing one cell.

Create a file named `app.js`. This will contain all our game logic. 

Let's create some variables that would be usefull for our game:

{% highlight js %}

const COLUMNS = 100;
const ROWS = 100;

// Lets also grab the section where we want to keep our game
/** @type {HTMLElement} */
const gameSection = document.querySelector("section.game");

{% endhighlight %}

Next step would be adding all the div cells. Here is one way to do it:


{% highlight js %}

for (let i = 0; i < ROWS; ++i) {
	const row = document.createElement("div");
	row.classList.add("flex");
	for (let j = 0; j < COLUMNS; ++j) {
		const cell = document.createElement("div");
		cell.classList.add("cell");
		row.appendChild(cell);
	}
	gameSection.appendChild(row);
}

{% endhighlight %}

If you save all the files and open up the `index.html` file in the browser,
you will notice that we have just a bunch of divs that don't do much.

Lets allow the user to turn any cell into a living one by clicking on it:


{% highlight js %}

/** @type {HTMLDivElement[]} */
const cells = document.querySelectorAll("div.cell");
for (const cell of cells) {
	cell.onclick = () => {
		cell.classList.toggle("cell-on");
	};
}

{% endhighlight %}

It should look like this now:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/game-of-life-01.gif" | prepend: site.url}}" width="800" alt="Game of life click demo"/>
</p>

So next step would be to add the actual logic of the rules into our game.

# Adding rules

So this is how we are going to do this:

- Make a function called `tick` that contains the rules logic
- Go through all the cells and count the number of closest live neighbours
- Mark the cell to **die** or **become alive** depending on the number of neighbours

So here it is:


{% highlight js %}

function tick() {
	const toDie = [];
	const toLive = [];
        // Go trough each cell using two loops
	for (let i = 0; i < ROWS; ++i) {
		for (let j = 0; j < COLUMNS; ++j) {
                        // Get the cell that we are counting for
			const row = gameSection.children.item(i);
			const cell = row.children.item(j);
			let neighbours = 0;
                        // Go trough 8 of its neighbours 
                        // This can be done without loops but I found this to be more elegant
			for (let x = -1; x < 2; ++x) {
				for (let y = -1; y < 2; ++y) {
                                        // I know how ugly the namings are...
					const xind = i + x;
					const yind = j + y;
                                        // Check if we are out of bounds
					if (xind < 0 || yind < 0 || xind >= ROWS || yind >= COLUMNS) {
						continue;
					}
                                        // Get the neighbouring cell
					const ncell = gameSection.children.item(xind).children.item(yind);
                                        // Dont count self as a neighbour
					if (cell.isSameNode(ncell)) {
						continue;
					}
                                        // Up the counter if neighbour is alive
					if (ncell.classList.contains("cell-on")) {
						++neighbours;
					}
				}
			}
                        // The actual game logic is simple
			if (neighbours < 2 || neighbours > 3) {
				toDie.push(cell);
			}
			if (neighbours === 3) {
				toLive.push(cell);
			}
		}
	}
        // We alive or kill the cell based on previous rules and that is that
	for (const c of toLive) {
		c.classList.add("cell-on");
	}

	for (const c of toDie) {
		c.classList.remove("cell-on");
	}
}

{% endhighlight %}

In order for us to be able to see if this is working, lets add a button that 
will invoke the `tick` function on click.

Add this in the **body** of the `index.html` 

{% highlight html %}

<button class="play-btn">play</button>

{% endhighlight %}

Add this to the end of the `app.js`

{% highlight js %}

document.querySelector("button.tick-btn").onclick = tick;

{% endhighlight %}

This is the result:

<p align="center">
    <img style="text: centered" src="{{"/assets/images/game-of-life-02.gif" | prepend: site.url}}" width="800" alt="Game of life tick demo"/>
</p>

Now it is only a matter of making the game play automatically.

# Finishing touches

We are going to add two buttons bellow our initial start button:

{% highlight html %}

<button class="play-btn">play</button>
<button class="stop-btn">stop</button>

{% endhighlight %}

Then in the `app.js` file we are going to handle the button presses:

{% highlight js %} 

// Use this variable for event handle
let playEv = undefined;

// Play button starts the interval
document.querySelector("button.play-btn").onclick = () => {
	if (playEv) return;
	playEv = setInterval(tick, 100);
};

// Stop button clears the interval
document.querySelector("button.stop-btn").onclick = () => {
	clearInterval(playEv);
	playEv = undefined;
};

{% endhighlight %}

# The end result

Feels good when you finish a project like this. Play the game on [github pages](https://mihna123.github.io/game-of-life){:target="_blank"}
or check out the result below and if you want the full source code, go to the [github link](https://www.github.com/mihna123/game-of-life){:target="_blank"}.


<p align="center">
    <img style="text: centered" src="{{"/assets/images/game-of-life-03.gif" | prepend: site.url}}" width="800" alt="Game of life finished"/>
</p>
