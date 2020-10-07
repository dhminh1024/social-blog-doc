## Friendship APIs

We need to implement quite a few actions for the friendship feature:
- User A can send a friend request to user B
- User B can accept or decline the request
- User A can cancel the friend request before User B takes action
- If user A and user B are friends, both of them can remove the friendship
- User can get a list of their friends, the sent requests or received requests.

- In `user.controller.js`, add:
```javascript
userController.sendFriendRequest = catchAsync(async (req, res, next) => {
  const userId = req.userId; // From
  const toUserId = req.params.id; // To

  const user = await User.findById(toUserId);
  if (!user) {
    return next(
      new AppError(400, "User not found", "Send Friend Request Error")
    );
  }

  let friendship = await Friendship.findOne({
    $or: [
      { from: toUserId, to: userId },
      { from: userId, to: toUserId },
    ],
  });
  if (!friendship) {
    await Friendship.create({
      from: userId,
      to: toUserId,
      status: "requesting",
    });
    return sendResponse(res, 200, true, null, null, "Request has ben sent");
  } else {
    switch (friendship.status) {
      case "requesting":
        if (friendship.from.equals(userId)) {
          return next(
            new AppError(
              400,
              "You have already sent a request to this user",
              "Add Friend Error"
            )
          );
        } else {
          return next(
            new AppError(
              400,
              "You have received a request from this user",
              "Add Friend Error"
            )
          );
        }
        break;
      case "accepted":
        return next(
          new AppError(400, "Users are already friend", "Add Friend Error")
        );
        break;
      case "removed":
      case "decline":
      case "cancel":
        friendship.from = userId;
        friendship.to = toUserId;
        friendship.status = "requesting";
        await friendship.save();
        return sendResponse(res, 200, true, null, null, "Request has ben sent");
        break;
      default:
        break;
    }
  }
});

userController.acceptFriendRequest = catchAsync(async (req, res, next) => {
  const userId = req.userId; // To
  const fromUserId = req.params.id; // From
  let friendship = await Friendship.findOne({
    from: fromUserId,
    to: userId,
    status: "requesting",
  });
  if (!friendship)
    return next(
      new AppError(404, "Friend Request not found", "Accept Request Error")
    );

  friendship.status = "accepted";
  await friendship.save();
  return sendResponse(
    res,
    200,
    true,
    null,
    null,
    "Friend request has been accepted"
  );
});

userController.declineFriendRequest = catchAsync(async (req, res, next) => {
  const userId = req.userId; // To
  const fromUserId = req.params.id; // From
  let friendship = await Friendship.findOne({
    from: fromUserId,
    to: userId,
    status: "requesting",
  });
  if (!friendship)
    return next(
      new AppError(404, "Request not found", "Decline Request Error")
    );

  friendship.status = "decline";
  await friendship.save();
  return sendResponse(
    res,
    200,
    true,
    null,
    null,
    "Friend request has been declined"
  );
});

userController.getFriendList = catchAsync(async (req, res, next) => {
  let { page, limit, sortBy, ...filter } = { ...req.query };
  const userId = req.userId;
  page = parseInt(page) || 1;
  limit = parseInt(limit) || 10;

  let friendList = await Friendship.find({
    $or: [{ from: userId }, { to: userId }],
    status: "accepted",
  });

  const friendIDs = friendList.map((friendship) => {
    if (friendship.from._id.equals(userId)) return friendship.to;
    return friendship.from;
  });

  const totalFriends = await User.countDocuments({
    ...filter,
    isDeleted: false,
    _id: { $in: friendIDs },
  });
  const totalPages = Math.ceil(totalFriends / limit);
  const offset = limit * (page - 1);

  let users = await User.find({ ...filter, _id: { $in: friendIDs } })
    .sort({ ...sortBy, createdAt: -1 })
    .skip(offset)
    .limit(limit);

  const promises = users.map(async (user) => {
    let temp = user.toJSON();
    temp.friendship = friendList.find((friendship) => {
      if (friendship.from.equals(user._id) || friendship.to.equals(user._id)) {
        return { status: friendship.status };
      }
      return false;
    });
    return temp;
  });
  const usersWithFriendship = await Promise.all(promises);

  return sendResponse(
    res,
    200,
    true,
    { users: usersWithFriendship, totalPages },
    null,
    null
  );
});

userController.getSentFriendRequestList = catchAsync(async (req, res, next) => {
  let { page, limit, sortBy, ...filter } = { ...req.query };
  const userId = req.userId;
  page = parseInt(page) || 1;
  limit = parseInt(limit) || 10;

  let requestList = await Friendship.find({
    from: userId,
    status: "requesting",
  });

  const recipientIDs = requestList.map((friendship) => {
    if (friendship.from._id.equals(userId)) return friendship.to;
    return friendship.from;
  });

  const totalRequests = await User.countDocuments({
    ...filter,
    isDeleted: false,
    _id: { $in: recipientIDs },
  });
  const totalPages = Math.ceil(totalRequests / limit);
  const offset = limit * (page - 1);

  let users = await User.find({ ...filter, _id: { $in: recipientIDs } })
    .sort({ ...sortBy, createdAt: -1 })
    .skip(offset)
    .limit(limit);

  const promises = users.map(async (user) => {
    let temp = user.toJSON();
    temp.friendship = requestList.find((friendship) => {
      if (friendship.from.equals(user._id) || friendship.to.equals(user._id)) {
        return { status: friendship.status };
      }
      return false;
    });
    return temp;
  });
  const usersWithFriendship = await Promise.all(promises);

  return sendResponse(
    res,
    200,
    true,
    { users: usersWithFriendship, totalPages },
    null,
    null
  );
});

userController.getReceivedFriendRequestList = catchAsync(
  async (req, res, next) => {
    let { page, limit, sortBy, ...filter } = { ...req.query };
    const userId = req.userId;
    page = parseInt(page) || 1;
    limit = parseInt(limit) || 10;

    let requestList = await Friendship.find({
      to: userId,
      status: "requesting",
    });

    const requesterIDs = requestList.map((friendship) => {
      if (friendship.from._id.equals(userId)) return friendship.to;
      return friendship.from;
    });

    const totalRequests = await User.countDocuments({
      ...filter,
      isDeleted: false,
      _id: { $in: requesterIDs },
    });
    const totalPages = Math.ceil(totalRequests / limit);
    const offset = limit * (page - 1);

    let users = await User.find({ ...filter, _id: { $in: requesterIDs } })
      .sort({ ...sortBy, createdAt: -1 })
      .skip(offset)
      .limit(limit);

    const promises = users.map(async (user) => {
      let temp = user.toJSON();
      temp.friendship = requestList.find((friendship) => {
        if (
          friendship.from.equals(user._id) ||
          friendship.to.equals(user._id)
        ) {
          return { status: friendship.status };
        }
        return false;
      });
      return temp;
    });
    const usersWithFriendship = await Promise.all(promises);

    return sendResponse(
      res,
      200,
      true,
      { users: usersWithFriendship, totalPages },
      null,
      null
    );
  }
);

userController.cancelFriendRequest = catchAsync(async (req, res, next) => {
  const userId = req.userId; // From
  const toUserId = req.params.id; // To
  let friendship = await Friendship.findOne({
    from: userId,
    to: toUserId,
    status: "requesting",
  });
  if (!friendship)
    return next(new AppError(404, "Request not found", "Cancel Request Error"));

  friendship.status = "cancel";
  await friendship.save();
  return sendResponse(
    res,
    200,
    true,
    null,
    null,
    "Friend request has been cancelled"
  );
});

userController.removeFriendship = catchAsync(async (req, res, next) => {
  const userId = req.userId;
  const toBeRemovedUserId = req.params.id;
  let friendship = await Friendship.findOne({
    $or: [
      { from: userId, to: toBeRemovedUserId },
      { from: toBeRemovedUserId, to: userId },
    ],
    status: "accepted",
  });
  if (!friendship)
    return next(new AppError(404, "Friend not found", "Remove Friend Error"));

  friendship.status = "removed";
  await friendship.save();
  return sendResponse(
    res,
    200,
    true,
    null,
    null,
    "Friendship has been removed"
  );
});
```

