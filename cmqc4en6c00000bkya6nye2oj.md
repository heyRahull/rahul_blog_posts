---
title: "1.8 Feature-Based Folder Structure"
datePublished: 2026-06-13T08:55:46.675Z
cuid: cmqc4en6c00000bkya6nye2oj
slug: 1-8-feature-based-folder-structure

---

When building real-world enterprise applications, how you organize your codebase can make or break team velocity. As your application scales past dozens of pages and hundreds of components, traditional layouts fall short.

This guide breaks down why **Feature-Based Folder Structure** is the gold standard for large-scale application design and how to implement it effectively.

* * *

## 1\. The Architectural Shift: Layer-Based vs. Feature-Based

There are three common ways to organize a frontend codebase: **Layer-Based**, **Feature-Based**, and **Hybrid** (a mix of both). Let's look at why large projects move away from layers to features.

### The Layer-Based Approach (Small Projects Only)

In a layer-based structure, files are grouped by their *technical type*. You have one massive folder for components, one for pages, and one for API services.

```text
src/
├── components/       # 400+ mixed components
├── pages/            # 100+ different screens
└── services/         # All API calls thrown together

```

*   **Why it fails at scale:** If you need to update the "Product Checkout" flow, you have to scroll through hundreds of unrelated files across three different root folders just to find the relevant code. It creates massive cognitive overload.
    

### The Feature-Based Approach (Scale-Ready)

In a feature-based structure, files are grouped by *business domain* or *user features* (e.g., Auth, Cart, Products). Everything a feature needs to work lives inside its own dedicated folder.

* * *

## 2\. Blueprint of an Enterprise Feature Folder

Let's dissect what a clean, scalable feature folder looks like under `src/features/products/`:

```text
src/
├── features/
│   ├── products/
│   │   ├── components/    # Components used only within this feature
│   │   ├── hooks/         # Custom React hooks specific to products
│   │   ├── pages/         # Product listing or details screens
│   │   ├── services/      # Product-specific API call logic
│   │   └── index.js       # The public gateway (Barrel File)
│   ├── auth/
│   └── cart/

```

### The Power of the `index.js` Gateway (Barrel Export)

Every feature folder should have an `index.js` file at its root. This acts as a **public API** for that feature.

Instead of allowing other parts of the app to reach deep into a feature's internal folders, they should only import what is explicitly exposed by `index.js`.

```javascript
// src/features/products/index.js
// Expose only what the rest of the application needs to know about
export { default as ProductList } from './pages/ProductList';
export { useProductData } from './hooks/useProductData';

```

Now, if another module needs a product component, it imports it cleanly:

```javascript
// Clean internal import
import { ProductList } from '@/features/products';

```

* * *

## 3\. Handling Global Architecture: App & Shared Folders

Of course, not everything fits perfectly into a single isolated feature. Some utilities and UI elements are used everywhere. For those, we design two helper modules: `app/` and `shared/`.

### 1\. The `shared/` Folder

If an asset is used across **multiple** features, it gets placed in the global shared directory.

*   **Shared Components:** Button, Input, Modal, Navbar.
    
*   **Shared Utils/Hooks:** Axios interceptors, date formatters, theme switchers.
    

### 2\. The `app/` Folder

This folder acts as the central brain or the orchestration layer of the application. It binds all your independent features together.

*   `app.jsx` (Root layout component)
    
*   `routes.jsx` (Global router configuration)
    
*   `store.js` (Global state configuration like Redux or Zustand)
    

### Complete High-Level Application View

```text
src/
├── app/                  # Application core configuration & routing
│   ├── app.jsx
│   ├── routes.jsx
│   └── store.js
├── features/             # Business domain modules
│   ├── auth/
│   ├── cart/
│   └── products/
├── shared/               # Reusable global primitives
│   ├── components/       # Buttons, Cards, Spinners
│   ├── hooks/            # useAuthToken, useTheme
│   └── utils/            # formatCurrency, api/axiosConfig
└── main.jsx              # Application entry point

```

* * *

## Interview Q&A

### Q1: What is the difference between a Layer-Based structure and a Feature-Based structure?

**Answer:** A layer-based structure groups files by their technical type—meaning all components go into one giant folder, all pages in another, and all API files in a third.

A feature-based structure groups files by what they actually do for the user. For example, everything related to "Authentication" (its components, pages, and API calls) lives together inside one self-contained folder.

### Q2: Why is a Feature-Based structure preferred for large applications?

**Answer:** When an application grows to hundreds of files, a layer-based structure becomes incredibly messy. Developers waste time hunting for files across different folders.

A feature-based structure keeps code highly organized because everything you need to build or fix a specific feature is in one place. It also helps teams work concurrently because developers working on different features rarely touch the same folders, which drastically reduces git merge conflicts.

### Q3: What is the purpose of an `index.js` file inside a feature folder?

**Answer:** It acts like a "front door" or a public gateway for that feature. Instead of letting other parts of the application reach deep into a feature's private folders, the `index.js` file explicitly exports only the components or functions that the rest of the app is allowed to use. This keeps imports clean and protects the internal structure of the feature from accidental breaking changes.

### Q4: Where do files like a global Navigation Bar or a currency formatter live in a feature-based structure?

**Answer:** Things that are shared across the entire application go into a special folder called `shared/`. Common UI pieces like a standard button or a navbar go under `shared/components/`, while universal helper functions like a currency formatter go under `shared/utils/`.