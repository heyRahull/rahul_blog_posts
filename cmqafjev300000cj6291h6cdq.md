---
title: "React System Design: Centralized API Error Handling & Toast Notifications"
datePublished: 2026-06-12T04:31:52.601Z
cuid: cmqafjev300000cj6291h6cdq
slug: react-system-design-centralized-api-error-handling-toast-notifications

---

In a production-ready application, gracefully handling API failures is just as important as handling successful data fetches. If an API fails, crashing the UI or leaving the user with a blank screen is a terrible user experience.

Instead of writing custom error logic in every single component, you need a **centralized architecture** that catches network errors at the root, propagates them through your service layers, and cleanly displays them to the user using a reusable UI component like a **Toast Notification**.

* * *

## 1\. Centralized Error Propagation Flow

To handle errors effectively without creating messy code, we follow a strict chain of responsibility across three layers:

```plaintext
[Network Fault] ➔ [Base Service catches & throws] ➔ [Feature Service bubbles up] ➔ [UI Component catches & sets State] ➔ [Toast Notification appears]

```

1.  **Base Service (**`baseServices.js`**):** Checks the raw HTTP response status. If the network call fails (e.g., `response.ok` is false), it halts execution and throws an explicit error message.
    
2.  **Feature Service (**`productServices.js`**):** Catches the error from the Base Service using a `try/catch` block and bubbles it up using the `throw` keyword to whoever called the function.
    
3.  **UI Component (**`Products.jsx`**):** Catches the propagated error, extracts the clean message string, and updates local state variables to display the alert component.
    

* * *

## 2\. Code Implementation

Let’s see how to update our architecture to implement this flow.

### Step 1: Catching Faulty Responses in the Base Service

By looking at the network object, we can inspect `response.ok`. If a network call returns a status code outside the 200–299 range (like 400 or 500), `response.ok` becomes **false**. We intercept this instantly.

```javascript
// src/services/api/baseServices.js

export const getAPI = async (url) => {
  const response = await fetch(url);
  
  // If the server returns an error code, stop and throw an error
  if (!response.ok) {
    throw new Error("Something went wrong. Please try again after some time.");
  }
  
  // Clean parsing at the root layer so feature layers don't repeat it
  return await response.json(); 
};

```

### Step 2: Bubbling the Error Through the Feature Service

The feature service serves as a pass-through layer. It attempts to run the base call, and if an exception occurs, it intercepts it and re-throws it to the UI component.

```javascript
// src/services/productServices.js
import { getAPI } from "./api/baseServices";
import { PRODUCT_URL } from "./api/endpoints";

export const getProductList = async (limit) => {
  try {
    // If limit parameter is missing or broken, getAPI will throw an error
    const targetUrl = limit ? `${PRODUCT_URL}?limit=${limit}` : PRODUCT_URL;
    const data = await getAPI(targetUrl);
    
    return data.products;
  } catch (error) {
    // Bubble the error up to the UI Component
    throw error;
  }
};

```

### Step 3: Catching Errors and Rendering the Toast UI

The component handles UI state. It maintains pieces of state to track whether an error message exists and triggers a visual `<Toast/>` pop-up when an error state changes.

```jsx
// src/components/Products.jsx
import React, { useState, useEffect } from "react";
import { getProductList } from "../services/productServices";
import Toast from "./Toast"; // Our reusable Toast Component

const Products = () => {
  const [products, setProducts] = useState([]);
  const [errorMessage, setErrorMessage] = useState("");
  const [showToast, setShowToast] = useState(false);

  useEffect(() => {
    const fetchAndLoadData = async () => {
      try {
        // Intentionally omitting the limit argument to trigger a mock API error
        const data = await getProductList(); 
        setProducts(data);
      } catch (error) {
        // Capture the thrown error message string gracefully
        setErrorMessage(error.message);
        setShowToast(true);
      }
    };

    fetchAndLoadData();
  }, []);

  const closeToastNotification = () => {
    setShowToast(false);
    setErrorMessage("");
  };

  return (
    <div style={{ padding: "20px" }}>
      <h1>Product Dashboard</h1>
      
      {/* Render the Toast UI if showToast state is true */}
      {showToast && (
        <Toast 
          message={errorMessage} 
          onClose={closeToastNotification} 
        />
      )}
      
      {/* Render list content here */}
    </div>
  );
};

export default Products;

```

* * *

## 3\. Core Architectural Highlights

*   **Automatic Cleanup (Self-Dismissal):** A robust UI design should ensure toast notification components feature a built-in countdown (e.g., using a `setTimeout` for 5 to 8 seconds) to dismiss themselves, while still offering an optional manually triggered close button.
    
    ```javascript
    // src/components/Toast.jsx
    import React, { useEffect } from "react";
    
    const Toast = ({ message, onClose }) => {
      useEffect(() => {
        // Auto-close after 5 seconds
        const timer = setTimeout(() => {
          onClose();
        }, 5000);
    
        // Cleanup timer on unmount
        return () => clearTimeout(timer);
      }, [onClose]);
    
      return (
        <div className="toast">
          <span>{message}</span>
          <button onClick={onClose}>✕</button>
        </div>
      );
    };
    
    export default Toast;
    ```
    
*   **Streamlining Parsing Cycles:** Moving `.json()` conversion out of specific feature layers and placing it directly inside `baseServices.js` means you avoid writing duplicate conversion methods across dozens of feature files.
    

* * *

## 💡 Interview Q&A

**Q1: How does JavaScript's built-in** `fetch()` **method handle HTTP errors like a 404 or a 500 server crash?**

**Ans:** Surprisingly, native `fetch()` does **not** fail or throw an error when a server returns a 404 or 500 code! It completes the request successfully. The only way it throws an error natively is if there is a complete network failure (like losing internet connection). To catch server crashes, we must manually look at `response.ok` and throw an explicit error ourselves.

**Q2: Why should we use** `throw` **to bubble errors up instead of managing the toast notification inside the service file itself?**

**Ans:** Because service files are purely for logic and data—they don't know anything about the UI layout. If you tie your UI components or toast triggers directly to your service file, you break the separation of concerns. Throwing the error up allows the UI component to decide exactly *how* and *where* to show that error message to the user.

**Q3: What property on a standard JavaScript** `Error` **object contains the actual message string we pass into it?**

**Ans:** When you create an error using `new Error("My custom error text")`, your text string is stored automatically inside the `.message` property. In your catching code blocks, you read `error.message` to get that exact text string back cleanly.

**Q4: Why is it bad practice to pass success/failure boolean flags from services back to components instead of throwing errors?**

**Ans:** Using boolean flags (like returning `{ success: false }`) forces you to write redundant `if/else` checks across every component in your application. Using a standard `try/catch` with thrown errors allows JavaScript to halt execution immediately when something goes wrong, making your code easier to read and maintain.