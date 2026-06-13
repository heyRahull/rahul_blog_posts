---
title: "1.4 API Architecture & Service Layers"
datePublished: 2026-06-11T09:37:46.029Z
cuid: cmq9b0xs400000bhpf901bkt5
slug: 1-4-api-architecture-service-layers

---

When building a basic React app or following short tutorials, it is common practice to make API calls directly inside a component's `useEffect` hook. While this works fine for small tasks, it becomes a major bottleneck as your application scales.

Placing API logic directly inside components prevents you from centralized management of endpoints, reusing common base services, and implementing application-wide logic like global headers, JWT token rotation, or cross-site scripting (XSS) defenses.

This post outlines how to design a structured, highly scalable **API Service Layer** for a React application.

## 1\. Architecture Overview

To separate UI logic from data-fetching logic, we break down our API layer into four distinct parts:

1.  **Endpoints File (**`endpoints.js`**):** A centralized configuration file containing all base URLs and specific API routes.
    
2.  **Base Service (**`baseServices.js`**):** A single source of truth from which *all* global network requests are dispatched (using `fetch` or `axios`). This is where universal configurations like authorization tokens and error-handling live.
    
3.  **Feature Services (**`productServices.js`**):** Specific abstraction functions that use the **Base Service** to pull feature-dependent data (e.g., product lists, user profiles).
    
4.  **UI Component (**`Products.jsx`**):** Consumes the Feature Services and updates the local state to display the data.
    

```plaintext
[UI Component] ➔ [Feature Service] ➔ [Base Service] ➔ [Endpoints Configuration]

```

## 2\. Setting Up the Service Layer

### Step 1: Centralizing API Routes (`endpoints.js`)

Instead of hardcoding URLs across multiple files, manage them globally. This file contains purely JavaScript configuration and does not include React JSX, so it uses the `.js` extension with a lowercase name.

```javascript
// src/services/api/endpoints.js

const BASE_URL = "https://dummyjson.com";

// Build feature-specific URLs using template literals
export const PRODUCT_URL = `${BASE_URL}/products`;

```

### Step 2: Building the Single Source of Truth (`baseServices.js`)

Every API call passes through this file. This provides a single choke point to add global headers, attach authentication headers, or intercept network level payloads.

```javascript
// src/services/api/baseServices.js

/**
 * Core wrapper function for GET requests
 * @param {string} url - The final constructed endpoint URL
 */
export const getAPI = async (url) => {
  // Utilizing core JavaScript fetch; can be swapped with Axios if needed
  const response = await fetch(url);
  return response; 
};

// Note: Future POST, PUT, and DELETE methods should also be hosted here

```

### Step 3: Abstracting Feature Business Logic (`productServices.js`)

Feature-specific service files import methods from the **Base Service** and handles URL construction parameters as well as response stream mapping (`response.json()`).

```javascript
// src/services/productServices.js
import { getAPI } from "./api/baseServices";
import { PRODUCT_URL } from "./api/endpoints";

/**
 * Fetches list of products with a specified limit
 * @param {number} limit - Number of items to retrieve
 */
export const getProductList = async (limit) => {
  // Construct dynamic URL with query parameters
  const targetUrl = `${PRODUCT_URL}?limit=${limit}`;
  
  // Await response stream from base service
  const response = await getAPI(targetUrl);
  const data = await response.json();
  
  // Return the specific data subset required by the frontend layout
  return data.products;
};

```

* * *

## 3\. Consuming \`the Service in Components

With the service layer complete, your React UI components stay decoupled from data implementation details. The component simply triggers an asynchronous call on mount and processes the final clean data.

```jsx
// src/components/Products.jsx
import React, { useEffect } from "react";
import { getProductList } from "../services/productServices";

const Products = () => {
  
  useEffect(() => {
    const fetchProductsData = async () => {
      try {
        // Querying service layer directly instead of invoking 'fetch' natively
        const productData = await getProductList(30);
        console.log("Cleaned Product Array Data:", productData);
      } catch (error) {
        console.error("Error fetching product listings:", error);
      }
    };

    fetchProductsData();
  }, []);

  return (
    <div>
      <h1>Product List</h1>
    </div>
  );
};

export default Products;

```

* * *

## 4\. Key Takeaways & System Behavior

*   **Development Double-Firing (React StrictMode):** You might observe that log phrases or API triggers double-fire in your local console. This occurs exclusively in **Development Mode** when your application is wrapped inside `<React.StrictMode>`. React mounts, unmounts, and remounts components intentionally to spot potential side-effect leaks. This behavior is absent in production builds.
    
*   **Separation of Concerns:** The UI component only cares about UI and presentation state. The networking stack handles the HTTP request configuration independently.
    

* * *

## Interview Q&A

### Q1: Why should we avoid writing native `fetch` or `axios` calls directly inside a component's `useEffect` loop?

**Answer:** Directly injecting API triggers inside components tightly couples the user interface with infrastructural implementations. It leads to issues such as:

*   Duplicated endpoint strings across multiple files.
    
*   Inability to globally intercept incoming headers or scale access tokens across requests.
    
*   Poor modularity, which makes executing unit tests on code logic isolated from UI renders challenging.
    

### Q2: What is the benefit of mapping the response data with `.json()` inside the service layer instead of inside the UI component?

**Answer:** The UI component should remain oblivious to how data is transferred. Raw network responses return a readable stream object. Isolating data normalization logic (like parsing streams or selecting specific payload slices like `data.products`) inside the service layer ensures that components receive structured, clean data ready for state mutations.

### Q3: Why do we see console logs or network hooks execute twice during local component mounting phases?

**Answer:** This behavior is caused by `<React.StrictMode>` running in the local development build. It runs components through double invocation sequences to discover unintentional memory leaks or side effects. This check runs strictly in development and is automatically omitted from standard production application builds.