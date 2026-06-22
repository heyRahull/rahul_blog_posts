---
title: "1.6 How to setup a professional backend project (part 1)"
datePublished: 2026-06-22T11:18:45.776Z
cuid: cmqp4h6va00010cjbf1b6c94l
slug: 1-6-how-to-setup-a-professional-backend-project-part-1

---

If you have been following along with backend tutorials online, you have likely learned the bare basics: spinning up a simple Express server, defining a couple of routes, and creating elementary Mongoose schemas. While that is a great starting point, it only scratches the surface of how software development actually functions in the real world.

It is time to step out of the comfort zone of basic variables, simple loops, and elementary functions. We are transitioning into a professional journey to build a **100% production-grade backend application** using Node.js, Express, and MongoDB. The codebase and architecture we are building here follow the exact standards used by major tech enterprises.

Building production-grade software demands high dedication and a deep understanding of code structure, edge cases, and problem-solving. By the end of this project, you will experience the true thrill of writing robust backend architecture.

* * *

## The Blueprint: Analyzing a Complex Real-World Application

Before writing a single line of backend code, professional teams always start with a finalized design blueprint. In the industry, UI/UX designers hand over comprehensive design layouts (such as Figma or Adobe XT files).

```plaintext

UI Designers  (Figma/Visual Layout) ───> Front-End Engineers  (API Calls & UI Logic) ───>   
Back-End Developers (Data Modeling & APIs)   

```

*   **The Front-End Perspective:** UI developers and front-end engineers look at this layout to recreate the exact visual aesthetics, fonts, colors, spacing, and asset management. They stitch the components together and execute API calls to fetch data.
    
*   **The Back-End Perspective:** As backend developers, we are not concerned with whether a button is purple or red. Our primary focus is **data**. We look at the visual blueprint to reverse-engineer the database architecture: *What data points need to be collected? How should they be stored efficiently? How will they be processed and delivered back to the client?*
    

### Deconstructing the App's Data Footprint

By looking at a modern, feature-rich streaming application layout, we immediately realize that a simple database model will not cut it. We need to capture:

*   Complex user profiles (usernames, emails, full names).
    
*   Multiple image assets per user (avatars, cover images).
    
*   User behavior metrics (watch histories, video liking systems, playlists).
    
*   Advanced security mechanisms (passwords, JWT tokens, and refresh tokens for session handling).
    

This level of industrial data modeling is rarely taught in a typical college curriculum or a standard surface-level tutorial. It requires thinking ahead about future scalability and scope.

* * *

## Phase 1: Clean Repository Initialization

Because this is a dedicated, real-world project, we are ignoring our previous basic practice scripts and starting fresh with a clean, trackable Git repository.

Open your terminal, create a blank project folder, and open it in Visual Studio Code:

```bash
mkdir backend-practise
cd backend-practise
code .
```

### 1\. Initialize Node.js Package Manager

Run the initialization command to generate your standard `package.json` file:

```bash
npm init
```

Fill out the prompts according to your project scope. We will configure `index.js` as our initial entry point for now.

### 2\. Upgrading to ES6 Modules

By default, Node.js uses CommonJS (`require('./file')`) for importing files. To write modern, consistent JavaScript across the stack, open your newly generated `package.json` file and explicitly add the module type flag:

```json
{
  "name": "backend-practise",
  "version": "1.0.0",
  "type": "module",
  "main": "src/index.js",
  ...    
}
```

Setting `"type": "module"` ensures we can cleanly use `import / export` syntax across our entire project workspace.

* * *

## Phase 2: Professional Directory & File Structure

A massive mistake junior developers make is dumping all their controllers, database configurations, and route definitions into a single, chaotic `index.js` file. In a production environment, this is an absolute nightmare to maintain.

We will strictly organize our project using a highly scalable, modular structure inside a root folder named `src/`.

### Creating the Project Anatomy

Navigate to your root folder and construct the following layout using your terminal or the VS Code sidebar:

