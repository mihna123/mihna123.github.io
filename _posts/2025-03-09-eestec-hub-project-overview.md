---
layout: post
title:  "Project overview #2 - EESTEC Hub"
date:   2025-03-09 18:28:31 +0100
categories: personal-projects web
author: mihna123
last_modified_at: 2025-03-10 15:11:30 +0100
---

[EESTEC Hub](https://eestec-hub.vercel.app){:target="_blank"} is an HRM 
platform designed to suit the **human resource automation** needs of student 
organizations.

---

## Table of contents

1. [The problem](#the-problem)
2. [The proposed solution](#the-proposed-solution)
3. [The development](#the-development)
    1. [Problem with forms](#problem-with-forms)
    2. [Solution to the forms problem](#solution-to-the-forms-problem)
    3. [Problem with calculating member points](#problem-with-calculating-member-points)
    3. [Solution to the points problem](#the-solution-to-the-points-problem)
4. [The result](#the-result)
5. [Future of development](#future-of-development)
5. [Tech stach](#tech-stack)
6. [Source code and contributing](#source-code-and-contributing)

---

_If you dont want to read the whole development story and want to go straight
to results, go to [the result section](#the-result)_


# The problem

[EESTEC LC Novi Sad](https://www.eestecns.org){:target="_blank"} is a rather 
large student organization with around **200 active members**. Board members 
have a big task of following each members activity within the organization. 
Activity is considered anything from attending organization events to being a 
part of an **internal team** or being a part of a **project organizing team**.

Tracking members activity is very important as the Board of the organization
has to give priorities to more active members when it comes to getting travel
reimbursements and when it comes to events with limited spots available.
The board also needs to know the **evaluation** of each member that applies to 
work on some project as the coordinator spots on projects are limited also.

How the Board members used to solve this was by having a spreadsheet ( _before
the spreadsheet this was done on paper 😨_ ) where they would write when some 
member attended an event or when some member would join a team. This was, as
you can imagine, very clumbersome and error prone. 

This activity tracking has historicaly been known to have lost data, data that
was hard to understand by the Board. Many times Board members restart the 
evaluation and tracking process every year as the data from the year before is 
missing or very poor.

# The proposed solution

Enter [EESTEC Hub](https://eestec-hub.vercel.app){:target="_blank"}. The idea
behind this app was quite simple. It was supposed to have a limited but usefull
set of features like 

- **User authentication** - All members having their user profiles
- **Projects** - Board members create them so that members can apply
- **Events** - Board members create them and members can enter
- **Teams** - Members should be able to join teams
- **Point system** - All users should be able to be evaluated based on activity
- **Member management** - Board members should be able to manage members

# The development

For the solution of the problem I went with **NextJS** with **Tailwindcss** 
and **MongoDB**. I went with this stack sice I am very comfortable with it 
and I didn't have much time to experiment. You can find a more details on 
the [tech stack segment](#tech-stack).

This app was going to be used mostly by the members from mobile devices so I 
knew that I had to go with the **mobile-first** approach. This was quite easy
to do with **Tailwindcss** and I found that it definetely **reduced the time
of development** for me a lot. The downside was that I was left with kind of long
html tags. Like this div for example:

{% highlight html %}
<div className="flex justify-between items-center py-2 border border-solid border-gray-300">
{% endhighlight %}

But once you accept this, your life becomes much easier.

## Problem with forms

I had a big headache with forms in NextJS. When you read the official docs,
you are incentivized to make your components 
[server rendered](https://nextjs.org/docs/pages/building-your-application/rendering/server-side-rendering){:target="_blank"}
as much as possible. This is because pages rendered on the server perform much
better and the **SEO** is way better compared to classical React
client rendered components.  

As you can imagine this application had a lot of `<form>` elements in it by
design:

- User log in and sign up
- Resseting password
- Creating projects
- Creating teams
- Creating events
- Joining events
- Registering members
- Editing a profile
- Maybe more

When you create forms in this way you can't have any state, so you are left
with a basic web-1.0 form with the only difference being that the form doesn't
have to call an API and can call a server action instead. 

This was a form that created a new project:

{% highlight jsx %}

// src/app/projects/new/page.js - commit a265d06
<form
    action={async (formData) => {
        "use server";

        const projectData = {
            name: formData.get("pname"),
            // and some more data...
        };
        const proj = await createProject(projectData);
        redirect("/projects");
    }}
>
    <input name="pname" />
    <!-- Some inputs are removed for simplicity -->
    <Button className="w-full md:w-64 mt-4" type="submit">
        Create new project
    </Button>
</form>

{% endhighlight %}

We reach a point where there are a couple of problems with this approach:

1. The user can **spam** the button, this can create a **lot of traffic** 
on the server
2. There is no way to display any indicators of sucess or failure since there 
is **no state**

## Solution to the forms problem

The solution to this was to ditch server components whenever I had to use the 
form element. I can just say that this was not a small endeavour 😀

This is how basically every component with forms looked like:

Firstly we have a server component that would look something like this

{% highlight jsx %}

// src/app/projects/new/page.js
export default async function NewProjectPage() {
	const session = await auth();
	if (!session || !session.user || session.user.role !== "board") {
		redirect("/login");
	}

	return <NewProjectForm />;
}

{% endhighlight %}

Secondly we have a client component that handles the state as well as the form
submission

{% highlight jsx %}

// src/app/projects/new/NewProjectForm.js - truncated for readability
export default function NewProjectForm() {
	const [formState, formAction] = useFormState(createNewProject, null);
	const [submitting, setSubmitting] = useState(false);
	const formRef = useRef(null);

	const handleButtonClick = () => {
		setSubmitting(true);
		formRef.current.requestSubmit();
	};
    
	useEffect(() => {
		if (formState?.success) {
            // Handle success
		} else if (formState?.error) {
            // Handle errors
		}
		setSubmitting(false);
	}, [formState]);

	return (
        <form
            ref={formRef}
            action={formAction}
        >
        <!-- Some inputs and a button... -->

{% endhighlight %}


## Problem with calculating member points

The other problem was calculating member points. I had imagined it going this
way:

- Every event entry gives you some amount of points
- Every day of you being in a team or project gives you some amount of points

The event entry was easy, when the member enters an event via code, just update
the database:

{% highlight js %}

const updatedEvent = await Event.findByIdAndUpdate(eventId, {
    $addToSet: { attendees: user.id },
});

{% endhighlight %}

But the problem was sending the query every day at the same time to check whether
the user is a part of some project or team. Since the whole platform is 
deployed on **Vercel**, everything is done via serverless functions. This means 
that I cannot just have a `setInterval` function run on a server - the server 
kinda doesn't exist ( _it does but you know what i mean_ ). 

**Vercel** supports **cron jobs** but they are not free. Since I had a budget 
for this project of around `$0` I had to find another way.

## The solution to the points problem

So the solution was actually quite simple in retrospective, but I had never
been in contact with cron jobs before this so be easy on me dude!

The solution was to have an api route that goes through all of the members and
calculates and updates their point count. This api would get hit every day at
midnight by **Github Actions** cron job.

The **API endpoint**:

{% highlight js %}

// src/app/api/members/calculate/route.js - truncated for visibility
export async function GET(req) {
	if (
		req.headers.get("Authorization") !== `Bearer ${process.env.CRON_SECRET}`
	) {
		return new Response("Unauthorized", {
			status: 401,
			headers: {
				"Access-Control-Allow-Origin": "*",
				"Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
				"Access-Control-Allow-Headers": "Content-Type, Authorization",
			},
		});
	}

	await dbConnect();
    // ... go and calculate points

{% endhighlight %}

As you can see, I added auth to this api via `CRON_SECRET` variable. This is so
the api doesn't get molested by bots or malicious users.

The secod part of this was the **Github Action**:

{% highlight yml %}

// .github/workflows/cron.yml
name: Score Calculation
on:
  schedule:
    - cron: '0 0 * * *' # Daily at midnight

jobs:
  calculate-points:
    runs-on: ubuntu-latest
    steps:
      - name: Trigger score calculation
        run: |
          curl -X GET "https://eestec-hub.vercel.app/api/members/calculate" \
          -H "Authorization: {% raw %}Bearer ${{ secrets.CRON_SECRET }}{% endraw %}"

{% endhighlight %}

Pretty simple right?

# The result

The result from all of that work is a feature rich application that is yet to
be tested in the real scenario, we are beta testing right now. Here is the user
flow:

## Authentication

All users must authenticate. The user can optionaly request an account via 
registering.

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-auth.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub auth showcase"/>
</p>

Optionaly, there is a password reset functionality if the user forgets their password.

## Projects

The `board members` have the ability to create new projects.

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-projects.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub projects showcase"/>
</p>

The `regular members` can then apply to these projects, while the `board members`
can later look at the applications

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-projects-2.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub projects showcase"/>
</p>

## Events

The `board members` can create events that the rest of the members can see in
their notifications.

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-events.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub events showcase"/>
</p>

The `regular members` can then attend an event by inputing a secret value that
the only `board members` have.

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-events-2.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub events showcase"/>
</p>

## Teams

`board members` create teams that rest of the members can join

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-teams.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub teams showcase"/>
</p>

## Member management

`board members` can manage the rest of the members by registering new users 

<p align="center">
    <img style="text: centered" src="{{"/assets/images/e-hub-members.gif" | prepend: site.url}}" width="600" alt="EESTEC Hub member management showcase"/>
</p>

# Future of development

The bigger part of the development is behind us. The thing that is left is 
writing tests and fixing buggs. I will leave this up to the team tho, as I have 
poured many lone hours into this project and kinda got burnet up by it.

# Tech stack

- [Next.js](https://nextjs.org/){:target="_blank"} - For basically everything
- [Vercel](https://vercel.com){:target="_blank"}  - For hosting
- [MongoDB](https://www.mongodb.com/){:target="_blank"}  - This is where we 
store `users`, `projects`, `events` and so on... Used 
[mongoose](https://mongoosejs.com/) for strong types
- [Atlas](https://cloud.mongodb.com){:target="_blank"}  - To host the database
- [Vercel blob](https://vercel.com/docs/vercel-blob){:target="_blank"}  - To 
store the user profile images 
- [Tailwindcss](https://tailwindcss.com/){:target="_blank"} - To take a rest 
from css and have an easy time with responsive design
- [jsdoc](https://jsdoc.app/){:target="_blank"}  - To help with types. Came in 
very clutch when the codebase started growing, very good for code completion
- [Github Actions](https://github.com/features/actions){:target="_blank"}  - 
For cron jobs
- [authjs](https://authjs.dev/){:target="_blank"}  - For user authentication

# Source code and contributing

The source for the project can be found on the 
[orgs github](https://github.com/EESTEC-LC-Novi-Sad/eestec-hub){:target="_blank"} 
. I welcome everyone and anyone to post an issue or a PR. 

If you are a part of another student organization and would want to 
incorporate this, but don't understand how to fork this repo, feed free to get 
in touch with me via [email](mailto:mihailonvojinovic@gmail.com)
