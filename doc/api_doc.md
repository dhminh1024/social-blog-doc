# Social Blog API Documentation

```
BACKEND_API="https://social-blog-cs.herokuapp.com"
```

## Authentication

### Register

```
POST {{BACKEND_API}}/api/users
```

User can register an account per an email.

**Parameters**

Parameters in the `body` of the `POST Request`

|Name|Type|Description|
|:--|:--|:--|
|`avatarUrl`|String|URL of the user's avatar (optional)|
|`name`|String|Name of the user|
|`email`|String|User's email (unique)|
|`password`|String|Password for login with email|

**Example**

```json
{
  "avatarUrl":"https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
  "name": "Mia Do",
  "email": "mia2@cs.vn",
  "password": "123"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "user": {
      "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
      "friendCount": 0,
      "_id": "5f71aae24ac41a65cf11e431",
      "name": "Mia Do",
      "email": "mia1@cs.vn",
      "createdAt": "2020-09-28T09:20:34.688Z",
      "updatedAt": "2020-09-28T09:20:34.688Z",
      "__v": 0
    }
  },
  "message": "Create user successful"
}
```

### Login

```
POST {{BACKEND_API}}/api/auth/login
```

User can login with email and password.

**Body Parameters**

Parameters in the `body` of the `POST Request`

|Name|Type|Description|
|:--|:--|:--|
|`email`|String|User's email (unique)|
|`password`|String|Password for login with email|

**Example**

```json
{
  "email": "mia2@cs.vn",
  "password": "123"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "user": {
      "avatarUrl": "https://res.cloudinary.com/ddawlo98i/image/upload/v1599482230/EAb16qe3IE_tv8kqh.jpg",
      "friendCount": 2,
      "_id": "5f56297a68b8842768ddb83e",
      "name": "Minh Do",
      "email": "minhdh@cs.vn",
      "createdAt": "2020-09-07T12:37:14.199Z",
      "updatedAt": "2020-09-09T15:10:50.561Z",
      "__v": 0
    },
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZjU2Mjk3YTY4Yjg4NDI3NjhkZGI4M2UiLCJpYXQiOjE2MDEyODQ5MjcsImV4cCI6MTYwMTM3MTMyN30.fYoXxmx5cV4YrEhRvoze63yWvfeJIRnGskWCwRfr4JQ"
  },
  "message": "Login successful"
}
```

## User Model

### Get current user

```
GET {{BACKEND_API}}/api/users/me
```

An access token in the header's authorization is required. The user's info will be responsed. This API is used to keep the user logged in after refreshing the browser.

**Headers**: Authorization: Bearer Token

**Example**

```json
Authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1ZjU2Mjk3YTY4Yjg4NDI3NjhkZGI4M2UiLCJpYXQiOjE2MDEyODQ5MjcsImV4cCI6MTYwMTM3MTMyN30.fYoXxmx5cV4YrEhRvoze63yWvfeJIRnGskWCwRfr4JQ"
```

**Response**

```json
{
  "success": true,
  "data": {
    "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
    "friendCount": 0,
    "_id": "5f71aaae7ce6cd65bd8fb5e3",
    "name": "Mia Do",
    "email": "mia@cs.vn",
    "createdAt": "2020-09-28T09:19:42.198Z",
    "updatedAt": "2020-09-28T09:19:42.198Z",
    "__v": 0
  },
  "message": "Get current user successful"
}
```

### Get list of users

```
GET {{BACKEND_API}}/api/users
```

Return the list of users with pagination.

Searching can be applied by following the syntax of MongoDB. Example to search for all user that has `mia` in their name, put this in the query parameters:

`name[$regex]=mia&name[$options]=i`

You can ignore `name[$options]=i` if you want to match exactly the search value `mia` (`i` means ignorecase).

Sorting can be defined by the query params too. Example to sort by name in ascending way, put this in the query paramters:

`sortBy[name]=1`

