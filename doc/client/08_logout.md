## Authentication - Log out

User can log out.

### Action and reducer

We should tell the server that the user want to logout. But keep it simple for now by removing user info and access token in the front-end side.

- In `src/redux/constants/auth.constants.js`, add:
  ```javascript
  //...
  export const LOGOUT = "AUTH.LOGOUT";
  ```
- In `src/redux/actions/auth.actions.js`, add:
  ```javascript
  //...
  const logout = () => (dispatch) => {
    delete api.defaults.headers.common["authorization"];
    localStorage.setItem("accessToken", "");
    dispatch({ type: types.LOGOUT, payload: null });
  };

  export const authActions = {
    register,
    loginRequest,
    logout,
  };
  ```
- In `src/redux/reducers/auth.reducer.js`, add:
  ```javascript
  const authReducer = (state = initialState, action) => {
    const { type, payload } = action;

    switch (type) {
      //...
      case types.LOGOUT:
        return {
          ...state,
          user: null,
          accessToken: null,
          isAuthenticated: false,
          loading: false,
        };
      default:
        return state;
    }
  };
  ```

### Integrate with the UI 

- In `PublicNavbar.js`, add:
  ```javascript
  //...
  import { useDispatch, useSelector } from "react-redux";
  import { authActions } from "redux/actions";
  //..
  const dispatch = useDispatch();
  const handleLogout = () => {
    dispatch(authActions.logout());
  };
  ```

### Evaluation

- User can log out by clicking on `Logout` on the navbar.

Good job! [Back to instructions](/README.md)