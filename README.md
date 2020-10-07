# Social Blog App

Now is the best time to learn programming. You can choose from numerous learning resources, reference guides, or tutorials. Once you are familiar with the core principles and syntax, the next step is to build your own web application. However, at this point, the knowledge that you've acquired is fragmented. You've learned pieces of information here and there, but you haven't seen all that information used in one application. And here we are.

This is a documentation of how to create a middle-level web application in MERN stack **from scratch**. We cover every step in detail and walk you through the entire process starting from 0 lines of code and end up a couple of thousand lines of code. In the end, you will have a big example that helps you connect all of your knowledge together, and also hopefully inspires you to build your own app based on this app.

A demo is worth a thousand words: [Demo App](https://social-blog-cs.netlify.app/)


## Features

* User management and login flow
* State management with redux
* CRUD for blogs, reviews and emoji reactions
* Error handling and notification
* Relationship between users (managing friends)
* Chat room (global room and individual chat)
* Email Template and settings
* And more..

## Implementation

### Client side

We will use a already deployed backend API which you will learn soon (the server side):

```
https://social-blog-cs.herokuapp.com
```

**[API Documentation](./doc/api_doc.md)**

Let's get started:

**Project setup**

* [Setup a React App](/doc/client/00_setup_project.md)
* [Project Structure](/doc/client/01_project_structure.md)
* [Redux configuration](/doc/client/02_redux_config.md)
* [React Router and layouts](/doc/client/03_routes_layouts.md)

**Basic Features**

* [User can see a list of blogs in the home page](/doc/client/04_get_blogs.md)
* [Redirect on successful async redux action](/doc/client/05_redirect_to.md)
* [User can register a new account](/doc/client/06_register.md)
* [User can log in with an account](/doc/client/07_login.md)
* [User can log out](/doc/client/08_logout.md)
* [Persist login state on refresh](/doc/client/09_persist_login.md)
* [The Blog Detail Page](/doc/client/10_blog_detail.md)
* [User can write review to a blog](/doc/client/11_blog_review.md)
* [User can give an emoji reaction to a review or a blog](/doc/client/12_emoji_reaction.md)
* [Authenticated user can create/edit/delete blogs](/doc/client/13_blog_crud.md)


### Server side

**Project setup**

* [Setup with express-generator](/doc/server/00_setup_project.md)
* [Project structure](/doc/server/01_project_structure.md)
* [Design the endpoints](/doc/server/03_design_endpoints.md)
* [Design database schema](/doc/server/04_design_schema.md)
* [Adding Mongoose middlewares](/doc/server/05_mongoose_middlewares.md)
* [Create Postman collection](/doc/server/06_create_postman_collection.md)

**Basic Features**

* [Register user account and validators](/doc/server/07_register_user.md)
* [User can log in](/doc/server/08_login.md)
* [Blog APIs](/doc/server/09_blog_apis.md)
* [Review APIs](/doc/server/10_review_apis.md)
* [Reaction API](/doc/server/10_review_apis.md)
* [User APIs](/doc/server/12_user_apis.md)
* [Friendship APIs](/doc/server/13_friendship_apis.md)