For descending, change `1` to `-1`.

**Headers**: Authorization: Bearer Token

**Query Parameters**

|Name|Type|Description|Example
|:--|:--|:--|:--|
|`page`|Number|# of current page, default:1| `page=1` |
|`limit`|Number|# of item per page, default: 10| `limit=10` |
|`field_name`|String|Search for documents that have `field_name` value that matches with the search value| `name[$regex]=minh&name[$options]=i` |
|`sortBy`|String|Sort by a specific field| `sortBy[email]=1` |

**Example**

`/api/users?page=1&limit=10&name[$regex]=mi&name[$options]=i&sortBy[email]=1`

**Response**

```json
{
  "success": true,
  "data": {
    "users": [
      {
        ...
        "_id": "5f561d83c633ca2759223723",
        "name": "Miranda Lowe",
        "email": "brendon57@hotmail.com",
        ...
      },
      {
        ...
        "_id": "5f5e46dbeadd56afff8dc5dc",
        "name": "Minh",
        "email": "dhminh1024@gmail.com",
        ...
      },
      {
        ...
        "_id": "5f5a555feb594378ffa6bfa5",
        "name": "Hai Minh Do",
        "email": "haiminh101@yahoo.com",
        ...
      },
      {
        ...
        "_id": "5f71aae24ac41a65cf11e431",
        "name": "Mia Do",
        "email": "mia1@cs.vn",
        ...
      }
    ],
    "totalPages": 1
  }
}
```

### Update profile

```
PUT {{BACKEND_API}}/api/users
```

Edit name or avatar URL of the current user. Login required.

**Headers**: Authorization: Bearer Token

**Body Parameters**

|Name|Type|Description|
|:--|:--|:--|
|`name`|String|Name of the user|
|`avatarUrl`|String|The avatar URL|

**Response**: The new user object

```json
{
  "success": true,
  "data": {
    "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
    "friendCount": 0,
    "_id": "5f71aaae7ce6cd65bd8fb5e3",
    "name": "Super Man",
    "email": "mia@cs.vn",
    "createdAt": "2020-09-28T09:19:42.198Z",
    "updatedAt": "2020-09-28T11:00:45.046Z",
    "__v": 0
  },
  "message": "Update Profile successfully"
}
```

## Blog Model

### Create a new blog

```
POST {{BACKEND_API}}/api/blogs
```

**Headers**: Authorization: Bearer Token

**Body Parameters**

|Name|Type|Description|
|:--|:--|:--|
|`title`|String|Title of the blog|
|`content`|String|Content of the blog|
|`images`|Array|List of image URLs|

**Example**

```json
{
  "title":"My first blog",
  "content":"This is a blog",
  "images":[
    "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/2KSzKN1obI_jkcfc8.jpg",
    "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/EAb16qe3IE_dabom2.jpg"
  ]
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "reactions": {
      "laugh": 0,
      "sad": 0,
      "like": 0,
      "love": 0,
      "angry": 0
    },
    "images": [
      "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/2KSzKN1obI_jkcfc8.jpg",
      "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/EAb16qe3IE_dabom2.jpg"
    ],
    "reviewCount": 0,
    "isDeleted": false,
    "_id": "5f71bba34ac41a65cf11e434",
    "title": "My first blog",
    "content": "This is a blog",
    "author": "5f71aaae7ce6cd65bd8fb5e3",
    "createdAt": "2020-09-28T10:32:03.266Z",
    "updatedAt": "2020-09-28T10:32:03.266Z",
    "__v": 0
  },
  "message": "Create new blog successful"
}
```

### Get a single blog

```
GET {{BACKEND_API}}/api/blogs/:blog_id
```

The app allows public users can see and read blogs. So this API doesn't require login.

**Example**

```
{{BACKEND_API}}/api/blogs/5f71bba34ac41a65cf11e434
```

**Response**