```plaintext
backend-practise/
├── public/                 # For static and temporary file handling
│   └── temp/               # Temporary local file storage
├── src/                    # Main application source directory
│   ├── controllers/        # Core business and logical operations
│   ├── db/                 # Database connection configurations
│   ├── middlewares/        # Middlewares (e.g., auth checks, file uploads)
│   ├── models/             # Schema definitions and database models
│   ├── routes/             # Clear, isolated routing layers
│   ├── utils/              # Reusable helper functions and utilities
│   ├── constants.js        # Global app constants
│   └── index.js            # Main server entry file
├── .env                    # Secret environment variables (Local)
├── .env.sample             # Blank template of env variables for the team
├── .gitignore              # Tells Git what to ignore
├── .prettierignore         # Tells Prettier what to ignore
└── .prettierrc             # Consistent code formatting rules

```

### Pro Tip: Forcing Empty Folders to Git

Git tracks files, not empty directories. If you try to push an empty folder like `public/temp/` to GitHub, Git will completely ignore it. To bypass this and preserve your folder structure for other team members, create an empty file named `.gitkeep` inside your empty directories.

```bash
touch public/temp/.gitkeep
```

* * *

## Phase 3: Developer Utilities Setup (Nodemon & Prettier)

When building an enterprise application with a team, absolute consistency in developer workflow and code styling is non-negotiable.

### 1\. Setting Up Live Reloads with Nodemon

Constantly killing and restarting your Node server manually every time you change a line of code is highly inefficient. We will use a development utility called `nodemon` to monitor file changes and automate restarts instantly.

Since `nodemon` is strictly needed for development and isn't required when running live on production servers (like AWS or DigitalOcean), we install it as a **development dependency** (`devDependencies`) using the `-D` or `--save-dev` flag:

```bash
npm install -D nodemon
```

Now, map this tool inside your `package.json` script section so you can trigger it seamlessly with a simple command:

```json
"scripts": {
  "dev": "nodemon src/index.js"
}
```

You can now spin up your live-reloading dev environment at any time by running `npm run dev`.

### 2\. Enforcing Code Formatting Rules via Prettier

Different developers use different formatting styles (e.g., some prefer 4-space tabs, others use 2; some use semicolons, others completely omit them). If unmanaged, merging branches into Git results in hundreds of chaotic formatting conflicts.

To lock down formatting across the entire team, install Prettier as a dev dependency:

```bash
npm install -D prettier
```

Next, create a configuration file named `.prettierrc` in your root folder to enforce absolute rules:

```json
{
  "singleQuote": false,
  "bracketSpacing": true,
  "tabWidth": 2,
  "semi": true,
  "trailingComma": "es5"
}
```

To stop Prettier from accidentally auto-formatting system files or scrambling environment variables, create a `.prettierignore` file:

```plaintext
/.env
/.env.sample
/node_modules
/dist
```

* * *

## Phase 4: Git and Environment Security Configuration

Never push sensitive credentials, API keys, database connection strings, or port configurations directly to public repositories.

### 1\. Setting Up a Solid `.gitignore`

Instead of guessing what system files to hide, use a standardized Node.js template from a reliable gitignore generator tool. Create a `.gitignore` file at the root level and include:

```plaintext
# Dependency directories
node_modules/
jspm_packages/
web_modules/

# Environment files
.env
.env.test
.env.production
.env.local

# Debug logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*

```

### 2\. The `.env` vs `.env.sample` Pattern

Your local credentials go into `.env`. Because `.env` is ignored by Git, always create a duplicate skeleton blueprint named `.env.sample`. This allows your fellow developers to instantly see what configuration keys are mandatory to run the backend when they clone your project repository.

* * *

## Initial Synchronization

With the architectural foundation perfectly laid out, we initialize Git, set up our default `main` branch, link the remote repository origin, and push our clean initial template:

```bash
git init
git add .
git commit -m "chore: initial project setup and professional file structure"
git branch -M main
git remote add origin <your-github-repo-url>
git push -u origin main
```

## Wrapping Up Part 1

We haven't written complex server logic yet, but we have completed the most crucial step: **laying a bulletproof, institutional foundation for our system**.

In the next part of this series, we will break out of basic connection templates and write clean asynchronous database connection handlers, professional error handling middleware, and standard API response wrappers.

**What are your thoughts on this modular layout pattern?** Let’s discuss it in the comments below! Don't forget to clone the code and test these configurations out on your local machines. Stay tuned for Part 2!