- In `friendship.api.js`:
```javascript
const express = require("express");
const router = express.Router();
const authMiddleware = require("../middlewares/authentication");
const validators = require("../middlewares/validators");
const userController = require("../controllers/user.controller");
const { body, param } = require("express-validator");

/**
 * @route POST api/friends/add/:id
 * @description Send a friend request to an user
 * @access Login required
 */
router.post(
  "/add/:id",
  authMiddleware.loginRequired,
  validators.validate([
    param("id").exists().isString().custom(validators.checkObjectId),
  ]),
  userController.sendFriendRequest
);

/**
 * @route DELETE api/friends/add/:id
 * @description Cancel a friend request to an user
 * @access Login required
 */
router.delete(
  "/add/:id",
  authMiddleware.loginRequired,
  validators.validate([
    param("id").exists().isString().custom(validators.checkObjectId),
  ]),
  userController.cancelFriendRequest
);

/**
 * @route GET api/friends/add
 * @description Get the list of friend requests that are sent by the user
 * @access Login required
 */
router.get(
  "/add",
  authMiddleware.loginRequired,
  userController.getSentFriendRequestList
);

/**
 * @route GET api/friends/manage
 * @description Get the list of received friend requests
 * @access Login required
 */
router.get(
  "/manage",
  authMiddleware.loginRequired,
  userController.getReceivedFriendRequestList
);

/**
 * @route GET api/friends
 * @description Get the list of friends
 * @access Login required
 */
router.get("/", authMiddleware.loginRequired, userController.getFriendList);

/**
 * @route POST api/friends/manage/:id
 * @description Accept a friend request from an user
 * @access Login required
 */
router.post(
  "/manage/:id",
  authMiddleware.loginRequired,
  validators.validate([
    param("id").exists().isString().custom(validators.checkObjectId),
  ]),
  userController.acceptFriendRequest
);

/**
 * @route DELETE api/friends/manage/:id
 * @description Decline a friend request from an user
 * @access Login required
 */
router.delete(
  "/manage/:id",
  authMiddleware.loginRequired,
  validators.validate([
    param("id").exists().isString().custom(validators.checkObjectId),
  ]),
  userController.declineFriendRequest
);

/**
 * @route DELETE api/friends/:id
 * @description Remove a friend
 * @access Login required
 */
router.delete(
  "/:id",
  authMiddleware.loginRequired,
  validators.validate([
    param("id").exists().isString().custom(validators.checkObjectId),
  ]),
  userController.removeFriendship
);

module.exports = router;
```

- You should pass all the tests now (maybe not the first two to register user A and B, remove them in the database and it should be fine).

**Congratulation! Well done!**

[Back to instructions](/README.md)