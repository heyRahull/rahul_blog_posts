---
title: "1.18 Polyrepo Architecture Explained"
datePublished: 2026-06-18T16:02:59.321Z
cuid: cmqjovas300000bjc5bcca1ja
slug: 1-18-polyrepo-architecture-explained

---

In contrast to **Monorepo** architecture (where multiple projects live in one repository), **Polyrepo** architecture follows a simpler rule: **Each project or application gets its own dedicated repository.**

While Monorepo is all about sharing code within a single folder, Polyrepo is about keeping projects isolated. But how do we share code like UI components or utility functions if they are in different places? That is what we’ll cover here.

* * *

### How Polyrepo Works

In a Polyrepo setup, applications don't "see" each other's files. Instead, they treat shared code as an **external dependency**.

*   **The Workflow:** You create a shared library, turn it into a package, and install it into your various projects (just like you install `react` or `axios` via npm).
    
*   **Common Ways to Share Code:**
    
*   **Private NPM Packages:** The most professional way. You publish your code to a registry and install it via `npm install @my-org/ui-kit`.
    
*   **Git-based Sharing:** You host your shared code in a separate repository and install it directly via a Git URL in your `package.json`.
    
*   **Local Linking:** For development, you can point your project to a local folder on your machine (e.g., `npm install ../packages/ui`).
    

* * *

### Implementation Example: Sharing a UI Component

Imagine you have two separate React apps, and you want them to both use a custom `Button` component.

#### 1\. Creating the Shared Library

Create a folder named `packages/ui` and initialize it:

```bash
# Inside packages/ui
npm init -y
npm install react # Shared packages need to know about react

```

#### 2\. Installing the Shared Library

Once your library is ready, you don't copy-paste the component file. You go into your application folder and install it:

```bash
# Inside your React App
npm install ../packages/ui

```

#### 3\. Using the Component

Now, your React application treats the component as an installed module, exactly like any other library:

```javascript
import { Button } from 'ui';

function App() {
  return (
    <div>
      <Button onClick={() => alert("Clicked!")}>Login</Button>
    </div>
  );
}

```

* * *

### Monorepo vs. Polyrepo: The Core Difference

| Feature | Monorepo | Polyrepo |
| --- | --- | --- |
| **Structure** | All projects in one folder. | One folder per project. |
| **Code Sharing** | Direct imports from local folders. | Installing as a package (NPM/Git). |
| **Updates** | Immediate across all apps. | Requires a version update/re-install. |
| **Complexity** | High setup (Workspaces). | Simple setup, higher maintenance. |

* * *

### Interview Q&A

**Q: What is the main idea behind Polyrepo?** A: It means "one repo per project." If you have three different apps, you have three different Git repositories. It keeps your projects completely independent of each other.

**Q: How do I share a button component between two apps in a Polyrepo?** A: You turn that button into a "package." You either publish it to a private NPM registry or link it using its file path/Git URL. Then, both apps "install" it just like they would install a library like React or Lodash.

**Q: When should I choose Polyrepo over Monorepo?** A: Polyrepo is great if your teams are completely separate and don't want to deal with the complexity of a shared repository. It’s also simpler to start with. Monorepo is better when you have a large organization where you want to keep everything in sync instantly without constantly updating package versions.

**Q: Is Polyrepo better for beginners?** A: Yes! Polyrepo is the "default" way most people start coding. It’s less confusing because you don't have to worry about complex "workspace" configurations or shared build tools.

* * *

*Do you currently find yourself copying code between projects, or do you manage your shared components as separate packages?*