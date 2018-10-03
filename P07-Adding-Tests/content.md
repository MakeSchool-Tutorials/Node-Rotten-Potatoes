---
title: "Adding Tests"
slug: adding-tests
---

Automated testing is an important part of any professional coder's life. Writing automated tests slow you down to start, but then they speed you way up as your project gets more complex and large.

Node.js is great for testing because it has a lot of good **Test Runners** (We'll be using Mocha) and **Assertion Libraries** (We'll be using Chai) and tests run really quickly if you build them right.

# What is Automated Testing?

Automated testing is code that tests if your code is working.

The opposite if **Manual Testing**.

No matter what you have to test your code and you have to test that your new code didn't break older code. If you have automated tests setup then you can just run the tests for old code to make sure that your new code didn't break any old code. This means that old tests become **Regression Tests**, and act as a sort of double check that new code didn't break old stuff.

So the tests you write today for your new feature, in the future become regression tests to make sure your feature didn't break when someone wrote something.

# Adding A Test Runner and Assertion Library

We're goint to use a few tools to test with, let's install them all up front:

```bash
$ npm install -g mocha
$ npm install --save chai chai-http
```

* `mocha` is our **Test Runner** - it actually runs our test code.
* `chai` is our **Assertion Library** - it gives us syntactic sugar to make writing test intuitive.
* `chai-http` is a **Helper Test Library** - it gives us methods to make http request inside our tests very easily.

# Adding One Test: Index Route

We're going to use `mocha` to write a test for the index route of our Review resource.

Next we need to create a folder called `test` and inside that folder a file called `test-reviews.js` in there put the following code.

First we'll need to export the `app` variable from our `app.js` file to include it in our tests.

```js
// app.js

...


module.exports = app;

```

Now we can add our test file.

```js
// test-reviews.js

const chai = require('chai');
const chaiHttp = require('chai-http');
const server = require('../app');
const should = chai.should();
const Review = require('../models/reviews');

chai.use(chaiHttp);

describe('Reviews', ()  => {

  // TEST INDEX
  it('should index ALL reviews on / GET', (done) => {
    chai.request(server)
        .get('/')
        .end((err, res) => {
          res.should.have.status(200);
          res.should.be.html;
          done();
        });
  });

  // TEST NEW
  // TEST CREATE
  // TEST SHOW
  // TEST EDIT
  // TEST UPDATE
  // TEST DELETE
});

```

Now go to your terminal inside your project directory and type `mocha` to run your tests.

```bash
$ mocha
```

Mocha will always look inside the `/test` directory and run all the files inside.

### How this works

Lets look line by line how this test works.

```js

// tell mocha you want to test Reviews (this string is taco)
describe('Reviews', ()  => {
  // make taco name for the test
  it('should index ALL reviews on / GET', (done) => {
    // use chai-http to make a request to your server
    chai.request(server)
        // send a GET request to root route
        .get('/')
        // wait for response
        .end((err, res) => {
          // check that the response status is = 200 (success)
          res.should.have.status(200);
          // check that the response is a type html
          res.should.be.html;
          // end this test and move onto the next.
          done();
        });
  });
});
```

# Red-Green-Refactor

Before moving on, we should double check to see if we can break the test and make it return false.

Set the test to expect the response to have a status of `404` and rerun your tests. Did it turn "Red"?

What was the error message?

Now put it back to 200 and see if it is green again.

If you don't do this step with tests, you might just be testing an **Identity** like that `4 = 4`. That's not a very good test!

# Next Test: New

Now that we have the Index route, lets use the same sort of testing logic for the New route.


```js
// test-reviews.js
  // TEST NEW
  it('should display new form on /reviews/new GET', (done) => {
    chai.request(server)
      .get(`/reviews/new`)
        .end((err, res) => {
          res.should.have.status(200);
          res.should.be.html
          done();
        });
  });
```

Now run your tests.


# Next Test: Show & Edit

To test the rest of the tests we'll need sample test data in our database. We can put test data in pretty easily, but we'll also have to send a dump truck around after testing to remove the new data we've created, otherwise every time we run our tests, we'll be adding documents to our database.

Let's add a `sampleReview` to the top of the test file.


```js

const chai = require('chai');
const chaiHttp = require('chai-http');
const server = require('../server');
const should = chai.should();
const Review = require('../models/review');

const sampleReview =     {
    "title": "Super Sweet Review",
    "movie-title": "La La Land",
    "description": "A great review of a lovely movie."
}

```

And now we can use this sample review to generate a new review in our DB and then create it, visit it, edit it, update it, and delete it. Let's start just by visiting its show route.

```js
// test-reviews.js

  // TEST SHOW
  it('should show a SINGLE review on /reviews/<id> GET', (done) => {
    var review = new Review(sampleReview);
    review.save((err, data) => {
      chai.request(server)
        .get(`/reviews/${data._id}`)
        .end((err, res) => {
          res.should.have.status(200);
          res.should.be.html
          done();
        });
    });
  });
```

Now if we run this each time this route will create a new review during the show rout test. So we need the dump truck to come around and delete all our sample reviews after each test. We'll use an `after()` test hook that mocha provides for exactly this use case.

```js

describe('Reviews', ()  => {

  after(() => {
    Review.deleteMany({title: 'Super Sweet Review'}).exec((err, reviews) => {
      console.log(reviews)
      reviews.remove();
    })
  });

  ...

});

```

The edit route is the same. Add that now:

```js

// TEST EDIT
it('should edit a SINGLE review on /reviews/<id>/edit GET', (done) => {
var review = new Review(sampleReview);
 review.save((err, data) => {
   chai.request(server)
     .get(`/reviews/${data._id}/edit`)
     .end((err, res) => {
       res.should.have.status(200);
       res.should.be.html
       done();
     });
 });
});

```

# Next Test: Create

Now we'll do the create route test. We'll send in the `sampleReview` as JSON to the route and check that the response is a 200 status.

```js
  // TEST CREATE
  it('should create a SINGLE review on /reviews POST', (done) => {
    chai.request(server)
        .post('/reviews')
        .send(sampleReview)
        .end((err, res) => {
          res.should.have.status(200);
          res.should.be.html
          done();
        });
  });

```

Run your tests again! Your line of green dots should be getting longer. Can you make this create route test fail and turn red and then turn it back to green?

# Next Test: Update

For update we have to create the sample review, then send in a PUT message with a change and see if it response with a 200.

```js

  // TEST UPDATE
  it('should update a SINGLE review on /reviews/<id> PUT', (done) => {
    var review = new Review(sampleReview);
    review.save((err, data)  => {
     chai.request(server)
      .put(`/reviews/${data._id}?_method=PUT`)
      .send({'title': 'Updating the title'})
      .end((err, res) => {
        res.should.have.status(200);
        res.should.be.html
        done();
      });
    });
  });

```

# Next Test: Delete

Finally we have to see if we can test deleting a review. We'll create the sample review, then send in a delete message to see if we can destroy it and get a 200 status back.

```js

  // TEST DELETE
  it('should delete a SINGLE review on /reviews/<id> DELETE', (done) => {
    var review = new Review(sampleReview);
    review.save((err, data)  => {
     chai.request(server)
      .delete(`/reviews/${data._id}?_method=DELETE`)
      .end((err, res) => {
        res.should.have.status(200);
        res.should.be.html
        done();
      });
    });
  });
});

```

# Commit

Good work adding your Resourceful Routes test sweet. Your product currently has **100% Test Coverage** because each route has a corresponding test.

Now commit these changes and push them to github.
