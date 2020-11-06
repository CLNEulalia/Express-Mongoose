# Express & Mongoose

## Learning Objectives

- Understand each part of the MVC architecture
- Explain the role each part of MVC plays in an application
- Identify how an Express app fits within the MVC framework
- Connect an Express app to a MongoDB database
- Introduce CRUD
- Implement CRUD functionality in an Express app using Mongoose

## Framing

Today we'll combine what we've learned about Node, Mongo, Mongoose and Express to build an application that allows us to create, read, update and delete todos in our database through a web UI!

# Intro to MVC

Before we get in to learning how to build full stack web applications, we need to take a step back and introduce an important architectural pattern: MVC.

When building an application, one important early step is _architecting_ the app. It encompasses things like deciding on a file structure, figuring out what entities your application will have and how they relate to each other, etc. Using a common convention for the architecture of our application lets us get started more quickly and onboard other developers more easily. Another benefit is that it eliminates the need expend any time or effort on solving problems that have already been solved by others!

MVC, which stands for **model**, **view**, **controller**, is one of the most — if not the most — common application architectures.

## Separation of Concerns

What makes MVC so powerful as an architectural pattern is the idea of [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns).

For any given feature of an application, we'll have multiple things we need to do to build that feature: persist the data for that feature, present the data for that feature, write some business logic to control how the feature works.

Each of these can be considered a separate **concern**: presentation, persistence, business logic. Separating these makes them easier to build, write, maintain or change. For example, if we want to change how we're presenting some data, we can do so by just changing the presentation part of our app without affecting the persistence or business logic.

