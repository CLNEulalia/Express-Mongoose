# Express & Mongoose

## Learning Objectives
* Understand each part of the MVC architecture
* Explain the role each part of MVC plays in an application
* Identify how an Express app fits within the MVC framework
* Connect an Express app to a MongoDB database
* Introduce CRUD
* Implement CRUD functionality in an Express app using Mongoose

## Framing (5 minutes / 0:05)

So far in this unit you've learned about a number of tools - Node, Express, MongoDB and Mongoose - that developers can use to build a server-side JavaScript application. You have yet, however, to use them all together. We'll be spending the bulk of today's lesson connecting everything and creating an application that can receive HTTP requests, retrieve data / make changes in a database, and send information back to the end-user.

### Quick Review (5 minutes)
* What is Express?
* What is Node?
* What is MongoDB?
* What is Mongoose?
* What is routing?
* What is middlewear?
* What is Handlebars?

## Intro to MVC

Before we get in to learning how to build fullstack web applications with Express, we need to take a step back and introduce an important architectural pattern: MVC.

> What might we mean when we say "architectural pattern"?

When building an application, there is an important step that has to happen that is often described as *architecting* the app. This step encompasses things like deciding on a file structure, figuring out what entities your application will have and how they relate to each other, etc. So, we describe it as *architecting*.

Just like with buildings, there are common ways of architecting an application. This goes back to the idea of "Convention over Configuration" that we've talked about previously. Using a common convention for the architecture of our application lets us get started sooner and onboard other developers quickly.

The application architecture we're learning today is one of the most common. It's called MVC, which stands for **model**, **view**, **controller**.

## Why MVC?
(5 minutes)

What makes MVC so powerful as an architectural pattern is the idea of [Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns).

<details>

<summary>What is Separation of Concerns?</summary>

For any given feature of an application, we'll have multiple things we need to do to build that feature out: persist the data for that feature, present the data for that feature, write some business logic to control how the feature works. Each of these can be considered a separate **concern**: presentation, persistence, business logic. Separating these makes them easier to build and write, maintain or change. If we want to change how we're presenting some data, we can do so by just changing the presentation and it wont affect the persistence or business logic.
</details>

## MVC In Action
(20 minutes)

Lets say we're building a hypothetical application for our client, Beyonce. This application will let her fans buy and sell second-hand merchandise. We'll call it eBae.

Specifically, we need to build out the part of the application that will let a user post a new item for sale. To do so, we need to know what data we have about a given item, how to present an item and how we can control the data and it's presentation through some business logic.

These map directly to the three parts of MVC:
1. Model - where we describe the data of a feature
2. View - where we present the data for a feature
3. Controller - where we compose our data and it's presentation through some business logic

For the feature we need to add to eBae, our Model, View and Controller would break down like this:

### The Model
The model is where we define the data for our feature. This is very closely related to Domain Modelling and ERDs.

If we were building an application for a library, we would have a model for a book, an employee, a member. Each of these would contain the definition of any attributes the entity has. For our eBae application, the model that we would need to define is for an Item that a user is selling - it may have a description, a seller, and a starting bid price.

