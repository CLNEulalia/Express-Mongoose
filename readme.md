# Express & Mongoose

## Learning Objectives
* Understand each part of the MVC architecture
* Explain the role each part of MVC plays in an application
* Identify how an Express app fits within the MVC framework
* Connect an Express app to a MongoDB database
* Introduce CRUD
* Implement CRUD functionality in an Express app using Mongoose

## Overview (5 min / 0:05) - Ali

This day-long workshop will get us familiar with the process of building web applications using Express, MongoDB and Mongoose and Handlebars. We'll start with high-level introductions to MVC and CRUD before learning how to connect Express to MongoDB using Mongoose and then building our first fully CRUD, MVC application.

So far in this unit you've learned about a number of tools - Node, Express, MongoDB and Mongoose - that developers can use to build a server-side JavaScript application. You have yet, however, to use them all together. We'll be spending the bulk of today's lesson connecting everything and creating an application that can receive HTTP requests, retrieve data / make changes in a database, and send information back to the end-user.

# Intro to MVC (5 min / 0:10) - James

Before we get in to learning how to build full stack web applications, we need to take a step back and introduce an important architectural pattern: MVC.

> What might we mean when we say "architectural pattern"?

When building an application, there is an important step that has to happen that is often described as *architecting* the app. This step encompasses things like deciding on a file structure, figuring out what entities your application will have and how they relate to each other, etc. So, we describe it as *architecting*.

Just like with buildings, there are common ways of architecting an application. This goes back to the idea of "Convention over Configuration" that we've talked about previously. Using a common convention for the architecture of our application lets us get started sooner and onboard other developers quickly.

The application architecture we're learning today is one of the most common. It's called MVC, which stands for **model**, **view**, **controller**.

## Why MVC? (5 min / 0:15) - Ali

What makes MVC so powerful as an architectural pattern is the idea of [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns).

<details>

<summary>What is Separation of Concerns?</summary>

For any given feature of an application, we'll have multiple things we need to do to build that feature out: persist the data for that feature, present the data for that feature, write some business logic to control how the feature works.

Each of these can be considered a separate **concern**: presentation, persistence, business logic. Separating these makes them easier to build, write, maintain or change.

If we want to change how we're presenting some data, we can do so by just changing the presentation and it wont affect the persistence or business logic.
</details>

## MVC In Action (20 min / 0:35) - James

Lets say we're building a hypothetical application for our client, Beyonce. This application will let her fans buy and sell second-hand merchandise. We'll call it eBey.

Specifically, we need to build out the part of the application that will let a user post a new item for sale. To do so, we need to know what data we have about a given item, how to present an item and how we can control the data and it's presentation through some business logic.

These map directly to the three parts of MVC:

1. Model - where we describe the data of a feature
2. View - where we present the data for a feature
3. Controller - where we compose our data and it's presentation through some business logic

For the feature we need to add to eBey, our Model, View and Controller would break down like this:

### The Model - Ali
The model is where we define the data for our feature. This is very closely related to Domain Modelling and ERDs.

If we were building an application for a library, we would have a model for a book, an employee, a member. Each of these would contain the definition of any attributes the entity has. For our eBey application, the model that we would need to define is for an Item that a user is selling - it may have a description, a seller, and a starting bid price.

### The View - James
The view is what the user sees and interacts with (the HTML and CSS that gets rendered in the browser).

Each Model can have a couple of different views. In MVC there are some conventions around common views:
  - a list/index view, where we show every instance of a model
  - a show view, where we see a single instance of a model
  - an edit view, where we can update a single instance of a model
  - a new view, where we can create a new instance of a model

In eBey, we need to build out a couple of views:
  - a view to show every item that is for sale (the list/index view)
  - a view for a single item for sale (the show view)
  - a view for a seller to edit an item they've posted for sale (an edit view)
  - a few for a seller to post a new item for sale (a new view)

### The Controller - Ali
The controller is where we knit our models and views together. It is where our business logic will live and we'll compose our views and models together.

In eBey, a user will see a new view that will display a form for them to fill out to post a new item for sale. That form will need to be processed - that's where our controller comes in. The controller will take that data and create a new Item from our Item model and save it to the database. Then, when another user wants to see that item, they'll visit the show view. When they do, the controller will use the model to pull the data for that specific item and render our show view.

