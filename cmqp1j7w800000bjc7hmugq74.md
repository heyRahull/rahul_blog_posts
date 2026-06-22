---
title: "1.5 Ecommerce & Hospital Management Data Model"
datePublished: 2026-06-22T09:56:21.594Z
cuid: cmqp1j7w800000bjc7hmugq74
slug: 1-5-ecommerce-hospital-management-data-model

---

A major roadblock developers face when building applications is failing to think about data points first. They skip the planning phase, jump straight to writing logic, and get stuck because they don't know what data to fetch from the user or store in the database.

The stronger your Data Modeling skills, the easier it becomes to build scalable applications. Once you master data modeling, writing controllers and implementing full-stack features becomes a breeze.

In this guide, we will design the database architecture for a production-ready **E-Commerce System**. Along the way, we will cover critical Mongoose concepts such as relational references, database asset optimization, sub-schemas (mini-models), and Enums.

* * *

## 1\. Project Setup: Identifying the Core Actors

When building an E-Commerce platform, you should avoid rushing into writing code for products right away. You must sit down, look at the big picture, and determine which fields are completely independent and which ones rely on others.

For a functional E-Commerce backend, we require four core models. To ensure team consistency, we use the professional `.models.js` file extension:

1.  `user.models.js` (Handles user accounts and identities)
    
2.  `category.models.js` (Manages product groupings like Summer Wear, Phones, etc.)
    
3.  `product.models.js` (Tracks product item details, pricing, and stock)
    
4.  `order.models.js` (Manages cart contents, status, and shipping information)
    

* * *

## 2\. Setting Up the Foundation: User and Category Models

Let’s start by writing our standard 3-step Mongoose boilerplate for our base schemas.

### User Model (`user.models.js`)

We want to capture essential credentials while ensuring data sanitation using built-in Mongoose properties like `lowercase` and `unique`.

```javascript
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: true,
      unique: true,
      lowercase: true // Automatically converts input to lowercase before saving
    },
    email: {
      type: String,
      required: true,
      unique: true,
      lowercase: true
    },
    password: {
      type: String,
      required: true // Stored as a string (hashed later via middleware)
    }
  },
  { timestamps: true }
);

export const User = mongoose.model('User', userSchema);

```

### Category Model (`category.models.js`)

The category model is simple but highly essential, acting as a standalone point that other models will reference.

```javascript
import mongoose from 'mongoose';

const categorySchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: true
    }
  },
  { timestamps: true }
);

export const Category = mongoose.model('Category', categorySchema);

```

> **💡 Quick Interview Note:** Always name your model string as a singular capitalized word (e.g., `'Category'`). MongoDB is highly intelligent; it will automatically convert this string into a lowercase, pluralized collection name (`categories`) under the hood.

* * *

## 3\. Designing the Product Model and Handling Media Assets

Now, let's build the `product.models.js` file. This model introduces two crucial concepts: handling images and creating database references (`ref`).

### 🚨 Production Secret: How to Store Images?

A common mistake among students is trying to store raw image files or heavy binary buffers directly inside MongoDB. **Do not do this.** Database engines are not designed to store media storage blocks; doing so will make your database heavy and slow down queries.

Instead, upload your media files (images, videos, PDFs) to your own server folder or a third-party cloud service like **Cloudinary** or an **AWS S3 Bucket**. These services return a public asset URL string. You store this **URL string** in MongoDB. When the frontend requests product details, you simply serve the string link.

```javascript
import mongoose from 'mongoose';

const productSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: true
    },
    description: {
      type: String,
      required: true
    },
    productImage: {
      type: String, // Public URL string fetched from Cloudinary/S3
      required: true
    },
    price: {
      type: Number,
      default: 0
    },
    stock: {
      type: Number,
      default: 0
    },
    category: {
      type: mongoose.Schema.Types.ObjectId, // Links to another document ID
      ref: 'Category', // Must exactly match the exported Category model name
      required: true
    },
    owner: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User' // Tracks which vendor/user listed the item
    }
  },
  { timestamps: true }
);

export const Product = mongoose.model('Product', productSchema);

```

* * *

## 4\. Designing the Order Model: Sub-Schemas and Enums

The `order.models.js` file is the most complex because an order can contain multiple items, and we must track the exact quantity of each product ordered.

### Creating Mini-Schemas (Sub-documents)

We can't just put an array of products in the order because we would lose track of the *quantity* of each individual product. To solve this, we create a dedicated `orderItemSchema` right inside the file. It doesn't need its own separate file or export because it's only used here.

```javascript
import mongoose from 'mongoose';

// Mini-Schema to define individual item structure inside the order array
const orderItemSchema = new mongoose.Schema({
  productId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Product'
  },
  quantity: {
    type: Number,
    required: true
  }
});

const orderSchema = new mongoose.Schema(
  {
    orderPrice: {
      type: Number,
      required: true
    },
    customer: {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'User',
      required: true
    },
    orderItems: [orderItemSchema], // Array of sub-documents using our mini-schema
    address: {
      type: String,
      required: true
    },
    status: {
      type: String,
      enum: ['PENDING', 'CANCELLED', 'DELIVERED'], // Enforces strict choices
      default: 'PENDING'
    }
  },
  { timestamps: true }
);

export const Order = mongoose.model('Order', orderSchema);

```

### 💡 The Power of Mongoose Enums (`enum`)

For critical states like order statuses (or flight seat choices like window/middle/aisle), you cannot trust plain string inputs. If one developer writes `"Delivered"` and another writes `"delivered"`, your frontend logic will break.

By using the `enum` property, you lock down the database field to a strict set of options. If an input string doesn't match the exact casing and spelling defined in the array, Mongoose will throw a validation error and refuse to save the document.

* * *

## Conclusion

Data modeling is not something you can learn overnight by reading a book cover-to-cover; it is a skill developed entirely based on project requirements. As you build more applications over 5 to 10 years, analyzing and structuring these schemas will become second nature.

Take these models, play around with them, add fields like custom address schemas or separate pricing matrices, and practice creating them yourself!