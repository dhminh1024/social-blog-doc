## Design the endpoints

In this step, we are designing REST APIs for our application. The main question is **how to apply REST principles in design process?**

The very first step is identifying the objects which will be presented as resources, which are:

- auth: for authentication process
- blog: everything about blog (create, read, update, delete)
- friendship: actions between 2 users (send friend request, accept/decline, etc.)
- reaction: create emoji reaction to a blog or a review
- review: CRUD of reviews of blogs
- user: CRUD of user accounts

Next, it's time to decide the resource URIs which are endpoints of our RESTful services. Think about the relationship between resources and its sub-resources (e.g. Blog vs Review, User vs Friendship). Example

```javascript
/** 
 * @route GET api/blogs?page=1&limit=10 - Get blogs with pagination
 * @route GET api/users/:id/blogs?page=1&limit=20 - Get blogs from user
 * @route GET api/blogs/:id - Get blog detail
 * @route POST api/blogs - Create a new blog
 * @route PUT api/blogs/:id - Update a blog
 * @route DELETE api/blogs/:id - Remove a blog
 * @route GET api/blogs/:id/reviews?page=1&limit=10 - Get reviews from a blog
 * @route POST api/blogs/:id/reviews - Create new review for a blog
 * @route PUT api/blogs/:id/reviews/:id - Update review
 * @route DELETE api/blogs/:id/reviews/:id - Delete review
 */
```

Notice: **URIs should be nouns only**, don't use any verb or operation like:
```diff
// don't do this
-@route POST api/blogs/create_blog - Create a new blog
```

**Assign HTTP Methods**: A user can perform browse, create, update, or delete operations. Typically we assign:
  * `GET` for browsing 
  * `POST` for creating
  * `PUT` for updating
  * `DELETE` for removing

**Authorization**: if there are different roles of users in your system, you should pre-define who can see/do what. Example: we allow everyone to see the list of blogs so the endpoint will look like:

```javascript
/**
 * @route GET api/blogs?page=1&limit=10
 * @description Get blogs with pagination
 * @access Public
 */
```

But if user want to write a blog, they need to login, so the endpoint will be defined:

```javascript
/**
 * @route POST api/blogs
 * @description Create a new blog
 * @access Login required
 */
```

Let's design our endpoints:

- Create `/routes/auth.api.js`:
```javascript
const express = require("express");
const router = express.Router();

/**
 * @route POST api/auth/login
 * @description Login
 * @access Public
 */

/**
 * @route POST api/auth/login/facebook
 * @description Login with facebook
 * @access Public
 */

/**
 * @route POST api/auth/login/google
 * @description Login with google
 * @access Public
 */

module.exports = router;
```

- Create `/routes/blog.api.js`:
```javascript
const express = require("express");
const router = express.Router();

/**
 * @route GET api/blogs?page=1&limit=10
 * @description Get blogs with pagination
 * @access Public
 */

/**
 * @route GET api/blogs/:id
 * @description Get a single blog
 * @access Public
 */

/**
 * @route POST api/blogs
 * @description Create a new blog
 * @access Login required
 */

/**
 * @route PUT api/blogs/:id
 * @description Update a blog
 * @access Login required
 */

/**
 * @route DELETE api/blogs/:id
 * @description Delete a blog
 * @access Login required
 */

module.exports = router;
```

- Create `/routes/friendship.api.js`:
```javascript
const express = require("express");
const router = express.Router();

/**
 * @route POST api/friends/add/:id
 * @description Send a friend request to an user
 * @access Login required
 */

/**
 * @route DELETE api/friends/add/:id
 * @description Cancel a friend request to an user
 * @access Login required
 */

/**
 * @route GET api/friends/add
 * @description Get the list of friend requests that are sent by the user
 * @access Login required
 */

/**
 * @route GET api/friends/manage
 * @description Get the list of received friend requests
 * @access Login required
 */

/**
 * @route GET api/friends
 * @description Get the list of friends
 * @access Login required
 */

/**
 * @route POST api/friends/manage/:id
 * @description Accept a friend request from an user
 * @access Login required
 */

/**
 * @route DELETE api/friends/manage/:id
 * @description Decline a friend request from an user
 * @access Login required
 */

/**
 * @route DELETE api/friends/:id
 * @description Remove a friend
 * @access Login required
 */

module.exports = router;
```

- Create `/routes/reaction.api.js`:
```javascript
const express = require("express");
const router = express.Router();

/**
 * @route POST api/reactions
 * @description Save a reaction to blog or review
 * @access Login required
 */

module.exports = router;
```

- Create `/routes/review.api.js`:
```javascript
const express = require("express");
const router = express.Router();

/**
 * @route GET api/reviews/blogs/:id?page=1&limit=10
 * @description Get reviews of a blog with pagination
 * @access Public
 */

/**
 * @route POST api/reviews/blogs/:id
 * @description Create a new review for a blog
 * @access Login required
 */

/**
 * @route PUT api/reviews/:id
 * @description Update a review
 * @access Login required
 */

/**
 * @route DELETE api/reviews/:id
 * @description Delete a review
 * @access Login required
 */

module.exports = router;
```

- Create `/routes/user.api.js`:
```javascript
const express = require("express");
const router = express.Router();

/**
 * @route POST api/users
 * @description Register new user
 * @access Public
 */

/**
 * @route PUT api/users/
 * @description Update user profile
 * @access Login required
 */

/**
 * @route GET api/users/me
 * @description Get current user info
 * @access Login required
 */

/**
 * @route GET api/users?page=1&limit=10
 * @description Get users with pagination
 * @access Login required
 */

module.exports = router;
```

- Finally, in `/routes/index.js`:
```javascript
const express = require("express");
const router = express.Router();

// userApi
const userApi = require("./user.api");
router.use("/users", userApi);

// authApi
const authApi = require("./auth.api");
router.use("/auth", authApi);

// blogApi
const blogApi = require("./blog.api");
router.use("/blogs", blogApi);

// reviewApi
const reviewApi = require("./review.api");
router.use("/reviews", reviewApi);

// reactionApi
const reactionApi = require("./reaction.api");
router.use("/reactions", reactionApi);

// friendshipApi
const friendshipApi = require("./friendship.api");
router.use("/friends", friendshipApi);

module.exports = router;
```

We haven't done any coding task yet, but we have a plan to follow and to measure our process.

When you design your own app, remember that it doesn't need to be perfect. You can come back and modify it later on. However, always start with a plan.

Good job! [Back to instructions](/README.md)