## Turn And Talk (10 min / 0:45) - James

Turn to your neighbor(s) and review the following questions:

1. What is MVC? What does it stand for? What are the different parts of MVC?
2. For each part of MVC,
  - what is it's concern?
  - How does it interact with the other two parts?

## Break (10 min / 0:55)

# Express & MongoDB 

## We Do: [To Do](https://git.generalassemb.ly/ga-wdi-exercises/express-to-do) (5 min / 1:00) - Ali

Clone down the [Express To Do](https://git.generalassemb.ly/ga-wdi-exercises/express-to-do) repository. This is the application we'll be building together as we learn Express and Mongoose.

## Setup (10 min / 1:10)

Before we can get started on building the application, we need to get setup. Lets run `npm init -y` and then install `express`. We'll then scaffold out the folder structure for our application:

```sh
mkdir db models views controllers
```

The `db` folder will be where we configure our database connection. The `models`, `views` and `controllers` folders will map to the three parts of MVC: the models, views and controllers.

It's important to note that we are by no means required to follow any particular folder structure with Express. That is one of Express' strengths - it's really flexible and we can structure the application however we as developers see best. The folder structure we're following here is convenient, as it closely follows MVC.

## Mongoose (5 min / 1:15) - James

### Why are we using Mongoose?

[Mongoose](http://mongoosejs.com/) is an Object Document Mapper (ODM) - a tool we can use to map between native objects and documents stored inside a document store (like MongoDB). ODMs like Mongoose let us connect to a database and then interact with our database in a consistent and easy way. We could just use the MongoDB JavaScript library, but Mongoose gives us an easier way to interact with our database and work with the objects stored in it. 

In the next few sections, we'll use Mongoose to connect to our MongoDB database and query that database.

## Connect to Mongoose (20 min / 1:35)

In order for us to use Mongoose to communicate with our database, we need to link it up to our Express application. We'll do this by...

* Establishing a connection with a Mongo database.
* Define a Mongoose schema and model.

A schema is a description of how an object should be structure (a *schematic* for objects we want to use in our application and store in our database). A schema is defined as part of a **Model**: a representation of our data in our application (the **M** in **MVC**).

### Set up Connection to MongoDB

1. Install Mongoose via the command line: `npm install --save mongoose`.
2. create a `db/` directory with a `connection.js` file in it
3. In `connection.js`, require `'mongoose'` and save it to a `mongoose` variable.
4. Connect to our `todo` database using `mongoose.connect()`.
5. Set Mongoose's default Promise library to JavaScript's native `Promise`.
6. Export this connected version of `mongoose` via `module.exports`.

```js
// db/connection.js

const mongoose = require('mongoose')

mongoose.connect('mongodb://localhost/todo')

mongoose.Promise = Promise

module.exports = mongoose
```

> **`const mongoose = require("mongoose")`** - In order to reference Mongoose, we need to require its corresponding node module and save it in a variable we can reference later.  
>  
> **`mongoose.connect`** - We also need to link Mongoose to our `todo` Mongo database.  
>
> **`module.exports = mongoose`** - When this file (`connection.js`) is required in other files, it will evaluate to this *connected* version of `mongoose`.

### Defining a Mongoose Schema and Model - Ali

1. Create a new file in `db` directory called `schema.js`.
2. At the top of `schema.js`, require the `connection.js` file and save it to a `mongoose` variable.
3. Define a `TodoSchema` using mongoose's `.Schema()` method.
4. Define a `'Todo'` model built off `TodoSchema` with `mongoose.model()` and save it to a `Todo` variable.
5. Export the `Todo` model using `module.exports`.

```js
// db/schema.js

const mongoose = require("./connection");

const ToDoSchema = new mongoose.Schema({
  title: String,
  complete: Boolean
});

const Todo = mongoose.model("Todo", ToDoSchema);

module.exports = Todo;
```

> **`mongoose.Schema( )`** - We use Mongoose's schema method to define a blueprint for our Todo model (i.e. what attributes it will have and what data types they will be).  
>  
> **`mongoose.model( )`** - We attach our schema to our model by passing in two arguments to this method: (1) the desired name of our model ("Todo") and (2) the existing schema.  
>  
> **`module.exports = Todo`** - When this file (`schema.js`) is required in other files, it will evaluate to the `Todo` model defined here through which we will be able to query the `todo` collection in our Mongo database.

## We Do: Seed the Database (10 min / 1:45) - James

Mongoose is now connected to our Express application. Let's seed some data into our database using Mongoose.

### Set Up Seed File

Seeds allow us to quickly create dummy data. Why would we do that? In order to test out the interfaces and functionalities we build out, we need some content/data to manipulate in order to see how it looks and feels in our application.

1. Create a new `seeds.js` file in `db`.
2. In `seeds.js`, require the files containing our model definition and seedData:
    - Require the `schema.js` file and save it to a variable called `Todo`
    - Require the `seeds.json` file and save it to a variable called `seedData`
3. Write Mongoose queries that accomplish the following...
    - Clears the database of all data, and `.then`...
    - Inserts our seed data into the database, and `.then`...
    - Calls `process.exit()`.

> If you need help remembering Mongoose queries, [the official documentation is a good place to look](http://mongoosejs.com/docs/guide.html).

```js
const Todo = require("./schema");
const seedData = require("./seeds.json");

Todo.remove({})
  .then(() => {
    return Todo.collection.insert(seedData);
  })
  .then(() => {
    process.exit();
  });
```

> **`const Todo = require("./schema")`** - Because we defined our model in `schema.js` and set its `module.exports` to be equal to the `Todo` model, we can reference it like so.  
>  
> **`Todo.remove({})`** - This clears out the entire `todo` collection. We're not passing in any parameters, so Mongoose interprets this command as delete all documents in that collection!  
>  
> **`Todo.collection.insert(seedData)`** - Create a collection using the JSON contained in our seed file. Note that this is ideal for bulk insertion but **skips** schema validation. In our controller, where we will want validation, we will use `Todo.create()`.

### Running the Seed File

1. Run `$ node db/seed.js` in the Terminal.
2. Then run `$ mongo` in the Terminal and enter the following commands via the Mongo CLI interface...

```
> use todo
> db.todo.find()
```

## Aside: CRUD (5 min / 1:50) - Ali

CRUD is an acronym you'll hear a lot: it captures all of the operations we can perform on data in our application. CRUD stands for:

* Create - make a new instance of our data
* Read - view our data
* Update - edit an existing data instance
* Delete - remove an existing piece of data

As we're building out the routes for working with our data, we'll be building them to perform full CRUD. That means we'll have routes for creating, reading, updating and deleting data in our application.

## Aside REST(15 min / 2:05) - James

The web as we know it transfers data via HTTP, or Hyper-Text Transfer Protocol. HTTP specifies that a server receives a **request** from a client and then delivers a corresponding **response**.

Remember: a server's job is to respond to HTTP requests. In order to talk about how Express methods can respond to different HTTP verbs, we need to talk about how HTTP requests work.

Every HTTP request consists of a request **method** and **path**. The **path** is the part of the URL following the domain. We likely have noticed paths when navigating the web. What is the path for this lesson?

Your browser always sends that request in a *particular way* that gives the server a hint as to the purpose of the request. This *particular way* is the **method**.

**REST**, or REpresentational State Transfer, is a convention that standardizes how clients make requests to servers.  

Knowing REST is important because the vast majority of web developers have agreed to follow this same convention.

"GET" is one of these methods. It means the browser just wants to read (or "get") some information. When you write `app.get('/say_hi', (req, res) => {})`, you're telling your server how to respond when a browser says, "Hey, I'd like to get some information from the `/say_hi` path."

We make requests all the time -- especially `GET` requests. Every time you go to your browser, enter a URL, and hit enter, you're actually making a `GET` request.

### RESTful HTTP Methods - Ali

REST defines five main methods, each of which corresponds to one of the CRUD functionalities.

| Method | Crud functionality | DB Action |
| ----- |--------|----------------------   |
| GET   | read   | retrieve data           |
| POST  | create | add data                |
| PUT   | update | modify existing data    |
| PATCH | update | modify existing data    |
| DELETE| delete | delete existing data    |

So, wait -- there are 5 REST methods, but only 4 CRUD methods?

`PUT` and `PATCH` are both used for updating. Whenever you update your Facebook profile you're probably making a `PUT` or `PATCH` request. The difference is `PUT` would be intended to completely replace your profile, whereas `PATCH` would be intended to just change a few fields of your profile.

To clarify further, `PATCH` is replacing part of the data and `PUT` is replacing the whole thing.

### What's the difference at a technical level between a GET and a POST request? - James

There is of course the difference in the METHOD type, but also in the request payload. A `POST` request for instance will contain all of the data necessary for creating some new object.

GET is for when you want to read something. The parameters of the GET request are used for identifying which piece of data the client would like to read. The parameters of the POST request are used for defining a new piece of data.

### RESTful Routes - Ali

A **route** is a **method** plus a **path**...

**Method + Path = Route**

Each route results in an **action**.

REST can be translated in to RESTful Routes (routes that follow REST): 

| Method |      Path     |       Action |
| ------ | ------------- | ---------------------------------- |
|   GET  |  `/students`  | Read information about all students |
|  POST  |  `/students`  | Create a new student |
|   GET  | `/students/1` | Read information about the student whose ID is 1 |
|  PUT   | `/students/1` | Update the existing student whose ID is 1 with all new content |
|  PATCH | `/students/1` | Update the existing student whose ID is 1 with partially new content |
| DELETE | `/students/1` | Delete the existing student whose ID is 1 |

Note that the path doesn't contain any of the words describing the CRUD functionality that will be executed. That's the method's job.

These routes are important to keep in mind as we build out our controllers. For a resource with full CRUD, the controller for that resource will likely have each of the above 7 routes.

## Break (10 min / 2:15)

## Index Route (20 min / 2:25) - James

First order of business: display all the to dos stored in the database. We'll do this by adding code to the controller that:

* Retrieves all of the to dos from the database.
* Renders a view displaying the retrieved to dos.

In `controllers/todos.js`, let's add the following controller actions:

```js
router.get('/', (req, res) => {
  Todo.find({})
    .then(todos => {
      res.render('index', { todos });
    })
    .catch(err => console.log(err))
});
```

> **`Todo.find({})`** - Retrieves all todos in the database since we are not passing in any parameters to the method.  
>  
> **`.then(function(todos){ ... })`** - `todos` represents the all the Todos pulled from the database. We can then reference this inside of `.then`.  
>  
> **`res.render('index', { todos });`** - A little confusing, we're rendering our `index` view and passing in our `todos` from the database

The above controller action fetches all Todos from the database and renders them to an `index` view. We don't have an index view yet! 

```html
<!-- views/index.hbs -->
<h1>Hello</h1>

<ul>
  {{#each todos}}
    <li><a href="/{{this.id}}">{{this.title}}</a> - <a href="edit/{{this.id}}">Edit</a></li>
  {{/each}}
</ul>

<a href="/new">New</a>
```

Now, if we reload the page, we should see a list containing three todos. We'll be coming back to some of these.

## Show Route (20 min / 2:45) - Ali

If `/todos` renders a list of todos, then `/todos/1` renders a view showing the to do with an id of 1. Lets build that next:

```js
app.get('/:id', (req, res) => {
  Todo.findOne({ _id: req.params.id })
    .then(todo => {
      res.render('todos/show', todo);
    })
    .catch(err => console.log(err))
});
```

> **`app.get('/:id', (req, res) => {})`** `:id` is a *parameter*: a placeholder for a value. We can access the value through the `params` object inside of `req`: `req.params.id`
> **`Todo.findOne({ _id: req.params.id })`** There are two methods you'll see used to query the database, `find()` and `findOne()`. The first will run a query and return an array (always an array) of the results where as the second will run a query and return the first object that matches or `undefined`. The query we pass in is an object and we can query for any field with this object.
> **`res.render('todos/show', todo)`** This is how we render the show view for todos. This is the file at `views/todos/show.hbs`.

The above will attempt to render a `show` view, which we do not yet have. Let's build it out:

```html
<h2>{{title}}</h2>
```

This view will render the title as well as a link to edit and a form to delete

## Creating a new To Do (45 min / 3:30) - James

We want to be able to add new to do items to our list. Doing so will require submitting a form that will be processed by our server. We first need to render a view with the form, commonly at `/{resource}/new`:

```js
app.get('/new', (req, res) => {
  res.render('todos/new');
});
```

### Forms & `body-parser` - James

In Express, in order to process user input received through a form we will need to install and implement the `body-parser` middleware.  

Install it via the command line -- `npm install --save body-parser` -- then make the following changes to your `index.js` file:

```js
// index.js

const parser = require('body-parser')

// ...

app.use(parser.urlencoded({ extended: true }))
```

> **`const parser = require("body-parser")`** - Require `body-parser` so we can reference it later.  
>  
> **`app.use(parser.urlencoded({extended: true}))`** - configure the parser to support html forms (access to the body of the request)

`body-parser` will parse the body of an incoming request, making it available to us as part of the `request` object. The body is the payload of the request: the data for a new item, for instance. Now that `body-parser` is set up, we can write up our view:

### View - Ali

```html
<h2>New To Do:</h2>

<form action="/" method="post" accept-charset="utf-8">
  <p>
    <label>Title:</label>
    <input type="text" value="" name="title" id=""/>
  </p>
  <input type="submit" value="Create" />
</form>
```

The body of a request is an object with key-value pairs. The key is determined by the `name` attribute and the value is determined by the `value` attribute. So when the above form is submitted, the title for our new to do will be stored at `req.body.title`.

A common convention is to nest data inside of another sub-object. Doing so just requires modifying the `name` attribute to `name="todo[title]"`. If we do that, then the title of our new to do will be stored at `req.body.todo.title`.

A user is going to fill out this form and then hit submit. We will need a route in our controller to handle that:

```js
app.post('/', (req, res) => {
  Todo.create(req.body)
    .then(todo => {
      res.redirect('/');
    })
});
```

> **`app.post('/', (req, res)`** - listen for `post` requests to `/todos`.
> **`Todo.create(req.body)`** - create a new to do in the database
> **`res.redirect('/')`** - redirect back to the root route

## Lunch

## Review (10 min / 0:10)

* What is MVC? What are the 3 parts of MVC and how do they relate to each other?
* What is CRUD?
* What is REST? What is RESTful routing? What is a resource?
* What is a route? What are the HTTP methods (verbs)?

## Updating a To do (20 min / 0:30)

Updating a to do has two steps: first, we need to get the to do that the user wants to update and render a form to update that to do, typically called the `edit` view; second, when the user submits that form, we update that to do in the database. For updating data in REST, we use `PUT` and `PATCH`, here we'll use `PUT`. There's just one small problem: `PUT`, `PATCH`, and `DELETE` are all part of REST and HTTP - they are **not** part of HTML. That means, we can't make the `method` attribute of a form equal and of those three methods.

To get around this, the team behind Express built the [method-override](https://github.com/expressjs/method-override) package, which we can download with NPM. Method-override overrides the form method, so we can set the method equal to `POST` and add a query parameter to the action to tell Express what HTTP verb we actually want to use. 

Inside of `index.js`, we need to include and configure `method-override`:

```js
// index.js

const methodOverride = require('method-override')

// ...

app.use(methodOverride('_method'))
```

Now we can create a form inside of `views/todos/edit.hbs`:

```html
<h2>Edit To Do:</h2>

<form action="/{{this.id}}?_method=put" method="post">
  <p>
    <label>Title:</label>
    <input type="text" value="{{this.title}}" name="title"/>
  </p>
  <p>
    <label>Complete:</label>
    {{#if complete}}
      <input type="checkbox" name="complete" checked/>
    {{else}}
      <input type="checkbox" name="complete"/>
    {{/if}}
  </p>
  <input type="submit" value="Update" />
</form>
```

When we submit the above form, we'll make a `POST` request, Express will see the `?_method=put` part of the action and redirect this to our `PUT` route, which we need to define:

```js
router.get('/edit/:id', (req, res) => {
  Todo.findOne({_id: req.params.id})
    .then(todo => {
      res.render("todos/edit", todo);
    })
});

router.put('/:id', (req, res) => {
  Todo.findOneAndUpdate({_id: req.params.id}, req.body, { new: true })
    .then(todo => {
      res.redirect('/')
    })
});
```

The first route above queries the database using `.findOne()` to find a record with the same `_id` as the id passed in via params. We then render that to do to the user. When the user submits the form in the edit view, we use the `findOneAndUpdate()` method to find and update a record in a single operation.

## Delete a To Do (20 min / 0:50)

We're almost there! Last bit of CRUD functionality we need to implement is `DELETE`. Let's start by adding a second form with a delete button to our show view:

```html
<form action="/{{this.id}}?_method=delete" method="post">
  <input type="submit" value="Remove">
</form>
```

We're using `method-override` again to tell Express we actually want to perform a `DELETE` action.

When a user wants to delete a record, we can use the `.findOneAndRemove()` to find and remove that record in a single operation:

```js
app.delete('/:id', (req, res) => {
  Todo.findOneAndRemove({ _id: req.params.id })
    .then(() => {
      res.redirect('/')
    })
});
```

And that's it! We now have full CRUD and are following both MVC and REST!

## Break (10 min / 0:60)

## We Do: Guestbook (90 min / 2:30)

For the remainder of class, lets build an application together that will serve as a guest book. Our guest book application will have one resource: entries. An entry has the following properties:

| Title | Type | Description |
| --- | --- | --- |
| author | String | The author of the entry |
| body | String | The body of the entry |

We want to perform full CRUD on our entry resource. We also want to follow RESTful routing and MVC.

## Closing / Questions

* What is MVC? What are the different parts of MVC? How do they relate?
* What is CRUD?
* What is REST? What is RESTful Routing? Using a resource, draw out a table of RESTful routes

## Homework

Your homework assignment is to build [YUM](https://git.generalassemb.ly/ga-wdi-exercises/yum#part-ii-add-express).

## Resources

* [Using ES6 Promises Instead of Callbacks for Mongoose Queries](http://erikaybar.name/using-es6-promises-with-mongoosejs-queries/)
* [How to Write Middleware for Express Apps](https://stormpath.com/blog/how-to-write-middleware-for-express-apps)
* [Method Override](https://github.com/expressjs/method-override)

## Bonus Material

### Query Parameters (20 / 3:25)

So far we've done all of our passing-of-data through the route's path, as path
parameters. This hasn't been a problem since the data has been tiny -- a user
ID, or a name.

But when data starts getting bigger, this'll start getting a little crazy. We'd
need to be going to some enormous URLs.

REST gives us a couple different ways of sending data. Path params are the most
upfront and basic. Slightly more advanced are GET parameters, or query
parameters.

#### We do: Demo with Twitter

To demonstrate, let's check out Twitter's search functionality.

`https://twitter.com/search`

I'm going to search for "toast". This takes me to a new URL...

`https://twitter.com/search?q=toast&src=typd`

Check out the stuff at the end of the URL. I've learned that `src` points to the search of the query in this case, I typed it in hence the `typd`. I also recognize "toast", which is in `q=toast`.

If I add `+strudel` to the end of `toast` and go to the new URL...

`https://twitter.com/search?q=toast+strudel&src=typd`

I can see that the search box now has the words "toast" and "strudel", and all
the displayed Tweets contain those words.

#### Query strings

This is a **query string**. It's a string of key/value pairs at the end of a URL
that pass additional data to the server.

Query strings always follow the same format...

```
[some URL]?[key_one]=[value_one]&[key_two]=[value_two]&[key_three]=[value_three]...
```

First you have the URL, like `http://google.com` or `http://twitter.com/search` or `localhost:4567/hello`.

Then, you have a single question mark `?` that indicates the beginning of the
query string.

Then, you have any number of pairs of `key=value`. Each pair is separated by an
ampersand `&`.

> Note: There is only **one** question mark in your query string! By convention, it separates the rest of the URL from the query string.

##### Looking back at the Twitter URL, with `q=toast+strudel`, what does `q` stands for?

#### URL Encoding

URLs aren't supposed to have spaces in them, and some characters are reserved. You can't just put a forward-slash `/` in a query string because your browser's going to think that you mean a directory. Any `&` in a query string is going to be interpreted as something separating key-value pairs.

So instead we encode "special characters" in a special way (*not* to be confused
with HTML's way of encoding special characters).

For example, spaces are replaced either with a plus `+`, or they are **percent-encoded** with a special number. Space is `%20`.

You'll notice that these three links go to the same place...

`https://twitter.com/search?q=toast%20strudel&src=typd`

`https://twitter.com/search?q=toast+strudel&src=typd`

`https://twitter.com/search?q=toast strudel&src=typd`

In fact, in the last one, my browser replaced the space with `%20` itself!

[You can checkout this site to URL-encode and URL-decode stuff](http://meyerweb.com/eric/tools/dencoder/)