```json
{
  "success": true,
  "data": {
    "reactions": {
      "laugh": 0,
      "sad": 0,
      "like": 0,
      "love": 0,
      "angry": 0
    },
    "images": [
      "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/2KSzKN1obI_jkcfc8.jpg",
      "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/EAb16qe3IE_dabom2.jpg"
    ],
    "reviewCount": 0,
    "_id": "5f71bba34ac41a65cf11e434",
    "title": "My first blog",
    "content": "This is a blog",
    "author": {
      "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
      "friendCount": 0,
      "_id": "5f71aaae7ce6cd65bd8fb5e3",
      "name": "Mia Do",
      "email": "mia@cs.vn",
      "createdAt": "2020-09-28T09:19:42.198Z",
      "updatedAt": "2020-09-28T09:19:42.198Z",
      "__v": 0
    },
    "createdAt": "2020-09-28T10:32:03.266Z",
    "updatedAt": "2020-09-28T10:32:03.266Z",
    "__v": 0,
    "reviews": []
  }
}
```

### Get list of blogs

```
GET {{BACKEND_API}}/api/blogs
```

Return the list of blogs with pagination. No login required.

Searching can be applied by following the syntax of MongoDB. Example to search for all blog that has `blog` in their title, put this in the query parameters:

`title[$regex]=blog&title[$options]=i`

You can ignore `title[$options]=i` if you want to match exactly the search value `blog` (`i` means ignorecase).

Sorting can be defined by the query params too. Example to sort by `title` in ascending way, put this in the query paramters:

`sortBy[title]=1`

For descending, change `1` to `-1`.

**Query Parameters**

|Name|Type|Description|Example
|:--|:--|:--|:--|
|`page`|Number|# of current page, default:1| `page=1` |
|`limit`|Number|# of item per page, default: 10| `limit=10` |
|`field_name`|String|Search for documents that have `field_name` value that matches with the search value| `title[$regex]=blog&title[$options]=i` |
|`sortBy`|String|Sort by a specific field| `sortBy[title]=1` |

**Example**

`{{BACKEND_API}}/api/blogs?page=1&limit=10&title[$regex]=blog&title[$options]=i&sortBy[title]=1`

**Response**

```json
{
  "success": true,
  "data": {
    "blogs": [
      {
        "reactions": {
          "laugh": 0,
          "sad": 0,
          "like": 0,
          "love": 0,
          "angry": 0
        },
        "images": [
          "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/2KSzKN1obI_jkcfc8.jpg",
          "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/EAb16qe3IE_dabom2.jpg"
        ],
        "reviewCount": 0,
        "_id": "5f71bba34ac41a65cf11e434",
        "title": "My first blog",
        "content": "This is a blog",
        "author": {
          "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
          "friendCount": 0,
          "_id": "5f71aaae7ce6cd65bd8fb5e3",
          "name": "Super Man",
          "email": "mia@cs.vn",
          "createdAt": "2020-09-28T09:19:42.198Z",
          "updatedAt": "2020-09-28T11:00:45.046Z",
          "__v": 0
        },
        "createdAt": "2020-09-28T10:32:03.266Z",
        "updatedAt": "2020-09-28T10:32:03.266Z",
        "__v": 0
      },
      {
        "reactions": {
          "laugh": 0,
          "sad": 0,
          "like": 0,
          "love": 0,
          "angry": 0
        },
        "images": [
          "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/2KSzKN1obI_jkcfc8.jpg",
          "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/EAb16qe3IE_dabom2.jpg"
        ],
        "reviewCount": 0,
        "_id": "5f71babd4ac41a65cf11e433",
        "title": "My first blog",
        "content": "This is a blog",
        "author": {
          "avatarUrl": "",
          "friendCount": 0,
          "_id": "5f71ba954ac41a65cf11e432",
          "name": "Minh",
          "email": "minh@cs.vn",
          "createdAt": "2020-09-28T10:27:33.936Z",
          "updatedAt": "2020-09-28T10:27:33.936Z",
          "__v": 0
        },
        "createdAt": "2020-09-28T10:28:13.094Z",
        "updatedAt": "2020-09-28T10:28:13.094Z",
        "__v": 0
      }
    ],
    "totalPages": 1
  }
}
```

