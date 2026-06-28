---
title: "1.9 User and video model with hooks and JWT"
datePublished: 2026-06-28T15:50:07.098Z
cuid: cmqxyt9lx00000akn0ufe36h9
slug: 1-9-user-and-video-model-with-hooks-and-jwt

---

In this guide, we will dive deep into creating advanced **User** and **Video** models using Mongoose, exploring complex data couplings, securing passwords with `bcrypt`, and implementing a robust token authentication system using JSON Web Tokens (JWT).

* * *

## 1\. Setting Up the Database Models

To maintain professional code standards, it is common practice to suffix model files with `.model.js` (e.g., `user.model.js` and `video.model.js`). This makes the codebase highly maintainable and easily navigable as it grows.

### The User Model (`user.model.js`)

The user model tracks foundational information such as unique user credentials, personal details, and behavioral history.

```javascript
import mongoose, { Schema } from "mongoose";
import bcrypt from "bcrypt";
import jwt from "jsonwebtoken";

const userSchema = new Schema(
  {
    username: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
      trim: true,
      index: true // Highly optimized for database searching
    },
    email: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
      trim: true
    },
    fullName: {
      type: String,
      required: true,
      trim: true,
      index: true
    },
    avatar: {
      type: String, // Third-party hosting URL (e.g., Cloudinary or AWS)
      required: true
    },
    coverImage: {
      type: String
    },
    watchHistory: [
      {
        type: Schema.Types.ObjectId,
        ref: "Video"
      }
    ],
    password: {
      type: String,
      required: [true, "Password is required"]
    },
    refreshToken: {
      type: String
    }
  },
  { timestamps: true }
);

export const User = mongoose.model("User", userSchema);

```

> **Database Optimization Tip:** Setting `index: true` on fields like `username` or `fullName` makes them highly searchable and optimized at the database level. While indexing adds minor overhead, it drastically increases query speeds for fields frequently used in search bars.

### The Video Model (`video.model.js`)

The video model handles media URLs, metadata, and views, and establishes a direct relationship with the `User` model via ownership.

```javascript
import mongoose, { Schema } from "mongoose";
import mongooseAggregatePaginate from "mongoose-aggregate-paginate-v2";

const videoSchema = new Schema(
  {
    videoFile: {
      type: String, // Third-party asset URL
      required: true
    },
    thumbnail: {
      type: String, 
      required: true
    },
    title: {
      type: String,
      required: true
    },
    description: {
      type: String,
      required: true
    },
    duration: {
      type: Number, // Extracted directly from asset host metadata
      required: true
    },
    views: {
      type: Number,
      default: 0
    },
    isPublished: {
      type: Boolean,
      default: true
    },
    owner: {
      type: Schema.Types.ObjectId,
      ref: "User"
    }
  },
  { timestamps: true }
);

// Injecting the Aggregation Plugin
videoSchema.plugin(mongooseAggregatePaginate);

export const Video = mongoose.model("Video", videoSchema);

```

* * *

## 2\. Unlocking Production-Grade Aggregation Pipes

Storing data cleanly is only half the battle. When processing rich structures like a user's `watchHistory` alongside relational media assets, standard queries fall short.

To bridge this gap, we inject the `mongoose-aggregate-paginate-v2` plugin directly into our schemas. This allows the backend to execute complex **Aggregation Pipelines**. Instead of running multiple taxing queries back-and-forth, aggregation pipelines enable the database to perform high-level processing, filtering, and pagination natively before returning data to the API layer.

* * *

## 3\. Securing Passwords with Mongoose Hooks

Storing passwords in clear, human-readable text inside a database is an immense security flaw. To prevent leaks, passwords must be securely hashed before they ever hit the database disks.

Using Mongoose **Pre-Hooks (**`pre`**)**, we can intercept the data saving process to automatically encrypt passwords.

### Pre-Save Hashing Logic

```javascript
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();

  this.password = await bcrypt.hash(this.password, 10);
  next();
});

```

### Key Considerations:

*   **The** `this` **Context:** We avoid ES6 arrow functions here because arrow functions do not bind their own `this` context. Standard anonymous functions ensure `this` correctly points to the current user document being saved.
    
*   **Conditional Execution:** Without the `isModified` check, the pre-hook would re-hash the password every time a user updates unrelated fields (like updating their profile avatar), locking them out of their account.
    

* * *

## 4\. Injecting Custom Instance Methods

Mongoose allows developers to attach custom functionality directly onto a schema using the `methods` object. This centralizes core database logic, making authentication controllers incredibly clean.

### Verifying Passwords

To securely compare an incoming plaintext login password against our salted hash in the database, we use a custom method:

```javascript
userSchema.methods.isPasswordCorrect = async function (password) {
  return await bcrypt.compare(password, this.password);
};

```

* * *

## 5\. Dual-Token Authentication Structure

For optimal security, production apps typically isolate user sessions using two distinct JSON Web Tokens (JWT): an **Access Token** and a **Refresh Token**.

JWTs consist of three parts: a Header, a Payload (the encrypted data), and a Signature verified via an environment-level secret key.

*   **Access Token:** Short-lived (e.g., 1 day) and sent with API requests to authenticate endpoints. It is never stored in the database.
    
*   **Refresh Token:** Long-lived (e.g., 10 days) and stored securely in both the database and client-side cookies. When an access token expires, the refresh token verifies the identity to seamlessly issue a new access token without forcing a manual re-login.
    

### Dynamic Token Generation Methods

```javascript
// Method to generate short-lived Access Tokens
userSchema.methods.generateAccessToken = function () {
  return jwt.sign(
    {
      _id: this._id,
      email: this.email,
      username: this.username,
      fullName: this.fullName
    },
    process.env.ACCESS_TOKEN_SECRET,
    {
      expiresIn: process.env.ACCESS_TOKEN_EXPIRY
    }
  );
};

// Method to generate long-lived Refresh Tokens (contains minimal payload)
userSchema.methods.generateRefreshToken = function () {
  return jwt.sign(
    {
      _id: this._id
    },
    process.env.REFRESH_TOKEN_SECRET,
    {
      expiresIn: process.env.REFRESH_TOKEN_EXPIRY
    }
  );
};

```

* * *

## Summary Workflow

By decoupling and configuring schemas this way, managing authenticated user states becomes incredibly intuitive. When a user creates an account or authenticates:

1.  The password is dynamically checked, hashed, and safely stored via the `pre` middleware.
    
2.  Custom methods verify incoming credentials with zero leakage risk.
    
3.  Access and refresh tokens are cleanly provisioned out of environment variables via explicit Mongoose instance methods.
    

Implementing these tightly coupled data layers lays down the ultimate blueprint for stable, secure, and performant enterprise backend applications.