---
layout: post
title:  "Minimalist comments engine for personal blogs"
categories: tutorials web
author: mihna123
---

I wanted a comments solution that was fast, simple and had no sign ups. So I
built a simple solution.

# The problem with other comment engines

I used to use `Disqus` for my comments. The problem with similar solutions is 
the traction associated with trying to post a comment when you dont have an 
account. 

All I wanted was a solution that let anyone just enter the desired username and
comment. No accounts, no limitations. The OG way of commenting.

# The problem with my proposal

Obviously, the problem is that anyone can pick any username, there is no 
authentication. Yeah that would be a problem if I had any readers! If someone
stumbles upon some of my blogs, I dont want to scare them away with asking for
their email and making them create an account. This is so simple and there is 
really no strings attached.

# How did I build it

It is just a very simple api done in `next.js`. Here is the whole code:

{% highlight js %}

import { NextRequest, NextResponse } from "next/server";
import { neon } from "@neondatabase/serverless";

const allowCORSHeaders = {
	"Access-Control-Allow-Origin": "*",
	"Access-Control-Allow-Methods": "GET, OPTIONS, POST",
	"Access-Control-Allow-Headers": "Content-Type",
};

export async function OPTIONS() {
	return new Response(null, {
		status: 204,
		headers: allowCORSHeaders,
	});
}

export async function GET(request, { params }) {
	const { post } = await params;
	const sql = neon(process.env.DATABASE_URL);
	const comments = await sql.query("SELECT * FROM comments WHERE post = $1", [
		post,
	]);
	comments.reverse();
	return new NextResponse(
		`<ul class="simple-comments">
            ${comments.map((c) => `<li><p>${c.username} - ${c.createddate.toUTCString()}:</p><p>${c.comment}</p></li>`).join("")}
        </ul>`,
		{
			headers: { "content-type": "text/html", ...allowCORSHeaders },
			status: 200,
		},
	);
}

/**
 * @param {NextRequest} request
 * */
export async function POST(request, { params }) {
	const formData = await request.formData();

	const { post } = await params;
	const username = formData.get("username");
	const comment = formData.get("comment");

	if (!username || !comment) {
		return new NextResponse("Missing fields", {
			status: 400,
			headers: allowCORSHeaders,
		});
	}

	const sql = neon(process.env.DATABASE_URL);

	await sql.query(
		`INSERT INTO comments 
            (post, username, comment, createddate) 
            VALUES ($1,$2,$3, current_timestamp)`,
		[post, username, comment],
	);

	return new NextResponse("Success", {
		status: 200,
		headers: allowCORSHeaders,
	});
}

{% endhighlight %}

So as you can see, one simple way of fetching comments based on what post
they are refering to, and one simple way of posting comments to some post.

Very wasy to implement.

The second thing I needed was the frontend script that would make it easy to 
just paste the comment section in any page I wanted. Here is the script:


{% highlight js %}

const form = document.createElement("form");

const pathParts = window.location.pathname.split("/");
let postName = pathParts[pathParts.length - 1];

if (postName.length === 0 || !postName) {
	postName = pathParts[pathParts.length - 2];
}

const url = `https://simple-comments.vercel.app/${postName}`;

form.classList.add("simple-comments-form");

form.innerHTML = `
    <label for="username">Username</label>
    <br/>
    <input required type="text" name="username"/>
    <br/>
    <label for="comment">Comment</label>
    <br/>
    <textarea required cols="50" name="comment"></textarea>
    <br/>
    <button type="submit">Post comment</button>
`;

function htmlToNode(html) {
	const template = document.createElement("template");
	template.innerHTML = html;
	return template.content.firstChild;
}

async function getComments() {
	const res = await fetch(url);
	const bodyHtml = await res.text();
	if (!bodyHtml) return;
	document.querySelector("ul.simple-comments")?.remove();
	form.after(htmlToNode(bodyHtml));
}

/**
 * @param {SubmitEvent} e
 * */
async function onSubmit(e) {
	e.preventDefault();
	try {
		await fetch(url, {
			method: "POST",
			body: new FormData(e.target),
		});
		form.querySelectorAll("input, textarea").forEach((i) => (i.value = ""));
		await getComments();
	} catch (e) {
		console.error(e);
	}
}

form.onsubmit = onSubmit;
document.currentScript.after(form);
getComments();

{% endhighlight %}

The script fetches the comments and refreshes them after each submit. Very easy.

The only thing the user needs to do is paste in the `script` tag with the 
source leading to this script and thats that.

# The customization

You can customize the comments very easily. Just make some class rules in css
for `.simple-comments-form` and `ul.simple-comments`. If you want the look of
my comment section here is the css:

{% highlight css %}

.simple-comments-form {
    margin-bottom: 20px;
}

.simple-comments-form button[type="submit"] {
    margin-top: 5px;
    background-color: rgba(0, 0, 0, 0.5);
    padding: 10px;
    border: none;
    color: inherit;
    cursor: pointer;
    border-radius: 3px;
}

.simple-comments-form button[type="submit"]:hover {
    background-color: rgba(0, 0, 0, 0.8);
}

.simple-comments p {
    font-size: initial;
    margin: 0;
}

.simple-comments li>p:last-of-type {
    margin-left: 20px;
    padding-left: 5px;
    background-color: rgba(0, 0, 0, 0.5);
}

{% endhighlight %}

# The conclusion

This is definetely not something you want to use on a large scale project or a
realy big blog, but for my usecases it is the best thing.  
Maybe we will update the functionalities if the need arises. If you want to 
check out the source you can do so on [my Github]("https://www.github.com/mihna123/simple-comments")

Try and comment below to see how it works (be nice!)