![image](https://media.git.generalassemb.ly/user/17300/files/32cdfa00-dbd8-11ea-915f-448ec99ed0d7)

In terms of MVC, we can roughly correlate:

- **Model** to data
- **View** to user interface
- **Controller** to the business logic inside the callback for each of our routes

# Express & MongoDB

## Setup

1. Create a directory for our project in our `sandbox` with `mkdir express-mvc`.
1. Change into the directory with `cd express-mvc`.
1. Run `npm init -y`.
1. Install express, handlebars, mongoose, and nodemon with `npm i express mongoose hbs nodemon`.
1. Create the base files in the project root with `touch index.js .gitignore`.
1. Scaffold out the folder structure for our application:

```sh
mkdir db models views controllers
```

The `db` folder will be where we configure our database connection. The `models`, `views` and `controllers` folders will map to the three parts of MVC: the models, views and controllers.

It's important to note that we are by no means required to follow any particular folder structure with Express. Express is really flexible, or unopinionated, allowing us to structure our application however we wish. The folder structure we're following here closely follows MVC, but there are many different structures and approaches you'll encounter in the wild.

Your project should look like this inside your code editor now:

<!-- prettier-ignore -->
```
express-mvc
  ├── controllers
  ├── db
  ├── models
  ├── node_modules
  ├── views
  .gitignore
  index.js
  package.json
  package-lock.json
```

Open `.gitignore` and add `node_modules` to it.

## Mongoose

### Why are we using Mongoose?

[Mongoose](http://mongoosejs.com/) is an Object Document Mapper (ODM) - a tool we can use to map between native objects and documents stored inside a document store (like MongoDB). ODMs like Mongoose let us connect to a database and then interact with our database in a consistent and easy way. We could just use the MongoDB JavaScript library, but Mongoose gives us an easier way to interact with our database and work with the objects stored in it.

In the next few sections, we'll use Mongoose to connect to our MongoDB database and query that database.

## Connect to Mongoose

In order for us to use Mongoose to communicate with our database, we need to link it up to our Express application. We'll do this by:

- Establishing a connection with a Mongo database.
- Define a Mongoose schema and model.

A schema is a description of how an object should be structure (a _schematic_ for objects we want to use in our application and store in our database). A schema is defined as part of a **Model**: a representation of our data in our application (the **M** in **MVC**).

### Set up Connection to MongoDB

1. Make sure Mongoose is installed (look under the dependencies in the `package.json` file) and if not, install it via the command line with: `npm i mongoose`.
1. Create a `connection.js` file in the `db/` directory.
1. Add the following code to the `connection.js` file:

```js
// db/connection.js
// Require Mongoose:
const mongoose = require('mongoose');

// Store the URI for our database in a variable.
// When we're working locally, we'll have a local DB,
// but in production, we'll have to have a database
// that's connected to the Internet.
const mongoURI =
  process.env.NODE_ENV === 'production'
    ? process.env.DB_URL
    : 'mongodb://localhost/express-mvc';

// Use the mongoose connect method to connect to the
// database.  The connect method takes two arguments:
// the address of the database and an object containing
// any options.
mongoose
  .connect(mongoURI, {
    useNewUrlParser: true,
    useCreateIndex: true,
    useUnifiedTopology: true,
    useFindAndModify: false,
  })
  // The connect method is asynchronous, so we can use
  // .then/.catch to run callback functions
  // when the connection is opened or errors out.
  .then((instance) =>
    console.log(`Connected to db: ${instance.connections[0].name}`)
  )
  .catch((error) => console.log('Connection failed!', error));

// Export mongoose so we can use it elsewhere
module.exports = mongoose;
```

> **`const mongoose = require('mongoose')`** - To use Mongoose, we require its corresponding node module and save it in a variable we can reference later.
>
> **`mongoose.connect`** - To link Mongoose to our `express-mvc` Mongo database, we'll use the `mongoose.connect()` method and pass it the address of the database.
>
> **`module.exports = mongoose`** - When `connection.js` file is required in other files, it will evaluate to this _connected_ version of `mongoose`.

### Defining a Mongoose Schema and Model

1. Create a new file in `models` directory called `todo-model.js`.
1. Create a schema and model for our todos:

```js
// models/todo-model.js

const mongoose = require('../db/connection');

const ToDoSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  complete: {
    type: Boolean,
    default: false,
  },
});

// Make sure to name the model with the singular Todo!
// Mongoose pluralizes and lowercases the name of the model
// to name the collection of documents in the database that
// correspond to this model.
const Todo = mongoose.model('Todo', ToDoSchema);

module.exports = Todo;
```

> **`mongoose.Schema( )`** - The schema method creates a blueprint for our Todo model describing tthe attributes it will have and what data types they will be.
>
> **`mongoose.model( )`** - We attach our schema to our model by passing in two arguments to this method: (1) the desired name of our model ("Todo") and (2) the existing schema.
>
> **`module.exports = Todo`** - When this file (`todo-model.js`) is required in other files, it will evaluate to the `Todo` model defined here through which we will be able to query the `todo` collection in our Mongo database.

## We Do: Seed the Database

With our database connection established, let's seed some data into our database using Mongoose.

Seeds allow us to quickly create dummy data. Why would we do that? In order to test out the interfaces and functionalities we build out, we need some content/data to manipulate in order to see how it looks and feels in our application.

### Set Up Seed Data

1. Create a new `todo-seeds.json` file in `db`. 
1. Add the data from the json [file in this repo](https://git.generalassemb.ly/seir-622/express-mongoose/blob/master/seeds.json). 

### Set Up Seed File

1. Create a new `todo-seeds.js` file in `db`.
1. Add the following code:

```js
// Require the model which has a connection to the database
const Todo = require('../models/todo-model');
// Require a json file which contains some dummy data
const seedData = require('./todo-seeds.json');

// Remove any preexisting data
Todo.remove({})
  .then(() => {
    // Insert the dummy data
    return Todo.collection.insert(seedData);
  })
  .then(() => {
    // Close the connection to Mongo
    process.exit();
  });
```

> **`const Todo = require('../models/todo-model')`** - Bring in our model that defines the structure for our data and provides a connection to the database.
>
> **`Todo.remove({})`** - This clears out the entire `todo` collection. The empty query (curly braces) works the same as when passed to `.find({})` **matching every document** in the database collection.
>
> **`Todo.collection.insert(seedData)`** - Create a collection using the JSON contained in our seed file. Note that this is ideal for bulk insertion but **skips** schema validation. In our controller, where we will want validation, we will use `Todo.create()`.

### Running the Seed File

1. Make sure you're in the `express-mvc` directory.
1. Run `node ./db/todo-seeds.js` in the Terminal.
1. Then run `mongo` in the Terminal and enter the following commands via the Mongo CLI:

```
> use express-mvc
> db.todos.find()
```

## Know the Lingo: CRUD

CRUD is an acronym you'll hear a lot: it captures all of the operations we can perform on data in our application. CRUD stands for:

- **Create** - make a new instance of our data
- **Read** - view our data
- **Update** - edit an existing data instance
- **Delete** - remove an existing piece of data

As we're building out the routes for working with our data, we'll be building them to perform full CRUD. That means we'll have routes for creating, reading, updating and deleting data in our application.

## Know the lingo: REST

We've learned that the Web transfers data via HTTP, and that HTTP specifies that a server receives a **request** from a client and then delivers a corresponding **response**.

Every HTTP request consists of a request **method** and **path**. The **path** is the part of the URL following the domain. It's the _"address"_ for the data or file we want. The **method** is the operation to perform, such _get_ the data or _delete_ the data at that address.

**REST**, or REpresentational State Transfer, standardizes the conventions we use when the combining these methods and paths to perform specific actions. While REST isn't a protocol like HTTP, the vast majority of web developers have agreed to follow this same convention.

## HTTP Methods for RESTful Services

HTTP defines a set of request methods (also called HTTP verbs) to indicate the desired action to be performed for a given resource. Of these methods, **5** correspond to CRUD functionalities. They are:

| HTTP Method | Crud functionality | DB Action            |
| ----------- | ------------------ | -------------------- |
| GET         | read               | retrieve data        |
| POST        | create             | add data             |
| PUT         | update             | modify existing data |
| PATCH       | update             | modify existing data |
| DELETE      | delete             | delete existing data |

So, wait -- there are 5 HTTP methods, but only 4 CRUD methods?

`PUT` and `PATCH` are both used for updating. The difference is that `PUT` replaces an entire database record/document, whereas `PATCH` replaces some of the data in the record/document.

## RESTful Routes

A **route** is a **method** plus a **path**...

**Method + Path = Route**

Each route results in an **action**. Routes that follow REST conventions are known as RESTful routes:

![image](https://media.git.generalassemb.ly/user/17300/files/96870180-db9d-11ea-83ce-efe6b7517ec5)

We'll refer back to these routes at each step as we build out our controllers. For a resource with full CRUD, the controller for that resource will likely have each of the above 7 routes.

## Build A Server

First order of business: Build a server in `index.js`! Our server needs a few basic components to make it ready to listen for requests:

```js
// index.js
// Require express
const express = require('express');
// Use express to instantiate our app
const app = express();

/* START ROUTE CONTROLLERS */
// Require our ToDo model
const Todo = require('./models/todo-model.js');

/* END ROUTE CONTROLLERS */

// Create a variable for our port
const port = process.env.PORT || 4000;

// Run our server!
app.listen(port, () => {
  console.log(`Express MVC app is running on port ${port}`);
});
```

> The port where we'll run our server might be different in production versus locally on our machine. Having the ability to use an environment variable to control this is a convenience that will serve us later if we choose to deploy this application.

## Index Route

Now that we have a basic server running, let's create an index route that displays all the to dos stored in the database.

#### Question: What are the two component (path and method) parts of the index route?

In our `index.js`, add a controller for the index route:

```js
app.get('/todos', (req, res) => {
  Todo.find({})
    .then((todos) => {
      res.render('todos/index', { todos });
    })
    .catch(console.error);
});
```

> **`Todo.find({})`** - Retrieves all todos in the database since we are not passing in any parameters to the method.
>
> **`.then(function(todos){ ... })`** - `todos` represents the all the Todos pulled from the database. We can then reference this inside of `.then`.
>
> **`res.render('todos/index', { todos });`** - A little confusing, we're rendering our `index` view and passing in our `todos` from the database

The above controller action fetches all Todos from the database and renders them to an `index` view. We don't have an index view yet! Create a new file in the `views` directory called `layout.hbs` with the base html for our app:

```hbs
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!--Adding Bootstrap so we can make it pretty-->
    <link
      rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"
      integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z"
      crossorigin="anonymous"
    />
    <title>Express MVC App</title>
  </head>

  <body class="container">
    {{{body}}}
  </body>
</html>
```

Now create a `todos` folder inside of the `views` directory. Inside the `todos` folder, create a file called `index.hbs`. This is where we'll create the template for our homepage view with all of our todo items.

We'll use the [each helper](https://handlebarsjs.com/guide/builtin-helpers.html#each) in Handlebars to iterate over the array of todos in the object we've passed to the render method and output a list item for each todo object it contains. To make it look nice we'll use the Bootstrap `list-group` and `list-group-item` classes!

```hbs
<!-- views/todos/index.hbs -->
<h1>Todos</h1>
<ul class="list-group">
  {{#each todos}}
  <li class="list-group-item d-flex justify-content-between">
    <div>{{this.title}}</div>
    <div class="links">
      <!-- for actions later -->
    </div>
  </li>
  {{/each}}
</ul>
```

At this point our page will almost be ready to render our components. However, we need to set Handlebars as our view engine by adding one more line to our `index.js` below the instantiation of our Express app:

```js
// index.js
// Require express
const express = require('express');
// Use express to instantiate our app
const app = express();
// Set Handlebars (hbs) as the view engine of our app
app.set('view engine', 'hbs');
```

Now, if we reload the page, we should see a list containing three todos. We'll be coming back to these shortly.

## Show Route

Next, we'll want to build out the details view of our todos, so we'll need to create a show route controller.

#### Question: What are the two component parts of the show route?

```js
app.get('/todos/:id', (req, res) => {
  Todo.findById(req.params.id)
    .then((todo) => {
      res.render('todos/show', todo);
    })
    .catch(console.error);
});
```

> **`router.get('/:id', (req, res) => {})`** `:id` is a _parameter_: a placeholder for a value. We can access the value through the `params` object inside of `req`: `req.params.id`
>
> **`Todo.findById(req.params.id)`** There are several methods you'll see used to query the database, `find()`, `findById()` and `findOne()` are all very common. The `.findById()` method expects us to pass it an id as an argument instead of a query object such as: `.find({ _id: req.params.id })`.
>
> **`res.render('show', todo)`** This is how we render the show view for todos. This is the file that we must create at `views/show.hbs`.

The above will attempt to render a `show` view, which we do not yet have. Here we'll make use of the [if helper](https://handlebarsjs.com/guide/builtin-helpers.html#if) in Handlebars to display the complete or incomplete status. Lastly, we'll add a link that will take us back to the index route and style it to look like a button using Bootstrap classes.

```hbs
<!-- views/todos/show.hbs -->
<h1>{{title}}</h1>
{{#if complete}}
<p>Status: complete</p>
{{else}}
<p>Status: incomplete</p>
{{/if}}
<a href="/todos" class="btn btn-secondary">&larr; Back</a>
```

Cool... but we need to update the `index.hbs` so there's a link to each of the todo 'show' routes. Once again, we'll use some Bootstrap classes to make these links look great!

```hbs
<!--insert this anchor tag into the div with a class of 'links'-->
<a href="/todos/{{this.id}}" class="btn btn-info">View</a>
```

## Setting Up A Controller File

Our app is working pretty well, but the code in our `index.js` is already getting long. If we keep adding our routes to this file we'll have seven of them for each resource! Let's fix this before it gets out of control. We'll start by creating a new file in the `controllers` directory called `todos.js` and add the following code to the top of the file:

```js
const express = require('express');
const router = express.Router();
```

Next, go into your `index.js` and select all of the code inside the controller START and END comment tags and cut them with <kbd>⌘</kbd> + <kbd>X</kbd> (command + X or ctrl + X on Windows) and then paste them into your `controllers/todos.js` file below the router variable with <kbd>⌘</kbd> + <kbd>V</kbd>:

```js
// Require our Todo model (note the change in file path now that we are in the controllers folder)
const Todo = require('../models/todo-model.js');

app.get('/todos', (req, res) => {
  Todo.find({})
    .then((todos) => {
      res.render('todos/index', { todos });
    })
    .catch(console.error);
});

app.get('/todos/:id', (req, res) => {
  Todo.findById(req.params.id)
    .then((todo) => {
      res.render('todos/show', todo);
    })
    .catch(console.error);
});
```

Now we need to change `app.get` to `router.get` in **both** routes and add `module.exports = router` as the **last line** of the file.

Lastly, we'll go back to the `index.js` and require and use our new controller! Make sure to add the following lines inside the controller START and END comments exactly in the same place where the controllers were before:

```js
/* START ROUTE CONTROLLERS */

const todoController = require('./controllers/todos');
app.use(todoController);

/* END ROUTE CONTROLLERS */
```

Test things out. Our code is now more modular and easier to read!

Let's make one more improvement. All of our paths to our routes for the todos will **always** start with `/todos`, so our code is inherently not going to be dry if we have to keep repeating that in each controller. Instead, we can tell Express to use the `todoController` for all routes that start with `'/todos'`. Back in the `index.js` make the following change:

```js
// replace:
// app.use(todoController);
// with:
app.use('/todos', todoController);
```

Now we can change the path in our routes in the `controller/todos.js` file to remove the `/todos` from each:

```js
// Index: GET all the todos
router.get('/', (req, res) => {
  // ...
});

// Show: GET an individual todo
router.get('/:id', (req, res) => {
  // ...
});
```

Awesome sauce!!! Not only is our code more DRY and modular, but now we have a good pattern that will be identical in all of our routes regardless of the resource name!

## Creating a new To Do

We want to be able to add new to do items to our list. Doing so will require submitting a form that will be processed by our server.

#### Question: What are the two component parts of the new route?

```js
router.get('/new', (req, res) => {
  res.render('new');
});
```

> Be sure to place this route BEFORE the get by id route, otherwise the controller file will try to find a todo with an id of "new" and not be able to load the new template.

### Forms

In Express, in order to process user input received through a form we will need to some middleware. The method `.use` sets up middleware for the Express application.

Make the following changes to your `index.js` file:

```js
// `express.json` parses application/json request data and
//  adds it to the request object as request.body
app.use(express.json());
// `express.urlencoded` parses x-ww-form-urlencoded request data and
//  adds it to the request object as request.body
app.use(express.urlencoded({ extended: true }));
```

Now, we can create our form view:

### New View

```html
<!--views/todos/new.hbs-->
<h2>Add a new To Do</h2>
<form action="/todos" method="POST">
  <div class="form-group">
    <label for="title">Title:</label>
    <input type="text" id="title" name="title" class="form-control" />
  </div>
  <input type="submit" class="btn btn-primary" />
  <a href="/todos" class="btn btn-secondary">Cancel</a>
</form>
```

The body of a request is an object with key-value pairs. The key is determined by the `name` attribute and the value is determined by the `value` attribute. So when the above form is submitted, the title for our new to do will be stored at `req.body.title`.

A common convention is to nest data inside of another sub-object. Doing so just requires modifying the `name` attribute to `name="todo[title]"`. If we do that, then the title of our new to do will be stored at `req.body.todo.title`.

A user is going to fill out this form and then hit submit. We will need a route in our controller to handle that:

```js
// Create: POST a new todo to the database
router.post('/', (req, res) => {
  Todo.create(req.body)
    .then((todo) => {
      res.redirect('/todos');
    })
    .catch(console.error);
});
```

> **`router.post('/', (req, res) => {})`** - listen for `post` requests to `/todos`.
>
> **`Todo.create(req.body)`** - create a new to do in the database
>
> **`res.redirect('/todos')`** - redirect back to the root route

Lastly, let's add a link at the top of the page in our `todos/index.hbs` file to the new form. To do that **replace** the `h1` at the top of the page with:

```hbs
<header class="d-flex justify-content-between align-items-center">
  <h1>Todos</h1>
  <a href="/todos/new" class="btn btn-primary">New To Do</a>
</header>
```

## Updating a To Do

Updating a to do has two steps: first, we need to get the to do that the user wants to update and render a form to update that to do, typically called the `edit` view; second, when the user submits that form, we update that to do in the database. For updating data in REST, we use `PUT` and `PATCH`, here we'll use `PUT`. There's just one small problem: `PUT`, `PATCH`, and `DELETE` are all part of REST and HTTP - they are **not** part of HTML. That means, we can't make the `method` attribute of a form equal any of those three methods.

To get around this, the team behind Express built the [method-override](https://github.com/expressjs/method-override) package, which we need to install with `npm i method-override`.

Method-override overrides the form method, so we can set the method equal to `POST` and add a query parameter to the action to tell Express what HTTP verb we actually want to use.

Inside of `index.js`, we need to include and configure `method-override`:

```js
// index.js

const methodOverride = require('method-override');

// ...

app.use(methodOverride('_method'));
```

Now we can create a form inside of `views/todos/edit.hbs`:

```hbs
<!--views/todos/edit.hbs-->
<h2>Edit To Do:</h2>

<form action="/todos/{{this.id}}/?_method=put" method="POST">
  <div class="form-group">
    <label for="title">Title:</label>
    <input type="text" id="title" name="title" class="form-control" value="{{this.title}}">
  </div>

  <div class="form-check mb-3">
    {{#if this.complete}}
    <input class="form-check-input" type="checkbox" id="complete" name="complete" checked="true" />
    {{else}}
    <input class="form-check-input" type="checkbox" id="complete" name="complete" />
    {{/if}}
    <label class="form-check-label" for="complete">Complete</label>
  </div>
  <input type="submit" value="Update To Do" class="btn btn-primary" />
  <a href="/todos" class="btn btn-secondary">Cancel</a>
</form>
```

When we submit the above form, we'll make a `POST` request, Express will see the query parameter `?_method=put` in the form action and redirect this to our `PUT` route. Inside our `controllers/todos.js` file, lets add our routes to show the form and handle the form processing:

```js
// Edit: GET an HTML form to update an existing todo
router.get('/:id/edit', (req, res) => {
  const id = req.params.id;
  Todo.findById(id)
    // Todo.find({ _id: id })
    .then((todo) => {
      res.render('todos/edit', todo);
    })
    .catch(console.error);
});

// Update: PUT data into the database when the edit form is submitted
router.put('/:id', (req, res) => {
  const id = req.params.id;
  Todo.findOneAndUpdate(
    { _id: id },
    {
      title: req.body.title,
      complete: req.body.complete === 'on',
    },
    { new: true }
  )
    .then((todo) => {
      res.render('todos/show', todo);
    })
    .catch(console.error);
});
```

The first route above queries the database using `.findById()` to find a record with the same `_id` as the id passed in via params. We then render that to do to the user. When the user submits the form in the edit view, we use the `findOneAndUpdate()` method to find and update a record in a single operation.

Excellent! Let's add an edit button into `todos/index.hbs` file so we can easily test it out:

```hbs
<!--insert this anchor tag into the div with a class of 'links'-->
<a href="/todos/{{this.id}}/edit" class="btn btn-primary">Edit</a>
```

It'd also be nice if we could visually see if a todo is complete in the index list so let's update that as well inside our `todos/index.hbs` using the if helper.

```hbs
<!-- replace: <div>{{this.title}}</div> with: -->
{{#if this.complete}}
<div class="text-muted"><s>{{this.title}}</s></div>
{{else}}
<div>{{this.title}}</div>
{{/if}}
```

## Delete a To Do

We're almost there! Last bit of CRUD functionality we need to implement is `DELETE`. Let's start by adding a second form with a delete button to our index view:

```html
<!--insert this anchor tag into the div with a class of 'links'-->
<form action="/todos/{{this.id}}?_method=DELETE" method="POST" class="d-inline">
  <input type="submit" value="Delete" class="btn btn-danger" />
</form>
```

We're using `method-override` again to tell Express we actually want to perform a `DELETE` action.

When a user wants to delete a record, we can use the `.findOneAndRemove()` to find and remove that record in a single operation:

```js
// Delete: DELETE the todo with a given id from the database
router.delete('/:id', (req, res) => {
  const id = req.params.id;
  Todo.findOneAndRemove({ _id: id })
    .then(() => {
      res.redirect('/todos');
    })
    .catch(console.error);
});
```

## :tada: Congrats!

We now have full CRUD and are following both MVC and REST for our todos resource!

# Your Turn

It's your turn to build another resource using this pattern. Here's your chance to put the pieces together on your own and create some RESTful routes for a songs resource.

**_Read through all of the steps below before you begin so you have a clear plan for how you'll proceed._**

1. Start by creating another model for your new song resource in a new file called `models/song-model.js`. The schema for your songs should have two attributes (add more if you like):

- A title which should be a String and required.
- An artist which should also be a String.

2. Next, create a `song-seeds.js` and `song-seeds.json` file in the `db` directory. Pattern both of these files off of the `todo-seeds.js` and `todo-seeds.json` files respectively. No need to create another connection file. You're simply going to add a new `songs` collection to the same `express-mvc` database we've been working with throughout the lecture.
3. Once you've verified that your seed data is in your database, add a new `/controllers/songs.js` file. Again, use the existing `/controllers/todos.js` file as an example and follow the pattern to replicate it by importing your Song model and creating a router that you export.
4. Require your controller file inside your `index.js` either immediately before or immediately after your todos controller. Make sure you then add the `app.use()` method for your new controller and pass it the `'/songs'` path as the first argument and the variable that you used to store the songs controller when you required it as the second argument.
5. Scaffold out your 7 routes inside of your `controllers/songs.js` file. All of the paths and methods will be **_identical_** to the paths and methods in your todos controller! The only things that will change will be the data that you get from the database and the files that you render inside your callback functions. Many developers like to stub out all of the routes in the file before they begin creating their views or figuring out what data should be returned. For example:

```js
// Index:
router.get('/', (req, res) => {});

// New:
router.get('/new', (req, res) => {});

// Edit:
router.get('/:id/edit', (req, res) => {});

// Show:
router.get('/:id', (req, res) => {});

// ... fill in the rest on your own!
```

6. Create a new `songs` directory in your `views` directory and create `edit.hbs`, `index.hbs`, `new.hbs`, and `show.hbs` files. The `layout.hbs` doesn't need to be duplicated or modified in any way. Handlebars will automatically use it when you call the render method in your controllers.
7. Fill in the first route controller callback and add the contents of the `views/songs/index.hbs` file to get all of the songs to display. Again, pattern these off of the corresponding route and file for the todos that we did together.
8. Work through each of the remaining 6 routes until you're done!

### BONUS 1: Add a Home Page

Add a homepage to your app by including a route for the base URL (localhost:4000/). Make sure the view you create has links to both the `/songs` and `/todos` routes.

### BONUS 2: Add a Bootstrap Modal

Instead of having the views rendered for the show route, pop open a [Bootstrap Modal](https://getbootstrap.com/docs/4.5/components/modal/#live-demo) using the code in the Live Demo
as an example. In addition to the CSS that's already included in the `layout.hbs`, you'll have to update that file to also add the required [Bootstrap JS](https://getbootstrap.com/docs/4.5/getting-started/introduction/#js) files.

### BONUS 3: Add a Third Resource of Your Choosing

Ready to really solidify your understanding of routes? Have another go at adding a new resource to your app. This time you get to choose what that resource should be and how you'll model it.

## Resources

- [Using ES6 Promises Instead of Callbacks for Mongoose Queries](http://erikaybar.name/using-es6-promises-with-mongoosejs-queries/)
- [How to Write Middleware for Express Apps](https://stormpath.com/blog/how-to-write-middleware-for-express-apps)
- [Method Override](https://github.com/expressjs/method-override)
