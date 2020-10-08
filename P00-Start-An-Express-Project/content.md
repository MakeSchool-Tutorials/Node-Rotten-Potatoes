---
title: "Making A Plan & Starting an Express.js Project"
slug: start-an-express-project
---

You might know the website [Rotten Tomatoes](https://rottentomatoes.com) it is a site where people can review movies. In this tutorial you are going to build Rotten Potatoes - your very own movie review website.

# Why is this important?

By finishing this tutorial you will continue to deepen your knowledge of Node.js and Express.js as well as main the internet-wide paradigms of RESTful and Resourceful routing. You will be Creating, Reading, Updating, and Deleting (CRUD) a single resource (in this case, a `Review`). You will also learn how to use a MongoDB document-based database with Express.js.

# Learning Outcomes

By the end of this tutorial, you should be able to...

1. Build a web app using Node.js, Express.js, and Handlebars
1. Utilize a NoSQL database (MongoDB) for your web app
1. Integrate RESTful and Resourceful routing in your web app
1. CRUD a single resource
1. Understand how to use Bootstrap for basic styling

# How to Plan a Coding Project: User Stories

Software development these days is usually organized into **Agile Sprints** that are usually two weeks long. You'll notice evidence of this if you ever update your apps and read the update text. Sometimes it just says:

```
We ship a new version every two weeks...
```

That's because they are organizing their engineering in this standard agile way.

During each sprint developers pick a few **User Stories** to build, test, and ship to production. A **User Story** is one chunk of functionality. Engineers use the term **User Story** instead of "feature" to emphasize the user's experience in their design, development and testing. This emphasis on the user is called **User Centered Development**. Whenever you build anything on a team or by yourself, always use a **Backlog** of **User Stories** to organize and prioritize what you will build and when.

It is a good habit to build applications one resource at a time, adding all the **Resourceful Routes** for each resource before moving to the next.

So the user stories we can have for this review app will be as follows:

1. Users can view all reviews (index)
1. Users can create a review (new/create)
1. Users can view one review (show)
1. Users can delete a review (destroy)
1. Users can edit a review (edit/update)

Once we have this single Review resource build, we can move onto making an associated Comment resource.

1. Users can comment on reviews (comments#create)
1. Users can delete comments (comments#destroy)

As we finish and test user stories we'll be committing to github 🐙.

# Getting Started - Node.JS and `npm`

Now that we have some user stories, let's initialize an Express.js project so we can start on the first user story in the next chapter of this tutorial.

We're gonna jump right in to starting the new review and provide more context as new topics and concepts come up.

For reference for the rest of this chapter you can look at [ExpressJS's Getting Started](https://expressjs.com/en/starter/installing.html) docs

Open your computer's terminal and then...

# Install Homebrew

If you don't already have Homebrew installed, Mac's package manager, install that first and then install NodeJS. NodeJS's package manager `npm` is installed automatically when you install node, so we'll be able to install node modules from our command line once we install node.

> [info]
> Whenever you see the `$` in a command, that means it should be called in your computer's terminal. Remember: Don't include the `$` in your command.

<!-- -->

> [action]
>
> Follow the [install instructions for Homebrew](https://brew.sh/). Once that's installed, use it to install node:
>
```bash
$ brew install node
```

# Starting a Node.js & Express.js Project

Now that we have Node installed (and therefore have `npm`), now we can use npm to initialize a Node project using the command `npm init` which means "npm initialize".

Make a new directory called 'rotten-potatoes', then navigate into that directory, and finally initialized a new npm project in that directory. The `npm init` function will prompt you to define the configuration options that will be recorded in a file called `package.json`. Just hit enter for each option to select the default choice.

> [action]
>
> Use the terminal commands below to execute the above instructions:
>
```bash
$ mkdir rotten-potatoes
$ cd rotten-potatoes
$ npm init
# (hit enter for each option it asks for to select the default choice)
```

Now if you open your project in Atom, you'll see the `package.json` which records the npm configuration you define upon initialization of the project.

# Adding Express.js

Now we need to add just a "Hello World" rendered with [Express.js](https://expressjs.com/) and then add the template engine we'll be using called [Handlebars](https://handlebarsjs.com/). Express.js is template engine-agnostic, meaning we could use all sorts of templating engines. We're going to use Handlebars so we get two key features:

1. A **layout template**, which will make organizing our templates easier
1. The ability to write in **real HTML** — with some templating engines, you write in a custom version of HTML to simplify the process.  For our purposes though, we want to practice writing real HTML.

Our main file of our whole application we'll call `app.js`

> [action]
> First install Express.js and then create the main file using the "touch" command.
>
```bash
$ npm install express --save
$ touch app.js
```
>

Now your project should have two files and a folder: `app.js`, `package.json`, and `node_modules`.

> [action]
> Now open your project's code base using the Atom text editor by typing:
```bash
$ atom .
```
>

And let's add some standard Express.js code to `app.js` to show a hello world.

> [action]
>
> Add the following to `app.js`:
>
```js
const express = require('express')
const app = express()
>
app.get('/', (req, res) => {
  res.send('Hello World!')
})
>
app.listen(3000, () => {
  console.log('App listening on port 3000!')
})
```

At this point, we could run our project with `node app.js` and we would see "Hello World". Remember that we still haven't added a template engine yet! We are just sending text back to the browser.

# Install nodemon and launch your server

Let's install `nodemon` if you haven't already. Make sure you've installed `nodemon` and run your server by running `nodemon`. [Nodemon](https://nodemon.io/) just helps us by restarting our server every time we change our code.

> [action]
>
> Install `nodemon` and then have it start our app:
>
```bash
$ npm install nodemon -g
$ nodemon app.js
```

In your terminal you should see "App listening on port 3000!" output in your terminal from the `console.log` inside of `app.listen(3000 ...)`. Now on your browser at `http://localhost:3000` you should see "Hello World".

Hello there code!

>[info]
> If you want to start your server by just entering `nodemon`, then edit the `main` option in `package.json` file to `app.js` instead of `index.js`

# Add Handlebars.js as a Templating Engine

Now, we are going to need to render HTML for our project, so we have to add a **Templating Engine** if we want extend Express.js to use HTML.

Let's add [Handlebars.js](https://handlebarsjs.com/) as our templating engine so our Express.js server can render templates. This is called _server-side HTML templates_.

> [action]
> Install Handlebars.js to your project using the `express-handlebars` node module.
>
```bash
$ npm install express-handlebars --save
```

Now that we've installed the package, we must require it or **initialize** it in your app and also, let's set the `defaultLayout` to `main`.

> [action]
>
> Add the following to `app.js`
>
```js
// app.js
var exphbs = require('express-handlebars');
>
app.engine('handlebars', exphbs({ defaultLayout: 'main' }));
app.set('view engine', 'handlebars');
```
>
> Now extend your **root route** ('/') to render `home.handlebars`.
>
```js
// app.js
>
app.get('/', (req, res) => {
  res.render('home', { msg: 'Handlebars are Cool!' });
})
```

Refresh your browser now and _read the error you get carefully._ This error tells us that our application can't find a `home.handlebars` template yet (because we haven't made it!).

> [info]
> It is useful to try to predict what error you might get as you are coding, and see if you get the error you expected. Errors can be a great way to check your work as you go and not go too far before checking your work.

<!-- -->

> [action]
> Create the `views`, `layouts` folders and `main.handlebars` and `home.handlebars` files.
>
```bash
$ mkdir views
$ mkdir views/layouts
$ touch views/layouts/main.handlebars
```

So now we have our **views** folder setup with a **layout template** called `main`. Great!

Now we'll add some boilerplate code to the `main.handlebars` layout template. Remember that we used Handlebars because it has a **Layout Template**. A **layout template** is a super-template that all other templates will inherit from. Having a layout template means we can put things that go on *every* page in just one file of code. These templates will be threaded in right at the `{{{body}}}`.

> [action]
>
> Add the following into `views/layouts/main.handlebars`:
>
```html
<!-- views/layouts/main.handlebars -->
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Rotten Potatoes</title>
</head>
<body>
>
  {{{body}}}
>
</body>
</html>
```
>
> Update `views/home.handlebars` to the following so that it will render the message from our root route:
>
```html
<!-- home.handlebars -->
<h1>{{msg}}</h1>
```

Now when you visit `localhost:3000` you now should see "Handlebars are Cool!" inside an `h1` tag.

# Initialize, Commit, and Push

Let's start by installing git if you don't already have it installed. And then initializing our project folder as a git repository. Then we can check the git status. We should see all our files as uncommitted and unstaged to commit.

> [action]
>
> Install git if you haven't, and then initialize it:
>
```bash
$ brew install git
$ git init
$ git status
```

Now we can stage all the files to commit, then commit them adding a commit message, and then double check our status. We should see we have no files to commit because we just committed them all.

> [action]
>
> Add and commit what we have so far:
>
```bash
$ git add .
$ git commit -m 'init'
$ git status
```
>
> Now go to github and create a public repository called `Rotten-Potatoes-Tutorial`.
>
> Once you've done that, associate it as a remote for your local git project and then push to it:
>
```bash
$ git remote add origin GITHUB-REPO-URL
$ git push origin main -u
```