### The View
The view is what the user sees and interacts with (the HTML and CSS that get's rendered in the browser).

Each Model can have a couple of different views. In MVC there are some conventions around common views:
  - a list/index view, where we show every instance of a model
  - a show view, where we see a single instance of a model
  - an edit view, where we can update a single instance of a model
  - a new view, where we can create a new instance of a model

In eBae, we need to build out a couple of views:
  - a view to show every item that is for sale (the list/index view)
  - a view for a single item for sale (the show view)
  - a view for a seller to edit an item they've posted for sale (an edit view)
  - a few for a seller to post a new item for sale (a new view)

### The Controller
The controller is where we knit our models and views together. It is where our business logic will live and we'll compose our views and models together.

In eBae, a user will see a new view that will display a form for them to fill out to post a new item for sale. That form will need to be processed - that's where our controller comes in. The controller will take that data and create a new Item from our Item model and save it to the database. Then, when another user wants to see that item, they'll visit the show view. When they do, the controller will use the model to pull the data for that specific item and render our show view.

## Turn And Talk
(5 minutes)

Turn to your neighbor(s) and review the following questions:
1. What is MVC? What does it stand for? What are the different parts of MVC?
2. For each part of MVC,
  - what is it's concern?
  - How does it interact with the other two parts?

### Request Response Cycle (5 minutes)

As a result, the request-response cycle looks like this for our Express applications...

  1. A user of our web application submits a request to our application's server.
  It can come in a myriad of ways. It could be someone typing in a URL and hitting
  enter, clicking a link or submitting a form on our application.

  2. The request hits the router of the application.

  3. The application then either doesn't recognize the route (error) or it does
  recognize it (route) and sends it to the appropriate controller.

  4. Once the controller gets the request, it performs any necessary actions. This
  might include fetching, updating, deleting, or creating information using one
  or more models.

  5. Once the controller has performed any actions / retrieved any information
  from the model(s), it uses a view to *render* an HTML page.

  6. The rendered view is then sent back to the client as a response.

### You Do: Diagramming a Website (5 minutes)
Diagram one of your favorite websites according to the MVC paradigm.

## Break (10 Minutes)

## CRUD

When we create applications, we often follow the CRUD paradigm for our database models. We have specific actions that we want to perform on our data. CRUD stands for:
* Create - make a new instance of our data
* Read - view our data
* Update - edit an existing data instance
* Delete - remove an existing piece of data

## Setup

Today we'll be working on a brand new app called "When President." It's a simple, one-model CRUD app that allows a user to declare who they're voting for as president in a given year.

Let's begin by cloning down the repo...

```bash
$ git clone git@git.generalassemb.ly:ga-wdi-exercises/whenpresident.git
```

> The starter and solution code are branches in the [`WhenPresident` repo](https://git.generalassemb.ly/ga-wdi-exercises/whenpresident/).


#### Starter Code

Checkout to the proper branch...

```bash
$ git checkout express-mongoose-starter
```

#### While You're At It...

Install the modules listed in `package.json`...

```bash
$ npm install
```

Then, start the Mongo server in a separate Terminal tab or window...

```bash
$ mongod
```

Check out what the starter code looks like in the browser by running `$ nodemon` and then visiting `http://localhost:3001` in the browser.

## Express Review (25 minutes)

> 10 minutes exercise. 15 minutes review.

Take 10 minutes to review the Express application as it stands. As you're going through it, do the following...

### MVC Chart

As you're reviewing the app, try to fill in the blanks in the below Express MVC table. Your job here is to define where things are in the Express application. Keep in mind, the answers for many of these are portions of files rather than entire files themselves.

|                       |Express |
|-----------------------|--------|
| **Database**          |        |
| **ORM**               |        |
| **Database (Config)** |        |
| **Route**             |        |
| **Controller**        |        |
| **Model**             |        |
| **View**              |        |


## Mongoose

### Why are we using Mongoose?

Mongoose is an ODM we can use to represent data from a Mongo database as models in a Javascript back-end.

## We Do: Connect to Mongoose (10 minutes)

In order for us to use Mongoose to communicate with our database, we need to link it up to our Express application. We'll do this by...
* Establishing a connection with a Mongo database.
* Define a Mongoose schema and model.

### Set up Connection to MongoDB

1. Install Mongoose via the command line: `npm install --save mongoose`.
2. Delete the contents of `connection.js`.
3. In `connection.js`, require `'mongoose'` and save it to a `mongoose` variable.
4. Connect to our `whenpresident` database using `mongoose.connect()`.
5. To handle deprecation warnings, add the `useMongoClient: true` option to `mongoose.connect()` and set Mongoose's default Promise library to JavaScript's native `Promise`.
6. Export this connected version of `mongoose` via `module.exports`.

```js
// db/connection.js

const mongoose = require('mongoose')

mongoose.connect('mongodb://localhost/whenpresident', { useMongoClient: true })

mongoose.Promise = Promise

module.exports = mongoose
```

> **`const mongoose = require("mongoose")`** - In order to reference Mongoose, we need to require its corresponding node module and save it in a variable we can reference later.  
>  
> **`mongoose.connect`** - We also need to link Mongoose to our `whenpresident` Mongo database.  
>
> **`module.exports = mongoose`** - When this file (`connection.js`) is required in other files, it will evaluate to this *connected* version of `mongoose`.


### Defining a Mongoose Schema and Model

1. Create a new file in `db` called `schema.js`.
2. At the top of `schema.js`, require the `connection.js` file and save it to a `mongoose` variable.
3. Define a `CandidateSchema` using mongoose's `.Schema()` method.
4. Define a `'Candidate'` model built off `CandidateSchema` with `mongoose.model()` and save it to a `Candidate` variable.
5. Export the `Candidate` model using `module.exports`.

```js
// db/schema.js

const mongoose = require('./connection')

const CandidateSchema = new mongoose.Schema({
  name: String,
  year: Number
})

const Candidate = mongoose.model('Candidate', CandidateSchema)

module.exports = Candidate
```

> **`mongoose.Schema( )`** - We use Mongoose's schema method to define a blueprint for our Candidate model (i.e., what attributes it will have and what data types they will be).  
>  
> **`mongoose.model( )`** - We attach our schema to our model by passing in two arguments to this method: (1) the desired name of our model ("Candidate") and (2) the existing schema.  
>  
> **`module.exports = Candidate`** - When this file (`schema.js`) is required in other files, it will evaluate to the `Candidate` model defined here through which we will be able to query the `candidates` collection in our Mongo database.

## We Do: Seed the Database (10 minutes)

Mongoose is now connected to our Express application. Let's seed some data into our database using Mongoose.

### Set Up Seed File

Seeds allow us to quickly create dummy data. Why would we do that?

* In order to test out the interfaces and functionalities we build out, we need some content/data to manipulate in order to see how it looks and feels on our application.

1. Create a new `seeds.js` file in `db`.
2. In `seeds.js`, require the files containing our model definition and seedData:
    - Require the `schema.js` file and save it to a variable called `Candidate`
    - Require the `seeds.json` file and save it to a variable called `seedData`
3. Write Mongoose queries that accomplish the following...
    - Clears the database of all data, and `.then`...
    - Inserts our seed data into the database, and `.then`...
    - Calls `process.exit()`.

> If you need help remembering Mongoose queries, [the official documentation is a good place to look](http://mongoosejs.com/docs/guide.html).

```js
const Candidate = require('./schema')
const seedData = require('./seeds.json')

Candidate.remove({})
  .then(() => {
    return Candidate.collection.insert(seedData)
  })
  .then(() => {
    process.exit()
  })
```

> **`var Candidate = require('./schema.js')`** - Because we defined our model in `schema.js` and set its `module.exports` to be equal to the `Candidate` model, we can reference it like so.  
>  
> **`Candidate.remove({})`** - This clears out the entire `candidates` collection. We're not passing in any parameters, so Mongoose interprets this command as delete all documents in that collection!  
>  
> **`Candidate.collection.insert(seedData)`** - Create a collection using the JSON contained in our seed file. Note that this is ideal for bulk insertion but **skips** schema validation. In our controller, where we will want validation, we will use `Candidate.create()`.

### Running the Seed File

1. Run `$ node db/seed.js` in the Terminal.
2. Then run `$ mongo` in the Terminal and enter the following commands via the Mongo CLI interface...

```
> use whenpresident
> db.candidates.find()
```


## Break (10 minutes)

## We Do: Index (10 minutes)

First order of business: display all candidates stored in the database. We'll do this by adding code to the controller that...
* Retrieves all of the candidates from the database.
* Renders a view displaying the retrieved candidates.

### Steps

In `controllers/candidates.js`, let's make some changes to our variable definitions...
  1. Delete the line of code that defines a `db` variable (we will query through our model now).
  2. Define a `Candidate` model in the exact same way as in `seed.js` (note that the `require` path will be different, however).

Now let's move down to our index route...
  1. Use Mongoose to retrieve all Candidates from our database, and `.then`...
  2. Render our existing index view, making sure to set `candidates` (the variable we will be accessing in the view) to the response of our Mongoose method.

```js
router.get('/', (req, res) => {
  Candidate.find({})
    .then((candidates) => {
      res.render('candidates-index', {
        candidates: candidates
      })
    })
    .catch((err) => {
      console.log(err)
    })
})
```

> **`Candidate.find({})`** - Retrieves all candidates in the database since we are not passing in any parameters to the method.  
>  
> **`.then(function(candidates){ ... })`** - `candidates` represents the all the Candidates pulled from the database. We can then reference this inside of `.then`.  
>  
> **`candidates: candidates`** - A little confusing, but the `candidates` we will be referencing in our view are now set to the `candidates` that are returned by Mongoose.

### Questions

<details>

  <summary><strong>What does `res.render` mean? What do we need to pass into it as arguments?</strong></summary>

  > `res.render` is used to render the server response back to the browser. In this example we pass it (a) the view we want to render and (b) the data that should be available to it (i.e., candidates).

</details>

<br/>

<details>

  <summary><strong>What does it mean to pass `{}` as an argument into `.find()`?</strong></summary>

  > Like `.remove`, it means to find everything. The search is not limited to certain key-value pairs.

</details>

<br/>

<details>

  <summary><strong>Why does our `res.render` statement need to be wrapped in a callback (within Promise's `.then()` method)?</strong></summary>

  > We want to wait until the Mongoose query has been completed before we render anything, especially if the render is dependent on data returned from the database.

</details>

<br/>

## Lunch

## You Do: Show (15 minutes)

> 10 minutes exercise. 5 minutes review.

So we can show all candidates. You know what's cooler than all candidates? **ONE** candidate. Let's go back into the controller and update the corresponding route / action.

### Steps

Let's make changes to our existing show route...
  1. Use a Mongoose method to retrieve the candidates whose name is located in the browser URL. (Hint: use `req.params`). `.then`...
  2. Render the existing show view, making sure to pass in the retrieved candidate as the value to the `candidate` key.

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ```js
  router.get('/:name', (req, res) => {
    Candidate.findOne({ name: req.params.name })
      .then((candidate) => {
        res.render('candidates-show', {
          candidate: candidate
        })
      })
      .catch((err) => {
        console.log(err)
      })
  })
  ```

</details>

### Questions

<details>

  <summary><strong>What's the difference between `.find` and `.findOne`?</strong></summary>

  > `.find` returns multiple items. `.findOne` only returns one.

</details>

## REST

## Overview (5 / 2:35)

The web as we know it transfers data via HTTP, or Hyper-Text Transfer Protocol. HTTP specifies that a server receives a **request** from a client and then delivers a corresponding **response**.

Remember: a server's job is to respond to HTTP requests. In order to talk about how Express methods can respond to different HTTP verbs, we need to talk about how HTTP requests work.

Every HTTP request consists of a request **method** and **path**. The **path** is the part of the URL following the domain. We likely have noticed paths when navigating the web. What is the path for this lesson?

Your browser always sends that request in a *particular way* that gives the server a hint as to the purpose of the request. This *particular way* is the **method**.

## REST (15)

REST, or REpresentational State Transfer, is a convention that standardizes how clients make requests to servers.  

Knowing REST is important because the vast majority of web developers have agreed to follow this same convention.

"GET" is one of these methods. It means the browser just wants to read (or "get") some information. When you write `get '/say_hi' do`, you're telling your server how to respond when a browser says, "Hey, I'd like to get some information from the `/say_hi` path."

We make requests all the time -- especially `GET` requests. Everytime you go to your browser, enter a URL, and hit enter, you're actually making a `GET` request.

### RESTful HTTP Methods

REST defines five main methods, each of which corresponds to one of the CRUD functionalities.

| Method | Crud functionality | DB Action |
|------|--------|----------------------   |
| GET  | read   | retrieve data           |
| POST | create | add data                |
| PUT  | update | modify existing data    |
|PATCH | update | modify existing data    |
|DELETE| delete | delete existing data    |

So, wait -- there are 5 REST methods, but only 4 CRUD methods?

`PUT` and `PATCH` are both used for updating. Whenever you update your Facebook profile you're probably making a `PUT` or `PATCH` request. The difference is `PUT` would be intended to completely replace your profile, whereas `PATCH` would be intended to just change a few fields of your profile.

`PUT` rewrites data; `PATCH` just changes parts of data. To clarify further, `PATCH` is replacing part of the data and `PUT` is replacing the whole thing.

### What's the difference at a technical level between a GET and a POST request?

There is of course the difference in the METHOD type, but also in the request payload. A `POST` request for instance will contain all of the data necessary for creating some new object.

GET is for when you want to read something. The parameters of the GET request are used for identifying which piece of data the client would like to read. The parameters of the POST request are used for defining a new piece of data.

### RESTful Routes (5)

A **route** is a **method** plus a **path**...

**Method + Path = Route**

Each route results in an **action**.

REST provides a template for the way different paths should look...

| Method |      Path     |       Action |
| ------ | ------------- | ---------------------------------- |
|   GET  |  `/students`  | Read information about all students |
|  POST  |  `/students`  | Create a new student |
|   GET  | `/students/1` | Read information about the student whose ID is 1 |
|  PUT   | `/students/1` | Update the existing student whose ID is 1 with all new content |
|  PATCH | `/students/1` | Update the existing student whose ID is 1 with partially new content |
| DELETE | `/students/1` | Delete the existing student whose ID is 1 |

Note that the path doesn't contain any of the words describing the CRUD
functionality that will be executed. That's the method's job.

Let's check out the [ESPN website](http://espn.go.com/). If we go to a specific
player's webpage, we can see this same sort of structure in the URL.

### Example (10)

For the next few minutes, I'm going to play the role of a client/browser while you all collectively play the part of the server. Students will be resources on this server. Each table will have a number (I'll go down the aisle writing those numbers) and each seat at the table will have a position numbered from 1 at the aisle and up towards the walls. Remember your seat number.

Now I'm going to write my requests method and route on the board. Pay attention for your resource path and be ready to `respond` appropriately to my `request`!

<details>
  <summary>More Examples</summary>
  <p>
    path: /tables/3/students/1/name and method: GET (student should say their name)
  </p>
  <p>
    path: /tables/2/students/3/name and method: PUT "I'm putting 'Fred'" (hand student a marker to change their name)
  </p>
  <p>
    path: /tables/2/students/3/name and method: GET (same student should respond with the new name)
  </p>
  <p>
    path: /tables/5/students/2/name and method: DELETE (motion for the student to leave the class, just kidding!)
  </p>
</details>


### You do... (5)

Write out the routes (method & path) for the following requests. The first one is done for you.

1. **Create a new animal**
    - `POST /animals`
2. **Delete an animal**
    <details>
      <summary>Answer</summary>

      `DELETE /animals/1`
    </details>
3. **Update an existing homework assignment**
    <details>
      <summary>Answer</summary>

      `PUT /homework/1`
    </details>
4. **Create a new class at GA.**
    <details>
      <summary>Answer</summary>

      `POST /classes`
    </details>
5. **View all students in WDI19.**
    <details>
      <summary>Answer</summary>

      `GET /wdi19/students`
    </details>
6. **Update the info for an animal with 3 as its id.**
    <details>
      <summary>Answer</summary>

      `PATCH /animals/3`
    </details>
7. **Update homework submission #32 for assignment #3**
    <details>
      <summary>Answer</summary>

      `PATCH /homework/32/assignment/3`
    </details>


## Forms & `body-parser` (10 minutes)

In NodeJS, in order to process user input received through a form we will need to install and implement the `body-parser` middleware.  

Install it via the command line -- `npm install --save body-parser` -- then make the following changes to your `index.js` file...  

```js
// index.js

const parser = require('body-parser')

// ...

app.use(parser.urlencoded({ extended: true }))
```

> **`const parser = require("body-parser")`** - Require `body-parser` so we can reference it later.  
>  
> **`app.use(parser.urlencoded({extended: true}))`** - configure the parser to support html forms (access to the body of the request)

## We Do: New (20 minutes)

> 5 minutes exercise. 5 minutes review.

Let's create a new candidate form. We'll add it to our existing index view...

### Before You Start Coding...


<br/>

<details>

  <summary><strong>What attributes are important for a form tag?  Why?</strong></summary>

  > `action` and `method`.  This defines what route we will submit the form contents to.  

</details>

<br/>

<details>
  <summary><strong>What params do we need to access in the route/controller?</strong></summary>

  > `{ candidate: { name: "Al Gore", year: 2000 }`  

</details>

<br/>

<details>
  <summary><strong>What must be in the form tag to create those params?</strong></summary>

  > Input tags will need to contain name attributes matching the attributes of our model. Ex: `name="candidate[year]"`.

</details>

### Steps

Create a form in the index view.

1. Using the `action` attribute, the form should direct to `/candidates`.
2. Using the `name` attribute, each input should store the value of a candidate attribute (i.e., `name` and `year`) to a key that exists inside of a `candidate` object.

### Solution

  ```html
  <!-- views/candidates-index.hbs -->

  <h2>Register New Candidate</h2>
  <form action="/candidates" method="post">
    <input type="text" name="candidate[name]" placeholder="Name">
    <input type="text" name="candidate[year]" placeholder="Year">
    <input type="submit" value="Register">
  </form>
  ```
### Testing our Form

Before we actually create a new candidate in the database, let's make sure we can access the user input submitted through the form.

1. In `controllers/candidates.js`, create an express `POST` route that corresponds with `/candidates`.
2. The route's only content should be a `res.json()` statement that returns the user input. (Hint: this is stored somewhere in `req`).

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ```js
  router.post('/', (req, res) => {
    res.json(req.body)
  })
  ```
  > **`res.json(req.body)`** - The server will respond with JSON that contains the user input, which is stored in `req.body`. This should look just like the output of other APIs you have seen in this course.

</details>

### Questions

<details>

  <summary><strong>Why do we set the `name` attribute to something like `candidate[name]`? How does this impact how we access this information in `controllers/candidates.js`?</strong></summary>

  > All candidate information will be available to us inside of a `candidate` object on the back-end.

</details>

<br />

<details>

  <summary><strong>How are the form and `req.body` related?</strong></summary>

  > The values a user submits through the form can be found in `req.body` on the back-end.

</details>

<br/>

<details>

  <summary><strong>What does `res.json` do?</strong></summary>

  > It's sends a response back to the browser in JSON form.

</details>

<br/>

<details>

  <summary><strong>Why are we accessing `req.body`?</strong></summary>

  > Because we want to render whatever the user sent through the form as JSON.

</details>


## We Do: Create (10 minutes / 2:00)

Let's modify this post route so that it creates a candidate in our database.

### Steps

1. In `controllers/candidates.js`, use a Mongoose method to create a new candidate and pass in the `candidate` object as the argument. (Hint: Again, this is stored somewhere in `req`). `.then`...  
2. Redirect the user to the show view for the newly-created candidate.


```js
router.post('/', (req, res) => {
  Candidate.create(req.body.candidate)
    .then((candidate) => {
      res.redirect(`/candidates/${candidate.name}`)
    })
    .catch((err) => {
      console.log(err)
    })
})
```

> **`Candidate.create(req.body.candidate)`** - Pass in the `candidate` object stored in `req.body` as an argument to `.create`.  
>  
> **`res.redirect()`** - Redirect the user to the new candidate's show view. In the callback, `candidate` represents the new candidate in our database.  

## Break (5 minutes / 2:05)

## We Do: Edit/Update (15 minutes / 2:20)

Onto editing and updating candidates. We'll set up a form in our show view to allow users to submit updated candidate information. However, since HTML does not natively support `PUT` or `DELETE` requests, we will need to install some middleware to provide us a workaround.


### Steps

Set up Express to use [method-override](https://github.com/expressjs/method-override).

1. Install the `method-override` package via the command line.
2. Require `'method-override'` in `index.js` and save it to a `methodOverride` variable.
3. Configure Express to use `method-override` by calling `app.use()`.

```js
// index.js

const methodOverride = require('method-override')

// ...

app.use(methodOverride('_method'))
```

Now create an edit form in the show view.

1. The form's `action` attribute should direct to the same URL as the show route and its `method` attribute should be `post`.
2. Using the `name` attribute, each input should store the value of a candidate attribute (i.e., `name` and `year`) to a key that exists inside of a `candidate` object.
3. Lastly, to use `method-override` to change this to a `put` request, append a query string onto the `action` attribute with a key value pair for `_method`.

```html
<!-- views/candidates-show.hbs -->

<h2>Update Information</h2>
<form action="/candidates/{{candidate.name}}?_method=put" method="post">
  <input type="text" name="candidate[name]" value="{{candidate.name}}">
  <input type="text" name="candidate[year]" value="{{candidate.year}}">
  <input type="submit" value="Update">
</form>
```

> **`?_method=put`** - By adding this key value pair as a query string, `method-override` will represent this `post` request a `put` request *when the request hits the backend*.

Lastly, let's set up a route to handle this `put` request.

1. In `controllers/candidates.js`, create a `.put` route that corresponds to our new form.
2. In it, use a Mongoose method to find and update the candidate in question. (Hint: Refer to the Mongoose [lesson plan](https://git.generalassemb.ly/ga-wdi-lessons/mongoose-intro#update-5-min) or  [documentation](http://mongoosejs.com/docs/api.html#query_Query-findOneAndUpdate)).
3. `.then`, redirect the user to the updated candidate's show page.

```js
// controllers/candidates.js

router.put('/:name', (req, res) => {
  Candidate.findOneAndUpdate({ name: req.params.name }, req.body.candidate, { new: true })
    .then((candidate) => {
      res.redirect(`/candidates/${candidate.name}`)
    })
    .catch((err) => {
      console.log(err)
    })
})
```

> **`.findOneAndUpdate()`** - This method takes three arguments: (1) the new params, (2) the candidate to be updated and (3) `new: true`, which causes the modified candidate to be returned in the callback.


## You Do: Delete (10 minutes)


We're almost there! Last bit of CRUD functionality we need to implement is `DELETE`. Let's start by adding a second form with a delete button to our show view...

### Steps

Add a second form to the `candidates-show` view for sending a `delete` request.

1. The form's `action` attribute should direct to `/candidates/:name` (the same as the show view) with the `method` attribute set to `post`.
2. The form only needs one input: a submit button with a value of `Remove`.
3. Append a query string to the `action` attribute with a key-value pair for `_method` signifying that we want this to be treated like a `delete` request.

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ```html
  <h2>Remove Candidate</h2>
  <form action="/candidates/{{candidate.name}}?_method=delete" method="post">
    <input type="submit" value="Remove">
  </form>
  ```

</details>

Now, set up a route in the controller to handle this delete request.

1. In `controllers/candidates.js`, create a route that corresponds to our delete request.
2. In it, use Mongoose to find and delete the candidate in question. (Hint: Refer to the Mongoose [lesson plan](https://git.generalassemb.ly/ga-wdi-lessons/mongoose-intro#delete-5-min) or [documentation](http://mongoosejs.com/docs/api.html#query_Query-findOneAndRemove)).

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ```js
  router.delete('/:name', (req, res) => {
    Candidate.findOneAndRemove({ name: req.params.name })
      .then(() => {
        res.redirect('/candidates')
      })
  })
  ```

</details>

## Closing / Questions

* What does module.exports do?
* Why do we use promises and callbacks when calling methods on a Mongoose model?

## Homework

You should now be able to complete the [second part of the YUM homework](https://git.generalassemb.ly/ga-wdi-exercises/yum#part-ii-add-express).

## Resources

* [Using ES6 Promises Instead of Callbacks for Mongoose Queries](http://erikaybar.name/using-es6-promises-with-mongoosejs-queries/)
* [How to Write Middleware for Express Apps](https://stormpath.com/blog/how-to-write-middleware-for-express-apps)
* [Method Override](https://github.com/expressjs/method-override)

## Bonus Material
## Query Parameters (20 / 3:25)

So far we've done all of our passing-of-data through the route's path, as path
parameters. This hasn't been a problem since the data has been tiny -- a user
ID, or a name.

But when data starts getting bigger, this'll start getting a little crazy. We'd
need to be going to some enormous URLs.

REST gives us a couple different ways of sending data. Path params are the most
upfront and basic. Slightly more advanced are GET parameters, or query
parameters.

### We do: Demo with Twitter

To demonstrate, let's check out Twitter's search functionality.

`https://twitter.com/search`

I'm going to search for "toast". This takes me to a new URL...

`https://twitter.com/search?q=toast&src=typd`

Check out the stuff at the end of the URL. I've learned that `src` points to the search of the query in this case, I typed it in hence the `typd`. I also recognize "toast", which is in `q=toast`.

If I add `+strudel` to the end of `toast` and go to the new URL...

`https://twitter.com/search?q=toast+strudel&src=typd`

I can see that the search box now has the words "toast" and "strudel", and all
the displayed Tweets contain those words.

### Query strings

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

#### Looking back at the Twitter URL, with `q=toast+strudel`, what does `q` stands for?

### URL Encoding

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

