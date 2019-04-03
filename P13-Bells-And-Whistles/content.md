---
title: "Bells and Whistles"
slug: bells-and-whistles
---

If you're looking for some extra challenges, there are a few more things we can do to tighten up our website and make it look more like the big leagues. Let's look at a few.

# Displaying "Created At" Time

Let's display a "timestamp" for when a Review was created that looks like this: "Created on Nov 3, 2018".

First we have to update our model to track when documents are created and updated. This is so common, that there is an established convention in Mongoose called "timestamps" to accomplish it. We just have to add the additional option of `{ timestamps: true }` to the schema of our model and then all documents will have a `createdAt` and `updatedAt` Date fields added and set correctly by default.

> [action]
>
> Update `models/review.js` to use `timestamps`:
>
```js
const Review = mongoose.model('Review', {
  ...
}, {
  timestamps: true
});
```

Now we can display that `createdAt` timestamp in our html:

> [action]
>
> Update `view/reviews-show.handlebars` to include the `createdAt` field right below the `title`:
>
```HTML
...
>
<h1>{{review.title}}</h1>
<p class="text-muted">Created on: {{review.createdAt}}</p>
>
...
```

Now create a new review and see what is displayed.

Uh-oh - what you see there is called a Unix timestamp.

`Mon Nov 26 2018 12:57:54 GMT-0800 (Pacific Standard Time)`

It technically says the date and time when the review was created, but it isn't very readable for humans!

# Formatting Timestamps

We could parse it manually, but let's use a neat and very common js library called [moment](https://momentjs.com/) to parse that time into something more readable.

> [action]
>
> Install moment
>
```bash
$ npm install moment --save
```
>
> Now update the `/show` route in `controllers/reviews.js` to format the `createdAt` date into something we can read:
>
```js
//reviews.js
>
const Review = require('../models/review');
const Comment = require('../models/comment');
// Got to import the libary
const moment = require('moment');
module.exports = function (app) {
>
...
>
  // SHOW
  app.get('/movies/:movieId/reviews/:id', (req, res) => {
      // find review
      Review.findById(req.params.id).then(review => {
          let createdAt = review.createdAt;
          createdAt = moment(createdAt).format('MMMM Do YYYY, h:mm:ss a');
          review.createdAtFormatted = createdAt;
          // fetch its comments
          Comment.find({ reviewId: req.params.id }).then(comments => {
              comments.reverse();
              // respond with the template with both values
              res.render('reviews-show', { review: review, comments: comments })
          })
      }).catch((err) => {
          // catch errors
          console.log(err.message)
      });
  });
>
...
>
}
```

Finally, let's use our new property:

> [action]
>
> Update `views/reviews-show.handlebars` to include the `createdAtFormatted` property:
>
```html
<!-- views/reviews-show.handlebars -->
...
>
<small class="text-muted">Created on: {{event.createdAtFormatted}}</small>
>
...
```

Reload your browser and check the timestamp. Doesn't that look so much better?

> [info]
> Want it to display differently? Use the [moment documentation](https://momentjs.com/) to tweak the **format string** until you are happy with how the text displays.


# Adding a Footer

Now let's add a footer (Brought to you by mdbootstrap.com). Add the following code after the `{{{body}}}` tag but before the `</body>` tag or any `<script>` tags.

> [action]
>
> Add the following code after the `{{{body}}}` tag, but before the `</body>` tag or any `<script>` tags in `views/layouts/main.handlebars`.
>
```html
<!-- main.handlebars -->
...
>
<!-- Footer -->
<footer class="page-footer font-small blue pt-4">
  <div class="container-fluid text-center">
     <div class="row">
     <hr class="clearfix w-100 d-md-none pb-3">
     <div class="col-md-6 mb-md-0 mb-3">
         <h5 class="text-uppercase">Links</h5>
         <ul class="list-unstyled">
             <li><a href="#!">Link 1</a></li>
             <li><a href="#!">Link 2</a></li>
             <li><a href="#!">Link 3</a></li>
             <li><a href="#!">Link 4</a></li>
         </ul>
     </div>
     <div class="col-md-6 mb-md-0 mb-3">
         <h5 class="text-uppercase">Links</h5>
         <ul class="list-unstyled">
             <li><a href="#!">Link 1</a></li>
             <li><a href="#!">Link 2</a></li>
             <li><a href="#!">Link 3</a></li>
             <li><a href="#!">Link 4</a></li>
         </ul>
     </div>
     </div>
  </div>
  <div class="footer-copyright text-center py-3">© 2018 Copyright:
     <a href="https://mdbootstrap.com/education/bootstrap/"> MDBootstrap.com</a>
  </div>
</footer>
>
...
```

Beautiful. Make any visual changes you like.


# Adding a Bootstrap Theme

Now if we want to customize our style a little bit, we can add a free bootstrap theme. One popular website for finding these is called [Bootswatch](https://bootswatch.com/).

You can pick whichever you like, but for these instructions we'll use the "flatly" theme.

> [action]
> Go to [Bootswatch](https://bootswatch.com/) and select a theme from the `Themes` dropdown
>
> Select the dropdown of the name of the style you selected. For example, if you like the `Flatly` style, you should see a `Flatly` dropdown as the last nav bar item on the left
>
> Select the `bootstrap.min.css` option in the dropdown
> Copy the URL into your `<head>` tag to include the style you want. Make sure that this theme comes AFTER your link to bootstrap itself. Otherwise it won't work.
>
```html
<head>
  ...
>
  <link rel="stylesheet" href="https://bootswatch.com/4/flatly/bootstrap.min.css">
</head>
```

Now you can mess around with the CSS classes you have previously applied to achieve the look that you want!. Note that this will cause some areas of the app (i.e. trailers, home page, etc.) to look a _lot_ different from what we initially did. But all of it is adjustable with some additional CSS!

# Now Commit

```bash
$ git add .
$ git commit -m 'added createdAt, footer, and bootstrap theme'
$ git push
```
