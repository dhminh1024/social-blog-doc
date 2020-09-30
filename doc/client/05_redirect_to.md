## Redirect on successful async redux action

There are several ways to redirect a user to another route in a React component using `history.push()` or `<Redirect />`. But redirection in redux action can be a bit tricky.

Let's begin with action and reducer:

- In `src/redux/constants/route.constants.js`:
  ```javascript
  export const SET_REDIRECT_TO = "ROUTE.SET_REDIRECT_TO";
  export const REMOVE_REDIRECT_TO = "ROUTE.REMOVE_REDIRECT_TO";
  ```
- In `src/redux/actions/route.actions.js`:
  ```javascript
  import * as types from "../constants/route.constants";

  const redirect = (link) => ({ type: types.SET_REDIRECT_TO, payload: link });

  const removeRedirectTo = () => ({
    type: types.REMOVE_REDIRECT_TO,
    payload: null,
  });

  export const routeActions = {
    redirect,
    removeRedirectTo,
  };
  ```
- In `src/redux/reducers/route.reducer.js`:
  ```javascript
  //...
  const routeReducer = (state = initialState, action) => {
    const { type, payload } = action;

    switch (type) {
      case types.SET_REDIRECT_TO:
        return { ...state, redirectTo: payload };
      case types.REMOVE_REDIRECT_TO:
        return { ...state, redirectTo: null };
      default:
        return state;
    }
  };

  export default routeReducer;
  ```

That's it. To use this feature, example to redirect user to the login page after successfully register new account, you can `dispatch(routeActions.redirect("/login"));` in the register middleware.

```javascript
const register = (name, email, password, avatarUrl) => async (dispatch) => {
  dispatch({ type: types.REGISTER_REQUEST, payload: null });
  try {
    const res = await api.post("/users", { name, email, password, avatarUrl });
    dispatch({ type: types.REGISTER_SUCCESS, payload: res.data.data });
    dispatch(routeActions.redirect("/login"));
    toast.success(`Thank you for your registration, ${name}!`);
  } catch (error) {
    dispatch({ type: types.REGISTER_FAILURE, payload: error });
  }
};
```

And in the `RegisterPage.js`, add:

```javascript
const redirectTo = useSelector((state) => state.route.redirectTo);
useEffect(() => {
  if (redirectTo) {
    if (redirectTo === "__GO_BACK__") {
      history.goBack();
      dispatch(routeActions.removeRedirectTo());
    } else {
      history.push(redirectTo);
      dispatch(routeActions.removeRedirectTo());
    }
  }
}, [dispatch, history, redirectTo]);
```

Good job! [Back to instructions](/README.md)