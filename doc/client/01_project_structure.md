## Project Structure

How to structure large React apps into folders and files is a highly opinionated topic. There is no right way to do it. However, let set up conventions to structure this React app:

- Put your stateless components in `/src/components/`, because we plan to **"reuse"** these components. Tip: if your component needs to access to the global store, put it in `/src/containers/`
- Keep all the components that manage the states, and logics in `/src/containers/`
- `src/redux/actions`: each file in this folder is related to a set of actions, e.g. `auth.actions.js`. **Rule**: only put API Connection in actions files.
- `src/redux/reducers`: set of reducers for managing the global store. They are combined in `index.js`.
- `src/redux/constants`: set of types of actions
- `src/redux/store.js`: configuration of the store

In the end, you will have a project structure like this:

  ```
  |- src\
    |- components\
      |- BlogCard.js
      |- PaginationBar.js
      |- ReactionList.js
      |- ReviewForm.js
      |- ReviewList.js
      |- SearchForm.js
    |- containers\
      |- AddEditBlogPage.js
      |- Admin\
      |- BlogDetailPage.js
      |- HomePage.js
      |- layouts\
      |- LoginPage.js
      |- PublicNavbar.js
      |- RegisterPage.js
      |- Routes\
    |- images\
    |- redux\
        |- actions\
        |- constants\
        |- reducers\
        |- api.js
        |- store.js
    |- App.css
    |- App.js
    |-index.js
  |- .env
  |- .gitignore
  |- package.json
  |- jsconfig.json
  |- README.md
  ```

- You can use command line to create folders quickly, e.g.

  ```bash
  $ mkdir src/components src/containers src/containers/Routes src/redux src/redux/actions src/redux/constants src/redux/reducers
  ```

**Optional**

To focus on the main concepts (ReactJS, Redux, ExpressJS, etc.), we will use pre-defined CSS classes of Bootstrap and `react-bootstrap`, and put the customized CSS styles in `App.css`. However, feel free to read more about the other ways to organize your CSS styles, e.g.:

- **[CSS Module in create-react-app](https://create-react-app.dev/docs/adding-a-css-modules-stylesheet)**: After you have setup your application with create-react-app (e.g. npx create-react-app my-app), you don't need to install anything else to make CSS modules work. However, you have to give your CSS files the "module" prefix prior the extension:

  ```css
  /* In src/components/List/List.module.css */
  .avatar {
    width: 128px;
    height: 128px;
  }

  .text-grey {
    color: #657786;
  }
  ```

  ```javascript
  // In src/components/List/index.js
  import React from "react";
  import styles from "./List.module.css";

  const List = () => {
    // ...
    return (
      // Then you can use css in your JSX like this
      <span className={styles["text-grey"]}>...</span>

      // If you want to concatenate with other classes
      <span className={`${styles["text-grey"]} mr-2`}>...</span>
      // ...
    })
  }

  export default List;
  ```

  In case of the `avatar` style, you can retrieve it with `styles.avatar` too. However, for the other styles with dashes you need to retrieve them with strings from the object, e.g. `styles["text-grey"]`. 


  Good job! [Back to instructions](/README.md)