### Update a single blog

```
PUT {{BACKEND_API}}/api/blogs/:blog_id
```

Login required and only author of the blog can edit it.

**Headers**: Authorization: Bearer Token

**Body Parameters**

|Name|Type|Description|
|:--|:--|:--|
|`title`|String|Title of the blog|
|`content`|String|Content of the blog|
|`images`|Array|List of image URLs|

**Response**

```json
{
  "success": true,
  "data": {
    "reactions": {
      "laugh": 0,
      "sad": 0,
      "like": 0,
      "love": 0,
      "angry": 0
    },
    "images": [
      "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/2KSzKN1obI_jkcfc8.jpg",
      "https://res.cloudinary.com/ddawlo98i/image/upload/v1601288885/EAb16qe3IE_dabom2.jpg"
    ],
    "reviewCount": 0,
    "_id": "5f71bba34ac41a65cf11e434",
    "title": "My first blog",
    "content": "This is a blog",
    "author": {
      "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
      "friendCount": 0,
      "_id": "5f71aaae7ce6cd65bd8fb5e3",
      "name": "Mia Do",
      "email": "mia@cs.vn",
      "createdAt": "2020-09-28T09:19:42.198Z",
      "updatedAt": "2020-09-28T09:19:42.198Z",
      "__v": 0
    },
    "createdAt": "2020-09-28T10:32:03.266Z",
    "updatedAt": "2020-09-28T10:32:03.266Z",
    "__v": 0,
    "reviews": []
  }
}
```

### Delete a single blog

```
DELETE {{BACKEND_API}}/api/blogs/:blog_id
```

Login required and only author of the blog can delete it.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Delete Blog successful"
}
```

## Review Model

User can write reviews for a blog.

### Get list of reviews of a blog

```
GET {{BACKEND_API}}/api/reviews/blogs/:blog_id
```

Return the list of reviews with pagination. No login required.

**Query Parameters**

|Name|Type|Description|Example
|:--|:--|:--|:--|
|`page`|Number|# of current page, default:1| `page=1` |
|`limit`|Number|# of item per page, default: 10| `limit=10` |

**Example**

`{{BACKEND_API}}/api/reviews/blogs/5f4a53dfc343510d6e033a13?page=1&limit=10`

**Response**

```json
{
  "success": true,
  "data": {
    "reviews": [
      {
        "reactions": {
          "laugh": 0,
          "sad": 0,
          "like": 0,
          "love": 0,
          "angry": 0
        },
        "_id": "5f71ce7d4ac41a65cf11e436",
        "user": "5f71aaae7ce6cd65bd8fb5e3",
        "blog": "5f4a53dfc343510d6e033a13",
        "content": "I like it",
        "createdAt": "2020-09-28T11:52:29.517Z",
        "updatedAt": "2020-09-28T11:52:29.517Z",
        "__v": 0
      }
    ],
    "totalPages": 1
  }
}
```

### Create a review

```
POST {{BACKEND_API}}/api/reviews/blogs/:blog_id
```

**Headers**: Authorization: Bearer Token

**Body Parameters**

|Name|Type|Description|
|:--|:--|:--|
|`content`|String|Content of the review|

**Response**

```json
{
  "success": true,
  "data": {
    "reactions": {
      "laugh": 0,
      "sad": 0,
      "like": 0,
      "love": 0,
      "angry": 0
    },
    "_id": "5f71ce7d4ac41a65cf11e436",
    "user": {
      "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
      "friendCount": 0,
      "_id": "5f71aaae7ce6cd65bd8fb5e3",
      "name": "Mia Do",
      "email": "mia@cs.vn",
      "createdAt": "2020-09-28T09:19:42.198Z",
      "updatedAt": "2020-09-28T11:00:45.046Z",
      "__v": 0
    },
    "blog": "5f4a53dfc343510d6e033a13",
    "content": "I like it",
    "createdAt": "2020-09-28T11:52:29.517Z",
    "updatedAt": "2020-09-28T11:52:29.517Z",
    "__v": 0
  },
  "message": "Create new review successful"
}
```

### Update a review

```
PUT {{BACKEND_API}}/api/reviews/:review_id
```

**Headers**: Authorization: Bearer Token

**Body Parameters**

|Name|Type|Description|
|:--|:--|:--|
|`content`|String|Content of the review|

**Response**

```json
{
  "success": true,
  "data": {
    "reactions": {
      "laugh": 0,
      "sad": 0,
      "like": 0,
      "love": 0,
      "angry": 0
    },
    "_id": "5f71ce7d4ac41a65cf11e436",
    "user": "5f71aaae7ce6cd65bd8fb5e3",
    "blog": "5f4a53dfc343510d6e033a13",
    "content": "I like it!!",
    "createdAt": "2020-09-28T11:52:29.517Z",
    "updatedAt": "2020-09-28T12:00:16.479Z",
    "__v": 0
  },
  "message": "Update successful"
}
```

### Delete a review

```
DELETE {{BACKEND_API}}/api/reviews/:review_id
```

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Delete successful"
}
```

