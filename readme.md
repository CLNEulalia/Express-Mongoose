# Express & Mongoose

## Learning Objectives

* Identify how an Express app fits within the MVC framework
* Connect an Express app to a MongoDB database
* Implement CRUD functionality in an Express app using Mongoose

## Framing (5 minutes / 0:05)

So far in this unit you've learned about a number of tools - Node, Express, MongoDB and Mongoose - that developers can use to build a server-side Javascript application. You have yet, however, to use them all together. We'll be spending the bulk of today's lesson connecting everything and creating an application that can receive HTTP requests, retrieve data/make changes to a database and send information back to the end-user.

### Starter/Solution Code

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

Optionally, create a new branch on which you will do your work...

```bash
$ git checkout -b MyName-express-mongoose
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

### 1. MVC Chart

As you're reviewing the app, try to fill in the blanks in the below Rails-to-Express MVC table. Your job here is to find the ME(A)N equivalents of components in a Rails application. Keep in mind, the answers for many of these are portions of files rather than entire files themselves.

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

### 2. Questions

Write down **up to three questions** on topics you would like further clarification on. We will spend 15 minutes going over this (and the MVC chart) afterwards.

## Before We Continue!

You are welcome to code along during the "I Do's" and "We Do's" in this lesson plan. We do ask, however, that **if you fall behind, do not attempt to catch up during those sections**. Instead, tilt down your screen and watch / take notes.

You are more than welcome to catch up when we get to the "You Do's," during which the instructors are available to help.

## Mongoose

### Why are we using Mongoose?

Like ActiveRecord for Rails, Mongoose is an ODM we can use to represent data from a Mongo database as models in a Javascript back-end.

## Connect to Mongoose (10 minutes / 0:45)

In order for us to use Mongoose to communicate with our database, we need to link it up to our Express application. We'll do this by...
* Establishing a connection with a Mongo database.
* Define a Mongoose schema and model.
* Generate some seed data.

### Steps

1. Install Mongoose via the command line: `npm install --save mongoose`.
2. In `connection.js`, require "mongoose" and save it to a `mongoose` variable.
3. Define a `CandidateSchema` using mongoose's `.Schema()` method.
4. Define a "Candidate" model built off `CandidateSchema` with `mongoose.model()`.
5. Connect to our `whenpresident` database using `mongoose.connect()`.

### Questions

<details>

  <summary><strong>What argument do we pass into `mongoose.connect()`?</strong></summary>

  > The location of the Mongo database.

</details>

<br/>

<details>

  <summary><strong>Does `.Schema()` modify our database? What about `.model()`?</strong></summary>

  > No. It's only once we start querying the database that it changes.

</details>

<br/>

<details>

  <summary><strong>What does `module.exports` do?</strong></summary>

  > It allows us to export code from one file to another. We could export only portions of the code or all of it if we wanted to.

</details>

<br/>

![Connect to Mongoose](https://i.imgur.com/g1LnWzx.png)

> **`var mongoose = require("mongoose")`** - In order to reference Mongoose, we need to require its corresponding node module and save it in a variable we can reference later.  
>  
> **`mongoose.Schema( )`** - We use Mongoose's schema method to define a blueprint for our Candidate model (i.e., what attributes it will have and what data types they will be).  
>  
> **`mongoose.model( )`** - We attach our schema to our model by passing in two arguments to this method: (1) the desired name of our model ("Candidate") and (2) the existing schema.  
>  
> **`mongoose.connect`** - We also need to link Mongoose to our `whenpresident` Mongo database.  

## Seed the Database (10 minutes / 0:55)

Mongoose is now connected to our Express application. Let's seed some data into our database using Mongoose.

### Steps

In `connection.js` we need to...
  1. Remove any references to seed data from `connection.js`.
  2. Set `module.exports = mongoose`.

Now, create a new `db/seed.js` file. In it we will...
  1. Require `connection.js` and `seeds.json`, saving them to their own `mongoose` and `seedData` variables respectively.  
  2. Define a `Candidate` variable that will contain our Mongoose model definition.
  3. Write Mongoose queries that accomplish the following...
    - Clears the database of all data, and `.then`...
    - Inserts our seed data into the database, and `.then`...
    - Calls `process.exit()`.

We can test this by...
  1. Running `$ node db/seed.js` in the Terminal.
  2. Then run `$ mongo` in the Terminal and enter the following commands via the Mongo CLI interface...

    ```
    > use whenpresident
    > db.candidates.find()
    ```

> If you need help remembering Mongoose queries, [the official documentation is a good place to look](http://mongoosejs.com/docs/guide.html).

### Questions

<details>

  <summary><strong>Why are we able to write out `mongoose.model("Candidate")` in `seed.js`?</strong></summary>

  > Because we defined that candidate in `connection.js`, which has been required.

</details>

<br/>

<details>

  <summary><strong>What does it mean to pass `{}` as an argument into `.remove()`?</strong></summary>

  > To remove everything. An empty object means that we're not going to restrict removal to certain key-value pairs.

</details>

<br/>

![Add Seed Data to DB 1](https://i.imgur.com/zWHSpRO.png)

> Notice that `connection.js` no longer contains any reference to seed data. It now only serves as a connection between our application and database.  

![Add Seed Data to DB 2](https://i.imgur.com/qzFr8AI.png)

> **`var mongoose = require("./connection")`** - Note that this time `var mongoose` is not set to `require("mongoose")`. Instead, it represents a connection to our database.  
>  
> **`var Candidate = mongoose.model("Candidate")`** - Because we defined our model in `connection.js`, we can reference it like so.  
>  
> **`Candidate.remove({})`** - This clears our entire database. We're not passing in any parameters, so Mongoose interprets this command as delete anything!  
>  
> **`Candidate.collection.insert(seedData)`** - Create a collection using the JSON contained in our seed file.  

## Break (10 minutes / 1:05)

## We Do: Index (10 minutes / 1:15)

First order of business: display all candidates stored in the database. We'll do this by adding code to the controller that...
* Retrieves all of the candidates from the database.
* Renders a view displaying the retrieved candidates.

### Steps

In `index.js`, let's make some changes to our variable definitions...
  1. Rename `db` to `mongoose`. We will be calling Mongoose methods on this variable - this makes more sense semantically!  
  2. Define a `Candidate` model in the exact same way as in `seed.js`.

Now let's move down to our index route...
  1. Use Mongoose to retrieve all Candidates from our database, and `.then`...
  2. Render our existing index view, making sure to set `candidates` (the variable we will be accessing in the view) to the response of our Mongoose method.

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

  <summary><strong>Why does our `res.render` statement need to be wrapped in a callback?</strong></summary>

  > We want to wait until the Mongoose query has been completed before we render anything, especially if the render is dependent on data returned from the database.

</details>

<br/>

![Index](https://i.imgur.com/QBz4ikv.png)

> **`Candidate.find({})`** - Retrieves all candidates in the database since we are not passing in any parameters to the method.  
>  
> **`.then(function(candidates){ ... })`** - `candidates` represents the all the Candidates pulled from the database. We can then reference this inside of `.then`.  
>  
> **`candidates: candidates`** - A little confusing, but the `candidates` we will be referencing in our view are now set to the `candidates` that are returned by Mongoose.  

## You Do: Show (15 minutes / 1:30)

> 10 minutes exercise. 5 minutes review.

So we can show all candidates. You know what's cooler than all candidates? **ONE** candidate. Let's go back into the controller and creating a corresponding route / action for that.

### Steps

Let's make changes to our existing show route...
  1. Use a Mongoose method to retrieve the candidates whose name is located in the browser URL. (Hint: use `req.params`). `.then`...
  2. Render the existing show view, making sure to pass in the retrieved candidate as the value to the `candidate` key.

### Questions

<details>

  <summary><strong>What's the difference between `.find` and `.findOne`?</strong></summary>

  > `.find` returns multiple items. `.findOne` only returns one.

</details>

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ![Show](https://i.imgur.com/OCIL9H5.png)

</details>

## Forms & `body-parser` (10 minutes / 1:40)

In NodeJS, in order to process user input received through a form we will need to install and implement the `body-parser` middleware.  

Install it via the command line -- `npm install --save body-parser` -- then make the following changes to your `index.js` file...  

![Install body-parser](https://i.imgur.com/ZWSc01J.png)

> **`var parser = require("body-parser")`** - Require `body-parser` so we can reference it later.  
>  
> **`app.use(parser.urlencoded({extended: true}))`** - configure the parser to support html forms

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
  <summary><strong>What must be be in the form tag to create those params?</strong></summary>

  > Input tags will contain `name="candidate[year]"`.

</details>

### Steps

Create a form in the index view.

1. Using the `action` attribute, the form should direct to `/candidates`.
2. Using the `name` attribute, each input should store the value of a candidate attribute (i.e., `name` and `year`) to a key that exists inside of a `candidate` object.

### Questions

<details>

  <summary><strong>Why do we set the `name` attribute to something like `candidate[name]`? How does this impact how we access this information in `index.js`?</strong></summary>

  > All candidate information will be available to us inside of a `candidate` object on the back-end.

</details>

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ![New non-functional 1](https://i.imgur.com/JqhY57R.png)

</details>

### Steps

Before we actually create a new candidate in the database, let's make sure we can access the user input submitted through the form.

1. In `index.js`, create an express `POST` route that corresponds with `/candidates`.
2. The route's only content should be a `res.json()` statement that returns the user input. (Hint: this is stored somewhere in `req`).

### Questions

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

  <summary><strong>Why are we accessing `req.body` instead of `res.body`?</strong></summary>

  > Because we want to render whatever the user sent through the form as JSON.

</details>

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ![New non-functional 2](https://i.imgur.com/iyxCyZC.png)

  > **`res.json(req.body)`** - The server will respond with JSON that contains the user input, which is stored in `req.body`. This should look just like the output of Rails APIs you have created in this course.

</details>

## We Do: Create (10 minutes / 2:00)

Let's modify this post route so that it creates a candidate in our database.

### Steps

1. In `index.js`, use a Mongoose method to create a new candidate. Pass in an argument that contains **only** the candidate's name. (Hint: Again, this is stored somewhere in `req`). `.then`...  
2. Redirect the user to the show view for the newly-created candidate.

### Questions

<details>

  <summary><strong>What is `res.redirect`? How is it different from `res.send`, `res.render` and `res.json`?</strong></summary>

  > `res.redirect` initializes a new request-response cycle and usually is not passed any data from the controller.

</details>

<br/>

![Create in DB](https://i.imgur.com/hqKzbWa.png)

> **`Candidate.create(req.body.candidate)`** - Pass in the name stored in `req.body` as an argument to `.create`.  
>  
> **`res.redirect()`** - Redirect the user to the new candidate's show view. In the callback, `candidate` represents the new candidate in our database.  

## Break (5 minutes / 2:05)

## You Do: Edit/Update (15 minutes / 2:20)

Onto editing and updating candidates. We'll set up a form in our show view to allow users to submit updated candidate information.

### Steps

Create an edit form in the show view.

1. Using the `action` attribute, the form's `action` attribute should direct to our application's show URL.
2. Using the `name` attribute, each input should store the value of a candidate attribute (i.e., `name` and `year`) to a key that exists inside of a `candidate` object.

### Questions

<details>

  <summary><strong>Why does `method="post"` even though we are updating (vs. creating) something?</strong></summary>

  > HTML does not support `PUT` or `PATCH`. That being said, we can make a `POST` request and define behavior on the back-end that will actual update something instead of create.

</details>

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ![Edit](https://i.imgur.com/74vYqMa.png)

  > **`method="post"`** - Wait, why is this a `POST` method? Aren't we supposed to send a `PUT` or `PATCH` request?  

</details>

### Steps

1. In `index.js`, create a `.post` route in `index.js` that corresponds to our new form.
2. In it, use a Mongoose method to find and update the candidate in question. (Hint: Refer to the Mongoose [lesson plan](https://git.generalassemb.ly/ga-wdi-lessons/mongoose-intro#update-5-min) or  [documentation](http://mongoosejs.com/docs/api.html#query_Query-findOneAndUpdate)).
3. `.then`, redirect the user to the updated candidate's show page.

### Questions

<details>

  <summary><strong>How come `.findOneAndUpdate` has 3 arguments while `.create` has only 2?</strong></summary>

  > Because we need to identify the thing we are updating **AND** what it's going to be updated with.

</details>

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ![Update](https://i.imgur.com/rtQGmQi.png)

  > **`.findOneAndUpdate()`** - This method takes three arguments: (1) the new params, (2) the candidate to be updated and (3) `new: true`, which causes the modified candidate to be returned in the callback.

  > **Note**: this screenshot cut off a method call, can you think what should happen after the update?

</details>

## You Do: Delete (10 minutes)

> We may not get to this during the lesson, but you should be able to implement this yourselves with the instructions below.

We're almost there! Last bit of CRUD functionality we need to implement is `DELETE`. Let's start by adding a delete button to our show view...

### Steps

1. Using the `action` attribute, the form should direct to `/candidates/:name/delete`.

### Questions

<details>

  <summary><strong>Why can't we use `app.delete` for a `DELETE` route?</strong></summary>

  > Again, because HTML only supports `GET` and `POST`, not `PUT` `PATCH` or `DELETE`.

</details>

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

  ![Delete 1](https://i.imgur.com/76mp0U4.png)

  > Again, **`method="post"`**. What's up with that?  

</details>

### Steps

1. In `index.js`, create a route that corresponds to our delete button.
2. In it, use Mongoose to find and delete the candidate in question. (Hint: Refer to the Mongoose [lesson plan](https://git.generalassemb.ly/ga-wdi-lessons/mongoose-intro#delete-5-min) or [documentation](http://mongoosejs.com/docs/api.html#query_Query-findOneAndRemove)).

### Solution

<details>
  <summary><strong>Click to reveal...</strong></summary>

![Delete 2](https://i.imgur.com/1qF64Tf.png)

</details>

## Closing / Questions

* What does module.exports do?
* Why does method="post" even though we are updating (vs. creating) something?
* Why do we use promises and callbacks when calling methods on a Mongoose model?

## Homework

You should now be able to complete the [second part of the YUM homework](https://git.generalassemb.ly/ga-wdi-exercises/yum#part-ii-add-express).

## Resources

* [Using ES6 Promises Instead of Callbacks for Mongoose Queries](http://erikaybar.name/using-es6-promises-with-mongoosejs-queries/)
* [How to Write Middleware for Express Apps](https://stormpath.com/blog/how-to-write-middleware-for-express-apps)
* [Method Override](https://github.com/expressjs/method-override)
