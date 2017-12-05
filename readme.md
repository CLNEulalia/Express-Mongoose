# Express & Mongoose

## Learning Objectives

* Identify how an Express app fits within the MVC framework
* Connect an Express app to a MongoDB database
* Implement CRUD functionality in an Express app using Mongoose

## Framing (5 minutes / 0:05)

So far in this unit you've learned about a number of tools - Node, Express, MongoDB and Mongoose - that developers can use to build a server-side Javascript application. You have yet, however, to use them all together. We'll be spending the bulk of today's lesson connecting everything and creating an application that can receive HTTP requests, retrieve data / make changes in a database, and send information back to the end-user.

### Setup

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

## Express Review (25 minutes / 0:30)

> 10 minutes exercise. 15 minutes review.

Take 10 minutes to review the Express application as it stands. As you're going through it, do the following...

### MVC Chart

As you're reviewing the app, try to fill in the blanks in the below Rails-to-Express MVC table. Your job here is to find the Mongoose / Express equivalents of each component in a Rails application. Keep in mind, the answers for many of these are portions of files rather than entire files themselves.

|                       | Rails                      | Express |
|-----------------------|----------------------------|---------|
| **Language**          | Ruby                       |         |
| **Database**          | PostgreSQL                 |         |
| **ORM**               | Active Record              |         |
| **Database (Config)** | `database.yml`             |         |
| **Route**             | `routes.rb`                |         |
| **Controller**        | `candidates_controller.rb` |         |
| **Model**             | `candidate.rb`             |         |
| **View**              | `index.html.erb`           |         |



## Mongoose

### Why are we using Mongoose?

Similar to ActiveRecord for Rails, Mongoose is an ODM we can use to represent data from a Mongo database as models in a Javascript back-end.

## We Do: Connect to Mongoose (10 minutes / 0:45)

In order for us to use Mongoose to communicate with our database, we need to link it up to our Express application. We'll do this by...
* Establishing a connection with a Mongo database.
* Define a Mongoose schema and model.

### Set up Connection to MongoDB

1. Install Mongoose via the command line: `npm install --save mongoose`.
1. Delete the contents of `connection.js`.
1. In `connection.js`, require `'mongoose'` and save it to a `mongoose` variable.
1. Connect to our `whenpresident` database using `mongoose.connect()`.
1. To handle deprecation warnings, add the `useMongoClient: true` option to `mongoose.connect()` and set Mongoose's default Promise library to JavaScript's native `Promise`.
1. Export this connected version of `mongoose` via `module.exports`.

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
1. At the top of `schema.js`, require the `connection.js` file and save it to a `mongoose` variable.
1. Define a `CandidateSchema` using mongoose's `.Schema()` method.
1. Define a `'Candidate'` model built off `CandidateSchema` with `mongoose.model()` and save it to a `Candidate` variable.
1. Export the `Candidate` model using `module.exports`.

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

## We Do: Seed the Database (10 minutes / 0:55)

Mongoose is now connected to our Express application. Let's seed some data into our database using Mongoose.

### Set Up Seed File

1. Create a new `seeds.js` file in `db`.
1. In `seeds.js`, require the files containing our model definition and seedData:
    - Require the `schema.js` file and save it to a variable called `Candidate`
    - Require the `seeds.json` file and save it to a variable called `seedData`
1. Write Mongoose queries that accomplish the following...
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
1. Then run `$ mongo` in the Terminal and enter the following commands via the Mongo CLI interface...

    ```
    > use whenpresident
    > db.candidates.find()
    ```


## Break (10 minutes / 1:05)

## We Do: Index (10 minutes / 1:15)

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


## You Do: Show (15 minutes / 1:30)

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


## Forms & `body-parser` (10 minutes / 1:40)

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

## You Do: New (10 minutes / 1:50)

> 5 minutes exercise. 5 minutes review.

Let's create a new candidate form. We'll add it to our existing index view...

### Before You Start Coding...

<details>

  <summary><strong>What did we use in Rails to create an input form?</strong></summary>

  > `form_for` helper.  

  ```rb
  form_for @candidate do |f|
    f.input :name
    f.input :year
    f.submit
  end
  ```

</details>

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

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ```html
  <!-- views/candidates-index.hbs -->

  <h2>Register New Candidate</h2>
  <form action="/candidates" method="post">
    <input type="text" name="candidate[name]" placeholder="Name">
    <input type="text" name="candidate[year]" placeholder="Year">
    <input type="submit" value="Register">
  </form>
  ```

</details>

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
  > **`res.json(req.body)`** - The server will respond with JSON that contains the user input, which is stored in `req.body`. This should look just like the output of Rails APIs you have created in this course.

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

  > It's sends a response back to the browser in JSON form. This functions similarly to `format.json` in Rails.

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