## Reaction model

```
POST {{BACKEND_API}}/api/reactions
```

User can react with "laugh", "sad", "like", "love", or "angry" to a blog or a review. The second time user send the same reaction means removing it.

**Headers**: Authorization: Bearer Token

**Body Parameters**

|Name|Type|Description|
|:--|:--|:--|
|`targetType`|String|Blog or Review|
|`targetId`|String|The ID of the blog or the review|
|`emoji`|String|"laugh", "sad", "like", "love", or "angry"|

**Example**

```json
{
  "targetType":"Blog",
  "targetId": "5f71d41e6588b96a02b0d38e",
  "emoji":"love"
}
```

**Response**

```json
{
    "success": true,
    "data": {
        "laugh": 0,
        "sad": 0,
        "like": 0,
        "love": 1,
        "angry": 0
    },
    "message": "Added reaction"
}
```

## Friendship Model

* User A can send a add friend request to user B
* User A can cancel the request
* User B can accept/decline the request
* If two users are friends, both can remove the friendship
* User can see a list of sent requests
* User can see a list of received requests
* User can see a list of his/her friends

### Send a friend request

```
POST {{BACKEND_API}}/api/friends/add/:to_user_id
```

Send a friend request to an user. Login required.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Request has ben sent"
}
```

### Cancel a request

```
DELETE {{BACKEND_API}}/api/friends/add/:to_user_id
```

Cancel a friend request to an user. Login required.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Friend request has been cancelled"
}
```

### Accept a friend request

```
POST {{BACKEND_API}}/api/friends/manage/:from_user_id
```

Accept a friend request from an user. Login required.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Friend request has been accepted"
}
```

### Decline a friend request

```
DELETE {{BACKEND_API}}/api/friends/manage/:from_user_id
```

Decline a friend request from an user. Login required.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Friend request has been declined"
}
```

### Remove a friend

```
DELETE {{BACKEND_API}}/api/friends/:friend_user_id
```

