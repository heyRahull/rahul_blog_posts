---
title: "1.21 Environment Variables Explained - Vite .env, Dev, Stage, Prod Build"
datePublished: 2026-06-18T16:08:04.104Z
cuid: cmqjp1tyg00000cjb55zw0lof
slug: 1-21-environment-variables-explained-vite-env-dev-stage-prod-build

---

In professional projects, you rarely have just one setup. You usually have a **Development** environment (for local coding), a **Staging/Testing** environment (for QA teams to verify), and a **Production** environment (for your end users).

Using **Environment Variables** allows you to switch between these configurations (API URLs, keys, logging) automatically without changing your code every time you deploy.

* * *

### Why Environment Variables?

If you hardcode your API URL (e.g., `[https://api.myapp.com](https://api.myapp.com)`), you might accidentally test against your live production database. Environment variables act as "config placeholders" that swap values based on where your app is currently running.

* * *

### How to Implement in Vite

Vite requires a specific naming convention. All custom variables **must** be prefixed with `VITE_`.

#### 1\. Create your files

In your project root, create these files:

*   `.env` (Default values used if specific ones aren't found)
    
*   `.env.development` (Values for local development)
    
*   `.env.staging` (Values for the QA environment)
    
*   `.env.production` (Values for the live application)
    

#### 2\. Configure Values

Inside each file, define your variables:

```env
# .env.development
VITE_API_URL=https://dev-api.myapp.com
VITE_APP_NAME=My App (Dev)

# .env.production
VITE_API_URL=https://api.myapp.com
VITE_APP_NAME=My App

```

#### 3\. Accessing Variables in Code

Vite automatically loads these variables into the `import.meta.env` object.

```javascript
const config = import.meta.env;

console.log(config.VITE_API_URL);
console.log(config.VITE_APP_NAME);

```

* * *

### Building for Different Environments

You can trigger different builds based on the environment you are targeting.

*   **Development:** `npm run dev` (Uses `.env.development` or `.env`)
    
*   **Production Build:** `npm run build` (Uses `.env.production`)
    
*   **Staging Build:** `npm run build -- --mode staging` (Uses `.env.staging`)
    

*Note: After building, run* `npm run preview` *to test your production or staging build locally.*

* * *

### Best Practices & Safety

*   **Always use the** `VITE_` **prefix:** Without it, Vite won't expose the variable to your frontend code.
    
*   **Never store secrets:** Environment variables are embedded into your frontend code during the build process. **Do not** store passwords, private API keys, or sensitive credentials here, as anyone can view them in the browser's source code.
    
*   **Build-time only:** These variables are "baked in" during the build process. If you change a variable, you must rebuild the application for the change to take effect.
    

* * *

### Interview Q&A

**Q: Why can't I just use a simple** `config.js` **file for all my URLs?** A: A single file makes it hard to manage different environments. With `.env` files, you can have a "production" file that lives on your server and a "development" file on your machine. You don't have to worry about accidentally pushing your local testing URLs to the live website.

**Q: If I use** `import.meta.env`**, is it safe to put my database password there?** A: **No!** Frontend environment variables are included in your final JavaScript bundle. Anyone who opens the "Developer Tools" in their browser can see them. Only put public-facing config like API base URLs, project names, or analytics IDs here.

**Q: What happens if I forget to set a variable in** `.env.production`**?** A: Vite will look for a fallback in your base `.env` file. If it’s not there either, the value will simply be `undefined`. It is a good practice to keep default values in your main `.env` file.

* * *

*Do you have different API endpoints for your development and production environments, and how do you currently manage them?*