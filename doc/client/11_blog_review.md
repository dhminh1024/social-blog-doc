## Write review for a blog

User can write reviews to a blog in the blog detail page.

### Action and reducer

- In `src/redux/constants/blog.constants.js`, add:
  ```javascript
  //...
  export const CREATE_REVIEW_REQUEST = "BLOG.CREATE_REVIEW_REQUEST";
  export const CREATE_REVIEW_SUCCESS = "BLOG.CREATE_REVIEW_SUCCESS";
  export const CREATE_REVIEW_FAILURE = "BLOG.CREATE_REVIEW_FAILURE";
  ```
- In `src/redux/actions/blog.actions.js`, add:
  ```javascript
  //...
  const createReview = (blogId, reviewText) => async (dispatch) => {
    dispatch({ type: types.CREATE_REVIEW_REQUEST, payload: null });
    try {
      const res = await api.post(`/reviews/blogs/${blogId}`, {
        content: reviewText,
      });
      dispatch({
        type: types.CREATE_REVIEW_SUCCESS,
        payload: res.data.data,
      });
    } catch (error) {
      console.log(error)
      dispatch({ type: types.CREATE_REVIEW_FAILURE, payload: error });
    }
  };

  export const blogActions = {
    blogsRequest,
    getSingleBlog,
    createReview,
  };
  ```
- In `src/redux/reducers/blog.reducer.js`, add:
  ```javascript
  const blogReducer = (state = initialState, action) => {
    const { type, payload } = action;

    switch (type) {
      //...
      case types.CREATE_REVIEW_REQUEST:
        return { ...state, submitLoading: true };
      case types.CREATE_REVIEW_SUCCESS:
        return {
          ...state,
          selectedBlog: {
            ...state.selectedBlog,
            reviews: [...state.selectedBlog.reviews, payload],
          },
          submitLoading: false,
        };
      case types.CREATE_REVIEW_FAILURE:
        return { ...state, submitLoading: false };

      default:
        return state;
    }
  };

  export default blogReducer;
  ```

### Integrate with the UI 

- Create `src/components/ReviewForm.js`:
  ```javascript
  import React from "react";
  import { Form, Button, Row, Col } from "react-bootstrap";

  const ReviewForm = ({
    reviewText,
    handleInputChange,
    handleSubmitReview,
    loading,
  }) => {
    return (
      <Form onSubmit={handleSubmitReview}>
        <Form.Group as={Row}>
          <Form.Label htmlFor="review" column sm="2">
            Review:
          </Form.Label>
          <Col sm="8">
            <Form.Control
              id="review"
              type="text"
              value={reviewText}
              onChange={handleInputChange}
            />
          </Col>
          {loading ? (
            <Button variant="primary" type="button" disabled>
              <span
                className="spinner-border spinner-border-sm"
                role="status"
                aria-hidden="true"
              ></span>
              Submitting...
            </Button>
          ) : (
            <Button type="submit" disabled={!reviewText}>
              Submit
            </Button>
          )}
        </Form.Group>
      </Form>
    );
  };

  export default ReviewForm;
  ```

- In `BlogDetailPage.js`, add:
  ```javascript
  //...
  const BlogDetailPage = () => {
    //..
    const isAuthenticated = useSelector((state) => state.auth.isAuthenticated);
    const submitLoading = useSelector((state) => state.blog.submitLoading);
    const [reviewText, setReviewText] = useState("");

    const handleInputChange = (e) => {
      setReviewText(e.target.value);
    };
    const handleSubmitReview = (e) => {
      e.preventDefault();
      dispatch(blogActions.createReview(params.id, reviewText));
      setReviewText("");
    };
    
    //...

    return (
      <>
        //...
        {loading ? (
          <div className="text-center">
            <ClipLoader color="#f86c6b" size={150} loading={loading} />
          </div>
        ) : (
          <>
            {blog && (
              //...
            )}

            {isAuthenticated && (
              <ReviewForm
                reviewText={reviewText}
                handleInputChange={handleInputChange}
                handleSubmitReview={handleSubmitReview}
                loading={submitLoading}
              />
            )}
          </>
        )}
      </>
    );
  };

  export default BlogDetailPage;
  ```

### Evaluation

- You can submit reviews to a blog
- When you hit enter to submit, the submit button turns into loading circle that prevents you to keep hitting enter and send the same review multiple times.

![](../images/B00_review.png)

Good job! [Back to instructions](/README.md)