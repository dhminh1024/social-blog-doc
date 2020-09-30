## Persist login state on refresh

Now if you refersh the browser, you will have to log in again. Let's persist the login state. The idea is storing the `accessToken` in the local storage and send a request to the server to get the user info again on refresh.

### Action and reducer

- In `src/redux/constants/auth.constants.js`, add:
  ```javascript
  //...
  export const GET_CURRENT_USER_REQUEST = "AUTH.GET_CURRENT_USER_REQUEST";
  export const GET_CURRENT_USER_SUCCESS = "AUTH.GET_CURRENT_USER_SUCCESS";
  export const GET_CURRENT_USER_FAILURE = "AUTH.GET_CURRENT_USER_FAILURE";
  ```
- In `src/redux/actions/auth.actions.js`, add:
  ```javascript
  //...
  const getCurrentUser = (accessToken) => async (dispatch) => {
    dispatch({ type: types.GET_CURRENT_USER_REQUEST, payload: null });
    if (accessToken) {
      const bearerToken = "Bearer " + accessToken;
      api.defaults.headers.common["authorization"] = bearerToken;
    }
    try {
      const res = await api.get("/users/me");
      dispatch({ type: types.GET_CURRENT_USER_SUCCESS, payload: res.data.data });
    } catch (error) {
      dispatch({ type: types.GET_CURRENT_USER_FAILURE, payload: error });
    }
  };

  export const authActions = {
    register,
    loginRequest,
    logout,
    getCurrentUser,
  };
  ```
- In `src/redux/reducers/auth.reducer.js`, add:
  ```javascript
  const authReducer = (state = initialState, action) => {
    const { type, payload } = action;

    switch (type) {
      //...
      case types.GET_CURRENT_USER_REQUEST:
        return { ...state, loading: true };
      case types.GET_CURRENT_USER_SUCCESS:
        return {
          ...state,
          user: payload,
          isAuthenticated: true,
          loading: false,
        };
      case types.GET_CURRENT_USER_FAILURE:
        return { ...state, loading: false, isAuthenticated: false };

      default:
        return state;
    }
  };
  ```

### Integrate with the UI 

When user refreshes the browser, the app will initialize again. So we should put the the request to get current user in `App.js`:

```javascript
import { useDispatch, useSelector } from "react-redux";
import { authActions } from "redux/actions";
import { ClipLoader } from "react-spinners";
//...
function App() {
  const dispatch = useDispatch();
  const isAuthenticated = useSelector((state) => state.auth.isAuthenticated);
  useEffect(() => {
    const accessToken = localStorage.getItem("accessToken");
    if (accessToken && accessToken !== "undefined") {
      dispatch(authActions.getCurrentUser(accessToken));
    } else {
      dispatch(authActions.logout());
    }
  }, [dispatch]);

  return (
    <>
      {isAuthenticated === null ? (
        <div className="vh-100 vw-100 d-flex justify-content-center align-items-center">
          <ClipLoader color="#f86c6b" size={150} loading={true} />
        </div>
      ) : (
        <Router>
          <Routes />
        </Router>
      )}
    </>
  );
}

export default App;
```

### Evaluation

- Login with an email and password, then refresh the browser. You should still stay logged in.

Good job! [Back to instructions](/README.md)