Remove a friend. Login required.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
  "success": true,
  "message": "Friendship has been removed"
}
```

### Get the list of sent requests

```
GET {{BACKEND_API}}/api/friends/add
```

Get the list of friend requests that are sent by the user. Login required.

Searching and sorting is similar like Get list of users.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
    "success": true,
    "data": {
        "users": [
            {
                "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
                "friendCount": 0,
                "_id": "5f71aaae7ce6cd65bd8fb5e3",
                "name": "Mia Do",
                "email": "mia@cs.vn",
                "createdAt": "2020-09-28T09:19:42.198Z",
                "updatedAt": "2020-09-28T12:55:17.627Z",
                "__v": 0,
                "friendship": {
                    "_id": "5f71d7de6588b96a02b0d391",
                    "from": "5f561d83c633ca2759223723",
                    "to": "5f71aaae7ce6cd65bd8fb5e3",
                    "status": "requesting",
                    "createdAt": "2020-09-28T12:32:30.893Z",
                    "updatedAt": "2020-09-28T12:32:30.893Z",
                    "__v": 0
                }
            }
        ],
        "totalPages": 1
    }
}
```

### Get the list of received requests

```
GET {{BACKEND_API}}/api/friends/manage
```

Get the list of received friend requests. Login required.

Searching and sorting is similar like Get list of users.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
    "success": true,
    "data": {
        "users": [
            {
                "avatarUrl": "https://lh3.googleusercontent.com/a-/AOh14GhnQ6aJ2YnaevyJzhii-qGws6Y17w-cwWLqF5iP",
                "friendCount": 0,
                "_id": "5f561d83c633ca2759223723",
                "name": "Miranda Lowe",
                "email": "brendon57@hotmail.com",
                "createdAt": "2020-09-28T09:19:42.198Z",
                "updatedAt": "2020-09-28T12:55:17.627Z",
                "__v": 0,
                "friendship": {
                    "_id": "5f71d7de6588b96a02b0d391",
                    "from": "5f561d83c633ca2759223723",
                    "to": "5f71aaae7ce6cd65bd8fb5e3",
                    "status": "requesting",
                    "createdAt": "2020-09-28T12:32:30.893Z",
                    "updatedAt": "2020-09-28T12:32:30.893Z",
                    "__v": 0
                }
            }
        ],
        "totalPages": 1
    }
}
```

### Get the list of friends

```
GET {{BACKEND_API}}/api/friends
```

Get the list of friends. Login required.

Searching and sorting is similar like Get list of users.

**Headers**: Authorization: Bearer Token

**Response**

```json
{
    "success": true,
    "data": {
        "users": [
            {
                "avatarUrl": "https://s3.amazonaws.com/uifaces/faces/twitter/mohanrohith/128.jpg",
                "friendCount": 2,
                "_id": "5f561d83c633ca2759223724",
                "name": "Mitchell Windler",
                "email": "nicklaus_barrows89@gmail.com",
                "createdAt": "2020-09-07T11:46:11.697Z",
                "updatedAt": "2020-09-07T11:46:12.209Z",
                "__v": 0,
                "friendship": {
                    "_id": "5f561d84c633ca275922372d",
                    "from": "5f561d83c633ca2759223723",
                    "to": "5f561d83c633ca2759223724",
                    "status": "accepted",
                    "createdAt": "2020-09-07T11:46:12.192Z",
                    "updatedAt": "2020-09-07T11:46:12.192Z",
                    "__v": 0
                }
            },
            {
                "avatarUrl": "https://s3.amazonaws.com/uifaces/faces/twitter/adhamdannaway/128.jpg",
                "friendCount": 2,
                "_id": "5f561d83c633ca2759223722",
                "name": "Lillie Daugherty",
                "email": "easton2@gmail.com",
                "createdAt": "2020-09-07T11:46:11.522Z",
                "updatedAt": "2020-09-07T11:46:12.234Z",
                "__v": 0,
                "friendship": {
                    "_id": "5f561d84c633ca275922372c",
                    "from": "5f561d83c633ca2759223722",
                    "to": "5f561d83c633ca2759223723",
                    "status": "accepted",
                    "createdAt": "2020-09-07T11:46:12.153Z",
                    "updatedAt": "2020-09-07T11:46:12.153Z",
                    "__v": 0
                }
            }
        ],
        "totalPages": 1
    }
}
```




