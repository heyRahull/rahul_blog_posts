---
title: "1.16 Separation of Concerns"
datePublished: 2026-06-18T15:58:57.623Z
cuid: cmqjoq4ae00000bj85lyk5wcq
slug: 1-16-separation-of-concerns

---

**Separation of Concerns (SoC)** is a fundamental architectural principle in frontend system design. It states that an application should be split into distinct sections, where each section addresses a specific "concern" or responsibility.

The core rule is simple: **Don't do everything in one place.** If your component is handling UI, business logic, API calls, and state management all at once, your code will become difficult to maintain as the application grows.

* * *

### Key Areas for Separation

To follow this principle effectively, break your application down into these layers:

#### 1\. Component Separation

Separate your **UI (Presentation)** from your **Logic (Container)**.

*   **Presentation Components:** Focus strictly on how things look (rendering).
    
*   **Container Components:** Focus on how things work (data fetching, business logic).
    
*   *Note:* If your logic is tiny, it is okay to keep it in the component. If it is complex or bulky, move it to a separate file.
    

#### 2\. Logic Separation

Use **Custom Hooks** for logic.

*   Because React is a reactive technology, custom hooks are the best place to handle logic that involves state or side effects (like API calls, authentication, or calculations).
    
*   For simple, non-reactive logic, standard **Utility or Helper files** are perfectly fine.
    

#### 3\. Folder Structure

Organize your project so that related files are grouped together. A recommended approach is **Feature-based structure**:

*   Within each feature folder, maintain clear sub-folders: `components/`, `hooks/`, `pages/`, `services/`, and `utils/`.
    
*   This keeps your project predictable—you always know exactly where to find a specific type of file.
    

#### 4\. API Layer Separation

Never write `fetch` or `axios` calls directly inside your UI components.

*   Create a dedicated `services/` folder.
    
*   Implement a **Base Service** file to handle common headers, interceptors, and error handling. This allows you to update API URLs or headers across the entire app by changing only one line of code.
    

#### 5\. State & Micro-Frontend Separation (Advanced)

*   **State Management:** Keep your state logic (Redux, Recoil, etc.) away from your UI files. Use dedicated slices or stores.
    
*   **Micro-Frontends:** If your application is massive, you can split it into entirely independent applications (e.g., a separate app for "Payments" vs. "Dashboard"). Only use this for very large systems.
    

* * *

### Summary Checklist

*   **Is my component only concerned with UI?** If yes, good.
    
*   **Is my business logic reused?** If yes, move it to a **Custom Hook**.
    
*   **Can I change my API base URL in one place?** If yes, your **API layer** is well-separated.
    
*   **Is my folder structure predictable?** You should be able to find any logic or UI file without guessing.
    

* * *

### Interview Q&A

**Q: What is the main benefit of "Separation of Concerns"?** A: It makes your code much easier to fix and update. When you separate UI from logic, you can change how a button looks without breaking the API call, and vice versa. It prevents your codebase from becoming a tangled "spaghetti" mess as your app gets bigger.

**Q: When should I move logic out of a component?** A: A good rule of thumb is "don't repeat yourself." If you find yourself writing the same logic in two different places, or if the logic is long and complicated, it's time to extract it into a custom hook or a utility file.

**Q: Why do we need a "Base Service" for API calls?** A: Imagine you have 50 components calling an API, and your backend developer changes the authentication token header. If you didn't use a base service, you’d have to go into 50 files to update that token. With a base service, you update it once, and the whole app is fixed.

**Q: Do I really need to use all these patterns for a small project?** A: For a very small project, you don't need to be overly strict. However, starting with these patterns early is a great habit. It teaches you how to think like a professional developer, which makes scaling your project later much easier.

* * *

*Is there a specific part of your current project that feels "messy" because it is handling too many responsibilities at once?*