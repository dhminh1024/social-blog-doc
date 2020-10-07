## Project structure

In the end, your project folder will look like:

```
|- bin/
|- controllers/
    |- auth.controller.js
    |- blog.controller.js
    |- reaction.controller.js
    |- review.controller.js
    |- user.controller.js
|- helpers/
    |- email.helper.js
    |- passport.helper.js
    |- utils.helper.js
|- middlewares/
    |- authentication.js
    |- validators.js
|- models/
    |- plugins/
    |- Blog.js
    |- Conversation.js
    |- Friendship.js
    |- GlobalMessage.js
    |- Message.js
    |- Reaction.js
    |- Review.js
    |- Template.js
    |- User.js
|- public/
|- routes/
    |- index.js
    |- auth.api.js
    |- blog.api.js
    |- conversation.api.js
    |- friendship.api.js
    |- reaction.api.js
    |- review.api.js
    |- socket.api.js
    |- user.api.js
|- .env
|- .gitignore
|- app.js
|- package.json
|- README.md
```

- `routes/` stores `.api` files that determine routes end point which is a URI and a specific HTTP request method (GET, POST, and so on). Each route have a handler function which is defined in `.controller` file.
- `models/` stores the schemas that map with the collections in your MongoDB.
- `middlewares/` stores Express middleware like Authentication or Validators.

Good job! [Back to instructions](/README.md)