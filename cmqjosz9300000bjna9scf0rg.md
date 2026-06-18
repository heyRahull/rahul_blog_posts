---
title: "1.17 Monorepo Architecture"
datePublished: 2026-06-18T16:01:11.064Z
cuid: cmqjosz9300000bjna9scf0rg
slug: 1-17-monorepo-architecture

---

In large organizations, managing multiple projects that share common functionality can become a nightmare. **Monorepo Architecture** solves this by keeping multiple projects inside a **single repository**.

Instead of copying and pasting code (or publishing private packages to a registry), you store your shared components, utilities, and libraries in a central folder that all your applications can access.

* * *

### What is Monorepo?

Think of it as a central hub for your organization's code.

*   **Traditional Approach:** Each application has its own repository. If you update a "Button" component, you have to go into every single repository and update it manually.
    
*   **Monorepo Approach:** You have one repository. You create a shared `packages/ui` folder. When you update the button there, every application in the monorepo gets the update automatically.
    

* * *

### Setting Up a Monorepo

To create a monorepo, you need to structure your root directory to manage "workspaces."

#### 1\. The Directory Structure

Create a root folder (e.g., `monorepo/`) and add two main folders:

*   `apps/`: Contains your different web applications (e.g., `e-commerce`, `admin-dashboard`).
    
*   `packages/`: Contains the shared code (e.g., `ui`, `utils`).
    

#### 2\. Defining Workspaces

In your root `package.json`, you define these folders as **workspaces**. This tells your package manager (npm/yarn/pnpm) that these directories are linked.

```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}

```

#### 3\. Sharing Components

Inside `packages/ui`, you create your shared components. You then link this "package" to your applications in the `apps/` folder by adding it to their `dependencies` in their respective `package.json` files:

```json
"dependencies": {
  "ui": "*" 
}

```

*Note: Using* `*` *tells the app to use the local version of the package.*

* * *

### Key Rules for Monorepos

1.  **Independent Package.json:** Every shared package must have its own `package.json`.
    
2.  **Workspace Listing:** Every application and package must be listed in the root `workspaces` configuration.
    
3.  **Dependency Alignment:** Ensure your shared packages are listed as dependencies in the applications that consume them.
    

* * *

### Advantages

*   **DRY (Don't Repeat Yourself):** Write shared UI components once and use them everywhere.
    
*   **Easy Refactoring:** Change the design of a button in one place, and it updates across all projects.
    
*   **Consistency:** Every application in your company maintains the same look and feel effortlessly.
    

* * *

### Interview Q&A

**Q: What is a Monorepo in simple terms?** A: It’s just a way of putting multiple projects into one single folder (repository) instead of keeping them in separate ones. This makes it super easy to share code, like buttons or login logic, between those projects.

**Q: Why would a company choose a Monorepo over separate repositories?** A: The biggest reason is code sharing. If you have five different apps that all need the same navigation bar or button style, a monorepo lets you build that component once in a "shared" folder. If you find a bug, you fix it in one place, and all five apps are fixed instantly.

**Q: Is a Monorepo only for React?** A: Not at all! While we use it for React, you can use monorepos for backend code, mobile apps, or a mix of different technologies. It is an architectural choice, not a React-specific feature.

**Q: What is the hardest part about using a Monorepo?** A: The initial setup can be a bit tricky because you have to configure "workspaces" so the projects know how to talk to each other. But once it's set up, it saves a massive amount of time in the long run.

* * *

*Do you have a project with multiple sub-apps where you find yourself copying and pasting the same UI components?*