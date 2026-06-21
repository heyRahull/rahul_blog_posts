---
title: "1.3 How to Connect Frontend & backend in javascript (CORS, proxy explained)"
datePublished: 2026-06-21T11:30:44.108Z
cuid: cmqnpgqgh00000bkr1yj9f5mz
slug: 1-3-how-to-connect-frontend-backend-in-javascript-cors-proxy-explained

---

In the development world, there is one word that intimidates new developers more than anything else—**"Production"**. When you are building your own project, where there are no real users yet and no manager to scold you for breaking things, why is there still such a deep fear of deploying code live?

Often, this anxiety is unintentionally created by online tutorials or overcomplication. But the reality is that production is not a mystical monster. Taking the code we learn on our local machines and running it live using ecosystem best practices and industry standards is exactly what production is.

In this article, we will look at the **Big Picture** of full-stack architecture, build a basic Express backend and a React frontend, and most importantly, break down the core concepts behind **CORS errors and Proxies**.

* * *

## 1\. Advanced Port Handling in Enterprise Production

When building a backend server locally, developers frequently hardcode the port:

```javascript
const port = 3000;

```

However, this practice **will cause your app to crash instantly in production**. In a production environment (like AWS or DigitalOcean), the port is injected dynamically by the hosting provider. Therefore, we must read it using **Environment Variables**.

### Best Practice Implementation:

```javascript
const port = process.env.PORT || 3000;

```

*   `process.env.PORT`: This checks the server's cloud environment to dynamically read the assigned port.
    
*   `|| 3000`: If the code is running on a local machine and no environment variable is found, it safely falls back to port `3000`.
    

* * *

## 2\. JavaScript Module Assembly: CommonJS vs. ECMAScript Modules (ESM)

The first time you try to use the modern `import` syntax in a raw Node.js/Express environment, you will face a classic error:

```bash
Critical Error: Cannot use import statement outside a module

```

### Why Does This Happen?

By default, Node.js uses the **CommonJS (CJS)** architecture, where files are loaded synchronously using `require()`:

```javascript
const express = require('express'); // CommonJS syntax

```

However, in React and modern JavaScript, we use **ES Modules (ESM)**, which evaluate assets asynchronously:

```javascript
import express from 'express'; // ES Module syntax

```

### The Solution

To explicitly tell Node.js that you want to use the modern `import/export` syntax, you must add the `"type": "module"` directive inside the root `package.json` file of your backend:

```json
{
  "name": "backend",
  "version": "1.0.0",
  "type": "module", 
  "scripts": {
    "start": "node server.js"
  }
}

```

* * *

## 3\. Creating a Standardized Mock Data Endpoint

According to industry standards, whenever you design API endpoints, they should be cleanly structured with proper versioning (e.g., `/api/v1/jokes`).

Here is the baseline architecture of a `server.js` file that serves a basic array of joke objects:

```javascript
import express from 'express';

const app = express();
const port = process.env.PORT || 3000;

// Mock JSON Data Array simulating a database response
const jokes = [
  { id: 1, title: 'Joke One', content: 'This is the first joke' },
  { id: 2, title: 'Joke Two', content: 'This is another joke' },
  { id: 3, title: 'Joke Three', content: 'This is the third joke' }
];

// Standardized API Route
app.get('/api/v1/jokes', (req, res) => {
  res.send(jokes);
});

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

```

> 💡 **Pro Tip:** Whenever a large JSON response is fetched in the browser, paste it into a web utility like **JSON Formatter** and view it in a **Tree Structure**. This makes reading complex data objects significantly easier during professional debugging.

* * *

## 4\. Frontend Toolchains and Bundlers

When we initialize a frontend application using CLI commands like `npm create vite@latest .`, we are utilizing what is known as a **Toolchain**.

### What Does a Bundler Actually Do?

Browsers are inherently simple platforms; they cannot natively read React's `.jsx` syntax, TypeScript, or modern complex JavaScript imports. Browsers only understand raw **HTML, CSS, and Vanilla JavaScript**.

The sole job of bundlers like **Vite, Webpack, or Parcel** is to act as a compiler. They take thousands of split, component-based developer files, resolve their connections, optimize them, and smash them down into simple static assets that any client browser can render seamlessly.

* * *

## 5\. The CORS Nightmare and the "Proxy" Solution

When you try to use Axios or Fetch to request data from your React frontend (running on `http://localhost:5173`) to your backend API (running on `http://localhost:3000/api/v1/jokes`), a bright red error crashes your browser console:

```bash
Access to XMLHttpRequest at '...' from origin 'http://localhost:5173' has been blocked by CORS policy...

```

### What is CORS? (Cross-Origin Resource Sharing)

Think of CORS as a security guard at the front door of a house. If a family member living in the house wants to step inside (**Same Origin**), they pass without issue. However, if a stranger from outside approaches (**Cross-Origin**), the guard blocks them unless the homeowner explicitly authorizes their entry.

In web architecture, if the **URL domain is different**, or even if just the **Port Number is different**, the browser flags it as Cross-Origin for security reasons. It blocks the request to prevent unauthorized sites from stealing data or exhausting your server's bandwidth.

### Solution 1: Backend Whitelisting (CORS Node Package)

The backend developer can install the `cors` package and explicitly pass your frontend's URL into a whitelist configuration.

### Solution 2: The Magic of a Proxy

For local development environments, the easiest and cleanest standard approach is setting up a **Proxy** directly inside your frontend toolchain configuration.

If you are using **Vite**, add the server configurations to your `vite.config.js` file:

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/react-vite-plugin'

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': 'http://localhost:3000', // Proxy Configuration
    }
  }
})

```

### How Does a Proxy Solve the Problem?

1.  **Cleaner Code:** You no longer need to write absolute paths like `axios.get('http://localhost:3000/api/v1/jokes')` in React. You can write clean, relative paths: `axios.get('/api/v1/jokes')`.
    
2.  **Origin Trickery:** When the frontend initiates an HTTP request, the proxy interceptor catches it and makes the backend server believe the request originated from the **exact same domain and port (**`http://localhost:3000`**)**. Because the origins match perfectly, the browser bypasses the CORS policy cleanly.
    

* * *

## 6\. Industry Bad Practices: Serving Static Files from the Backend

In the software industry, many companies adopt a shortcut that is heavily considered an **architectural bad practice**. Instead of hosting the frontend and backend on decoupled servers, they run a production build of the frontend and drop the compiled assets directly into the backend folder to serve them via Express middleware:

```javascript
app.use(express.static('dist')); // Serving Static Assets

```

### Why Is This Considered a Bad Practice?

| Problem | Technical Impact |
| --- | --- |
| **No Hot-Reloading** | Changes made to the UI will not update live on the development server. |
| **Repetitive Build Cycles** | Every minor code adjustment forces you to re-run `npm run build` and manually replace the folder in the backend. |
| **Tangled Architecture** | Blurs the separation of concerns between frontend and backend, complicating application scaling and cloud debugging. |

In professional systems, keeping frontend and backend architectures completely **segregated** on independent hosting tiers is always the supreme best practice.

* * *

## Conclusion

Building full-stack applications is far less daunting once you zoom out and look at the big picture. The trick to becoming an expert UI/Full-Stack developer isn't memorizing code blocks—it's understanding the structural mechanics of port assignment, asynchronous module compilation, CORS barriers, and proxy networks.

Once you grasp these underlying structural mechanics, you can seamlessly connect frontend architectures to any API network with absolute confidence!