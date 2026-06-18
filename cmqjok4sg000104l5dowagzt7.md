---
title: "1.15 Custom Hooks Design Pattern in React"
datePublished: 2026-06-18T15:54:18.324Z
cuid: cmqjok4sg000104l5dowagzt7
slug: 1-15-custom-hooks-design-pattern-in-react

---

While **Custom Hooks** are primarily a built-in feature of React, they serve as a powerful **design pattern** when used to separate business logic from your UI components. By extracting complex logic into its own reusable function, you keep your components clean, maintainable, and focused strictly on the view.

* * *

### What is the Custom Hooks Pattern?

In systems design, a pattern is a proven solution to a recurring problem. The **Custom Hook Pattern** is the strategy of moving non-UI logic—such as API calls, authentication, or local storage management—out of your components and into dedicated, reusable functions.

*   **Naming Convention:** By convention, custom hooks must start with the keyword `use` (e.g., `useLocalStorage`). This tells React (and other developers) that this function is a hook and may internally use other React hooks like `useState` or `useEffect`.
    

* * *

### Why Use This Pattern?

Without custom hooks, you often end up with "logic bloat" inside your components. If you need the same logic in five different places, you might end up copying and pasting that code five times, making it a nightmare to update later.

**Benefits:**

*   **Reusability:** Write your logic once and import it wherever you need it.
    
*   **Cleaner Components:** Your components only care about *displaying* data, not how the data is fetched or calculated.
    
*   **Easier Maintenance:** If the logic needs to change, you update it in one place, not in every component that uses it.
    

* * *

### Implementation Example: `useLocalStorage`

Instead of writing `localStorage.getItem` and `setItem` logic inside every component, we can create a custom hook.

`useLocalStorage.js`

```javascript
import { useState } from 'react';

function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const storedValue = localStorage.getItem(key);
    return storedValue ? JSON.parse(storedValue) : initialValue;
  });

  const updateValue = (newValue) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, updateValue];
}

export default useLocalStorage;

```

**Using the hook in a Component:**

```javascript
function ThemeToggler() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');

  return (
    <div>
      <h1>Current Theme: {theme}</h1>
      <button onClick={() => setTheme('dark')}>Dark Theme</button>
      <button onClick={() => setTheme('light')}>Light Theme</button>
    </div>
  );
}

```

* * *

### Interview Q&A

**Q: What exactly is a "Custom Hook" in simple terms?** A: Think of it as a specialized JavaScript function that "hooks" into React's capabilities. It allows you to package up a piece of logic so you can share it across different parts of your app without repeating yourself.

**Q: Why name it starting with** `use`**? Is it just for looks?** A: It is more than just a name! When you start a function name with `use`, you are telling React, "Hey, this function uses other React hooks inside it." It also helps tools like ESLint automatically check your code to ensure you are following the rules of hooks, which prevents common bugs.

**Q: Can’t I just use a regular helper function for this logic?** A: You *could* write a normal helper function, but it wouldn't be able to use React's built-in features like `useState` or `useEffect`. By making it a custom hook, you gain the ability to manage state and side effects, which makes it much more powerful for building UI-related logic.

**Q: When should I decide to extract logic into a custom hook?** A: A good rule of thumb is: if you find yourself writing the exact same "logic block" (like setting local storage, fetching from an API, or handling a form) in more than one component, it is time to move that logic into a custom hook.

* * *

*Are you currently working on a feature that involves repetitive logic, such as data fetching or form validation, that could be simplified into a custom hook?*