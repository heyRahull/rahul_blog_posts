---
title: "React Interview Guide"
datePublished: 2026-07-16T13:48:15.931Z
cuid: cmrnkdw9s00010ai4gp3j17h4
slug: react-interview-guide

---

## ─── JAVASCRIPT & FOUNDATIONS ───

### Q1: Object Reference Puzzle (Output-Based)

**Question:** What is the output of the following code and why?

```javascript
const obj = { test: "Priyansh" };
const obj2 = obj;
obj2.test = "Khatri";

console.log(obj, obj2);

```

*   **The Perfect Answer:** Both `obj` and `obj2` will print `{ test: "Khatri" }`.
    
*   **The Core Concept:** In JavaScript, non-primitive data types like objects are assigned by **reference**, not by value. When we write `const obj2 = obj`, both variables point to the exact same memory address. Therefore, modifying a property via `obj2` directly alters the shared object in memory, affecting `obj` as well.
    
*   **Follow-up (How to avoid this):** This is called a **shallow copy** issue. To avoid mutating the original object, we can create a deep copy using `JSON.parse(JSON.stringify(obj))`, structural cloning (`structuredClone(obj)`), or a shallow clone using the spread operator (`{ ...obj }`) if there are no nested objects.
    

### Q2: Array Methods: `every()` vs `some()`

**Question:** What is the difference between the `every()` and `some()` array methods?

*   **The Perfect Answer:**
    
*   `every()` checks if **all** elements in the array satisfy the given condition. It returns `true` only if the callback returns `true` for every single item; otherwise, it returns `false`.
    
*   `some()` checks if **at least one** element in the array satisfies the condition. It short-circuits and returns `true` the moment it finds a single matching element; it returns `false` only if all elements fail the condition.
    

### Q3: Promises: `.then()` vs `.finally()`

**Question:** What is the difference between `.then()` and `.finally()` in Promise handling?

*   **The Perfect Answer:**
    
*   `.then()` is executed conditionally based on the fulfillment of the promise. It takes a callback to handle the successfully resolved data.
    
*   `.finally()` is executed **deterministically**, regardless of whether the promise was resolved or rejected. It is typically used for clean-up tasks (like stopping a loading spinner) because it is guaranteed to run after the promise settles.
    
*   **Follow-up (Rejection):** Rejections should be handled specifically using the `.catch()` block.
    

### Q4: Function Scoping: Normal Functions vs Arrow Functions

**Question:** What is the difference between a normal function and an arrow function?

*   **The Perfect Answer:**
    
*   **Syntactic Conciseness:** Arrow functions provide a shorter syntax and implicit returns for single-line statements.
    
*   `this` **Binding (Crucial):** Normal functions have their own dynamic `this` context, determined by *how* the function is invoked. Arrow functions do not have their own `this` binding; instead, they lexically inherit `this` from their surrounding parent scope.
    
*   **Hoisting:** Normal function declarations are fully hoisted (meaning they can be called before they are declared in the code). Arrow functions are typically assigned to `let` or `const` variables, meaning they are bound by variable hoisting rules and reside in the Temporal Dead Zone (TDZ) until execution reaches their declaration.
    

### Q5: Equality Comparison: Objects Comparison (`==` vs `===`)

**Question:** What is the output of `{} == {}` and `{} === {}` and why?

```javascript
console.log({} == {});
console.log({} === {});

```

*   **The Perfect Answer:** Both lines will print `false`.
    
*   **The Core Concept:** *The candidate in the video incorrectly said true.* In JavaScript, primitive values are compared by their value, but **objects are compared by their memory reference**. Every time you write `{}` (an empty object literal), JavaScript allocates a brand new object at a unique location in memory. Because these two empty objects reside at completely different memory addresses, their references are unequal, causing both loose (`==`) and strict (`===`) equality checks to return `false`.
    

### Q6: Event Loop Priorities: Promises vs `setTimeout`

**Question:** Asynchronous tasks run after synchronous code. Between a Promise and a `setTimeout`, which executes first?

*   **The Perfect Answer:** **Promises** will always execute before `setTimeout`.
    
*   **The Core Concept:** The Event Loop manages asynchronous operations through two queues: the **Microtask Queue** and the **Macrotask Queue** (or Callback Queue). Promises (`.then()` / `.catch()`) are placed into the Microtask Queue. `setTimeout` callbacks are web/browser APIs that get placed into the Macrotask Queue. The Event Loop is strictly prioritized to completely exhaust the Microtask Queue before moving on to pick up any tasks from the Macrotask Queue.
    

### Q7: Variables: `let`, `const`, and `var`

**Question:** What are the key differences between `let`, `const`, and `var`?

*   **The Perfect Answer:**
    
*   **Reassignment:** `let` and `var` variables can be reassigned new values. `const` variables are constant references and cannot be reassigned.
    
*   **Redeclaration:** `var` variables can be freely redeclared within the same scope. `let` and `const` variables will throw a syntax error if you attempt to redeclare them in the same block scope.
    
*   **Scoping:** `var` is function-scoped, whereas `let` and `const` are strictly block-scoped (confined to the closest `{}`).
    

### Q8: Closures & Lexical Scope

**Question:** What is a closure? Does the parent scope have access to a child scope's variables?

*   **The Perfect Answer:** A closure is a feature where an inner function retains access to the variables of its outer (lexical) parent scope, even after the outer function has completely finished executing.
    
*   **Scope Direction:** Scope accessibility is strictly a **one-way street**. An inner (child) scope can look upward to access variables in the outer (parent) scope. However, a parent scope has **no access** to variables declared inside a child scope.
    

### Q9: Callbacks & Higher-Order Functions (HOF)

**Question:** Define Callback functions and Higher-Order functions. Can a Higher-Order function take multiple function arguments?

*   **The Perfect Answer:**
    
*   **Callback Function:** A function passed as an argument into another function, intended to be executed at a later time.
    
*   **Higher-Order Function (HOF):** A function that operates on other functions, either by accepting one or more functions as arguments, returning a function, or both.
    
*   **Multiple Arguments:** **Yes**, a Higher-Order function can absolutely accept multiple functions as arguments. A classic real-world example is a custom composition function or a promise chain utility that accepts distinct success and failure callback functions.
    

### Q10: Iteration: `for...in` vs `for...of`

**Question:** What is the difference between a `for...in` loop and a `for...of` loop?

*   **The Perfect Answer:**
    
*   `for...in` iterates over the enumerable **keys (or property names)** of an object. It is primarily used to loop through object properties.
    
*   `for...of` iterates over the **values** generated by an iterable object (such as an Array, Map, Set, or String).
    

* * *

## ─── CODING LOGIC / ARRAY EXERCISE ───

### Q11: Array Separation Challenge

**Question:** Given an array containing mixed types: `[10, 20, 30, 'C', 80, 'Riyansh', 7, 'Khatri', 'J', 'L']`. Write a clean function to separate numbers, single characters, and long strings into different arrays.

*   **The Perfect Answer:**
    

```javascript
const mixedArray = [10, 20, 30, 'C', 80, 'Riyansh', 7, 'Khatri', 'J', 'L'];

function separateData(arr) {
  const numbers = [];
  const characters = [];
  const strings = [];

  arr.forEach(item => {
    if (typeof item === 'number') {
      numbers.push(item);
    } else if (typeof item === 'string') {
      // Check length to distinguish single characters from strings
      if (item.length === 1) {
        characters.push(item);
      } else {
        strings.push(item);
      }
    }
  });

  return { numbers, characters, strings };
}

console.log(separateData(mixedArray));

```

* * *

## ─── REACT.JS ARCHITECTURE ───

### Q12: Rendering: SSR vs CSR

**Question:** What is the difference between Server-Side Rendering (SSR) and Client-Side Rendering (CSR)?

*   **The Perfect Answer:**
    
*   **CSR (Client-Side Rendering):** The server sends a bare-minimum HTML file along with a large bundle of JavaScript. The user's browser executes this JavaScript to build the DOM dynamically.
    
*   **SSR (Server-Side Rendering):** The server executes the components on the backend, populates the layout with real data, and compiles a fully constructed HTML page to send back to the client.
    
*   **Trade-offs:** SSR provides a significantly faster **First Contentful Paint (FCP)**. It is highly beneficial for **SEO** because search engine web crawlers can immediately parse the pre-rendered HTML text without having to execute complex client-side script bundles first.
    

### Q13: State Mutability & Props

**Question:** Are React `props` and `state` mutable or immutable?

*   **The Perfect Answer:** **Both are strictly immutable** from the perspective of the component using them.
    
*   **Props** are read-only values passed down from a parent component. A child component must never directly modify its received props.
    
*   **State** represents local component memory. It must never be mutated directly (e.g., writing `state.value = 10` is an anti-pattern). Instead, state transitions must always be triggered via its designated setter function (e.g., `setState()`), which notifies React to schedule a clean re-render.
    

### Q14: State Management: Prop Drilling vs Context API

**Question:** What is prop drilling and how do you prevent it?

*   **The Perfect Answer:** Prop drilling is the process of passing down data through multiple layers of intermediate nested components solely to deliver that data to a deeply targeted child component that needs it. This clutters the intermediate components with unnecessary props.
    
*   **Prevention:** We prevent this by utilizing the **Context API** (or global state libraries like Redux). The Context API provides a `Provider` wrapper that allows any deeply nested component to subscribe directly to the data stream, bypassing intermediate layers entirely.
    

### Q15: Performance: `useMemo`, `useCallback`, and Memoization

**Question:** Explain Memoization. What is the difference between `useMemo` and `useCallback`?

*   **The Perfect Answer:**
    
*   **Memoization:** An optimization technique where the result of an expensive operation is cached based on its inputs. If the inputs don't change on subsequent renders, the cached result is returned directly, saving processing time.
    
*   `useMemo`**:** Used to memoize and cache a calculated **computed value** (e.g., sorting a massive dataset).
    
*   `useCallback`**:** Used to memoize and cache a **function definition itself** across renders. This prevents child components from unnecessarily re-rendering when a function callback is passed down as a prop.
    

### Q16: DOM Mechanics: Real DOM vs Virtual DOM

**Question:** What is the core difference between the Real DOM and the Virtual DOM?

*   **The Perfect Answer:** The Real DOM is the browser's actual HTML node tree structure. Updating it directly is computationally expensive. The **Virtual DOM** is a lightweight, in-memory JavaScript object representation of the Real DOM. When state changes, React updates the Virtual DOM first, runs a **diffing algorithm** to compare the new Virtual DOM snapshot against the previous one, and calculates the exact minimum changes needed. It then applies only those targeted patches to the Real DOM via a batch process called **reconciliation**.
    

### Q17: Higher-Order Components (HOC) & Real-World Use Case

**Question:** What is a Higher-Order Component (HOC)? Share a practical scenario where you have built or used one.

*   **The Perfect Answer:** A Higher-Order Component is an advanced architectural pattern in React where a function takes a component as an argument and returns an enhanced component injected with extra features or data.
    
*   **Real-World Case:** **Authentication Shielding.** You can create a `withAuth(WrappedComponent)` HOC. Inside the HOC, you evaluate if the client possesses a valid authentication session token. If authenticated, it renders the `WrappedComponent` (like a Protected Dashboard); if unauthenticated, it intercepts the route and uses a redirect component to steer them out to the Login Page.
    

### Q18: Architecture Terms: DOM Storage, Babel, Webpack, & Lifting State Up

**Question:** Define where the DOM is stored, Babel, Webpack, and the concept of "Lifting State Up".

*   **The Perfect Answer:** *The candidate failed these completely in the video. Knowing these gives you an edge:*
    
*   **Where the DOM is stored:** The DOM is stored in the browser's memory heap as a structured object representation of the document.
    
*   **Babel:** A JavaScript compiler/transpiler. It converts modern ECMAScript code (ES6+) and JSX syntax into backward-compatible versions of JavaScript that older web browsers can easily parse and execute.
    
*   **Webpack:** A static module bundler for modern JavaScript applications. It takes all individual project dependency files (JS, JSX, CSS, images) and processes them into optimized, minified bundles for production deployment.
    
*   **Lifting State Up:** A React architectural design pattern. When two or more sibling components need access to the same shared state, you "lift" that state up to their closest common parent component, passing it back down via props to ensure a single source of truth.
    

### Q19: React Critique: SEO & Routing Challenges

**Question:** What are some disadvantages or structural limitations of React compared to frameworks like Next.js?

*   **The Perfect Answer:** Standard React (CRA/Vite configurations) acts primarily as a client-side SPA library.
    
*   **SEO Limitations:** Because rendering happens entirely on the client, initial pages appear empty to older web crawlers, creating SEO indexation challenges compared to Next.js's native SSR.
    
*   **Routing:** React does not feature built-in routing. Developers must configure external libraries like `react-router-dom` manually, whereas Next.js provides file-system-based routing natively out-of-the-box.
    
*   **Dynamic Routing Hook:** When performing dynamic routing (e.g., matching a path like `/user/:id`), React Router uses the `useParams()` hook to extract parameters from the URL path.
    

* * *

## ─── WEB HYPERTEXT & STYLING ───

### Q20: HTML Tables: Layout Attributes

**Question:** How do you club multiple cells together in an HTML table? What is the difference between cell padding and cell spacing?

*   **The Perfect Answer:**
    
*   **Clubbing Cells:** Use the `rowspan` attribute to merge cells vertically across rows, and the `colspan` attribute to merge cells horizontally across columns.
    
*   **Cell Padding vs Cell Spacing:** *The candidate missed this.* Cell padding controls the clear space *inside* a cell between its border and the content. Cell spacing controls the physical space *between* separate individual table cells.
    

### Q21: HTML Typography: `<em>` vs `<i>` Tags

**Question:** What is the structural difference between using the `<em>` tag and the `<i>` tag?

*   **The Perfect Answer:** *The candidate missed this.* While both render text in italics by default, they carry distinct semantic meanings:
    
*   `<em>` **(Emphasis):** Carries semantic importance and structural stress. Screen readers will emphasize the spoken tone of words wrapped in this tag.
    
*   `<i>` **(Idiomatic/Italic):** Purely typographic style choice used to differentiate text (like technical terms or idiomatic phrases) without assigning any extra semantic weight or structural emphasis.
    

### Q22: CSS Visibility Layout: `display: none` vs `visibility: hidden`

**Question:** What is the structural difference between `display: none` and `visibility: hidden`?

*   **The Perfect Answer:**
    
*   `visibility: hidden` hides the element from view, but the element **still occupies its physical space** in the document layout flow. It acts like an invisible box.
    
*   `display: none` completely removes the element from the document rendering tree. The element vanishes entirely, and surrounding elements collapse to fill the layout space it occupied.
    

### Q23: CSS Specificity: Selectors Priority

**Question:** Rank the priority/specificity order among Universal (`*`), Class (`.`), and ID (`#`) selectors. Which holds the absolute least priority?

*   **The Perfect Answer:** The priority ranking from highest specificity to lowest is:
    

1.  **ID Selector (**`#`**)** - Highest Priority
    
2.  **Class Selector (**`.`**)** - Medium Priority
    
3.  **Universal Selector (**`*`**)** - Absolute Lowest Priority
    

*   **The Core Concept:** *The candidate mistakenly said class had the lowest priority first.* The Universal selector (`*`) targets everything globally but has a specificity weight of 0, meaning any class or ID selector will easily overwrite it.
    

### Q24: CSS Architecture: Group Selectors

**Question:** What is a CSS group selector?

*   **The Perfect Answer:** A group selector is a comma-separated list used to apply the exact same set of style rules to multiple selectors simultaneously. For example: `h1, h2, p, .custom-div { color: blue; }`. This keeps stylesheets DRY (Don't Repeat Yourself) by preventing code duplication.
    

## ─── JAVASCRIPT TYPES & ENGINE EQUATIONS ───

### Q1: Loose Equality & Falsy Values Puzzle (Output-Based)

**Question:** What is the output of the following checks and why?

```javascript
var x = 0;
var y = "0";
var z = false;

console.log(x == y);
console.log(x == z);

```

*   **The Perfect Answer:** Both expressions evaluate to `true`.
    
*   **The Core Concept:**
    
*   `x == y`**:** Loose equality triggers **implicit type coercion**. When comparing a number to a string, JavaScript converts the string to a number under the hood. `"0"` becomes `0`, matching `x`, so it returns `true`.
    
*   `x == z`**:** JavaScript converts boolean values to numbers during loose comparison. `false` converts to `0` (and `true` to `1`). Because `0 == 0`, this also evaluates to `true`.
    

### Q2: `null` vs `undefined` Equality Checks

**Question:** What do `null == undefined` and `null === undefined` return, and what are their data types?

*   **The Perfect Answer:**
    
*   `null == undefined` evaluates to `true` because JavaScript explicitly treats them as loosely equal to each other.
    
*   `null === undefined` evaluates to `false` because they are entirely different data types.
    
*   **The Types:** `typeof undefined` is `"undefined"`, whereas `typeof null` returns `"object"`. Note: The candidate in the video incorrectly claimed that the type of null is "undefined" or an object; remember, it is treated as an object due to a historical quirk in JavaScript.
    

### Q3: Direct Primitive Evaluation: `NaN`, `[] + []`, and `{} + {}`

**Question:** What are the evaluation outputs of `typeof appointments/NaN`, `[] + []`, and `{} + {}`?

*   **The Perfect Answer:**
    
*   `typeof NaN` returns `"number"`. Even though `NaN` stands for "Not-a-Number", it is technically categorized under the numeric type system in JavaScript.
    
*   `[] + []` returns an **empty string (**`""`**)**. The candidate incorrectly guessed `2` or `0`. During addition, JavaScript attempts to convert arrays into primitive values. An empty array strings out to `""`, resulting in `"" + ""` which yields an empty string.
    
*   `{} + {}` behaves conditionally based on context. In standard console evaluations, JavaScript interprets the first `{}` as an empty code block and treats `+ {}` as a unary plus operation on an empty object, returning `NaN`. However, if evaluated directly as an expression (e.g., `console.log({} + {})`), it treats both as objects, coerces them to strings, and returns `"[object Object][object Object]"`.
    

### Q4: JavaScript Native Errors

**Question:** Name the common native error types built into JavaScript.

*   **The Perfect Answer:** The candidate mistakenly guessed "UI Error". The native error structures are:
    
*   `ReferenceError`**:** Thrown when referencing a variable that does not exist in the current scope.
    
*   `TypeError`**:** Thrown when an operation is performed on an incompatible data type (e.g., invoking a string as a function).
    
*   `SyntaxError`**:** Thrown when the interpreter encounters code that breaks standard language syntax rules.
    
*   `RangeError`**:** Thrown when a numeric value falls outside its allowable range limits.
    

### Q5: Understanding the Temporal Dead Zone (TDZ)

**Question:** Explain what the Temporal Dead Zone (TDZ) is and point out exactly where it lies in a program.

*   **The Perfect Answer:** The Temporal Dead Zone is the specific region of a program execution block from the start of the block scope until the moment the variable is officially declared.
    
*   **The Rules:** Variables declared with `let` and `const` are hoisted, but they are not initialized with a default value. Attempting to access them within this zone throws a `ReferenceError`.
    

```javascript
function checkTDZ() {
  // === TEMPORAL DEAD ZONE FOR variable 'a' START ===
  console.log(a); // Throws ReferenceError!
  // === TEMPORAL DEAD ZONE FOR variable 'a' END ===
  
  let a = 10; // Variable is initialized here.
  console.log(a); // Safe to access: prints 10.
}

```

### Q6: Function Expressions vs Anonymous Functions & Hoisting

**Question:** What is an anonymous function, and are function expressions or arrow functions hoisted?

*   **The Perfect Answer:** An **anonymous function** is a function declaration defined without an explicit name identifier. **Function expressions** and **arrow functions** are not structurally hoisted as functions. If they are assigned to `var`, the variable initialization hoists as `undefined`. If assigned to `let` or `const`, they remain trapped inside the Temporal Dead Zone until execution reaches their assignment lines.
    

### Q7: Mathematical Operations on Strings

**Question:** What does evaluation of `"Raza" - "Ali"` return?

*   **The Perfect Answer:** It evaluates to `NaN` **(Not-a-Number)**. The subtraction operator (`-`) forces arithmetic type conversion. Because standard text strings cannot be mathematically converted into numbers, the operation fails and yields `NaN`.
    

### Q8: Optional Chaining Purpose (`?.`)

**Question:** Why do we use optional chaining (`?.`) instead of standard dot notation?

*   **The Perfect Answer:** Optional chaining permits developers to safely read nested properties deep within an object tree without explicitly checking every step of the reference path for validity. If a reference is `null` or `undefined`, the expression short-circuits gracefully and returns `undefined` rather than throwing a runtime crash error.
    

### Q9: Native Pop-up Frameworks

**Question:** Name the three types of native window pop-up interfaces provided by JavaScript.

*   **The Perfect Answer:**
    
*   `alert()`**:** Displays a simple informational modal window with an "OK" confirmation option.
    
*   `confirm()`**:** Displays a modal interface offering conditional "OK" and "Cancel" choices, returning a boolean value.
    
*   `prompt()`**:** Opens an input dialog window enabling users to submit text entries directly back to the script.
    

* * *

## ─── LIVE RUN CODING EXERCISES ───

### Q10: Printing Only Negative Numbers from an Array

**Question:** Write a function that takes an array containing positive and negative values and returns a clean array containing only the negative entries.

*   **The Perfect Answer:**
    

```javascript
const mixedNumbers = [100, -200, 300, -400, 800, -800];

function extractNegatives(arr) {
  // Use filter to cleanly isolate values below 0
  return arr.filter(num => num < 0);
}

console.log(extractNegatives(mixedNumbers)); // Output: [-200, -400, -800]

```

### Q11: Calculate a Factorial via Recursion

**Question:** Write a recursive function to compute the factorial of a given number (e.g., 10).

*   **The Perfect Answer:**
    

```javascript
function getFactorial(num) {
  // Base case termination check
  if (num === 0 || num === 1) {
    return 1;
  }
  // Recursive case: multiply number by structural decrement
  return num * getFactorial(num - 1);
}

console.log(getFactorial(10)); // Output: 3628800

```

### Q12: Character Count Tracker

**Question:** Given a string string variable like `"Ali Raza"`, write a function that returns an object mapping the total occurrences of each unique character present.

*   **The Perfect Answer:**
    

```javascript
const targetString = "Ali Raza";

function countCharacters(str) {
  const charMap = {};
  // Sanitize string to lowercase and remove spacing if needed
  const cleanStr = str.toLowerCase().replace(/\s+/g, '');

  for (let char of cleanStr) {
    charMap[char] = (charMap[char] || 0) + 1;
  }
  
  return charMap;
}

console.log(countCharacters(targetString)); 
// Output: { a: 3, l: 1, i: 1, r: 1, z: 1 }

```

* * *

## ─── STRUCTURAL HTML & STYLING ───

### Q13: Image Maps & Semantic Entities

**Question:** What are HTML Entities and what is an HTML Image Map?

*   **The Perfect Answer:**
    
*   **HTML Entities:** Specialized string character sets (e.g., `&nbsp;` for non-breaking spaces or `&copy;` for the copyright icon) used to display reserved characters or symbols that could otherwise interfere with HTML parsing engines.
    
*   **Image Map:** A system achieved by nesting `<map>` and `<area>` tags inside an image component. It maps out coordinates over an image layout, turning specific coordinates into independent, clickable hyperlinks.
    

### Q14: CSS Implementation Models & Specificity Priorities

**Question:** What are the three methods of styling, and which holds the highest specificity weight?

*   **The Perfect Answer:** The three core integration patterns are **Inline**, **Internal (Embedded)**, and **External stylesheets**. **Inline styles** (written directly inside the element's `style` attribute) command the highest priority weight and override rules declared externally.
    

### Q15: Advanced Syntax Selectors: Pseudo-Classes vs Pseudo-Elements

**Question:** What is the distinction between a Pseudo-Class and a Pseudo-Element, and how are they syntactically denoted?

*   **The Perfect Answer:**
    
*   **Pseudo-Class:** Targets a specific state changes of an existing element (e.g., `:hover`, `:active`, `:focus`). They are denoted using a **single colon (**`:`**)**.
    
*   **Pseudo-Element:** Targets a specific sub-part of an element's structural content layout (e.g., `::before`, `::after`, `::first-line`). They are denoted using a **double colon (**`::`**)**.
    

* * *

Review these rules—especially the exact data outputs of array string additions (`[]+[]`) and pseudo-selector notation definitions (`:` vs `::`). They are common traps that highlight deep architectural knowledge!

## ─── JAVASCRIPT TYPES & ENGINE EQUATIONS ───

### Q1: Loose Equality & Falsy Values Puzzle (Output-Based)

**Question:** What is the output of the following checks and why?

```javascript
var x = 0;
var y = "0";
var z = false;

console.log(x == y);
console.log(x == z);

```

*   **The Perfect Answer:** Both expressions evaluate to `true`.
    
*   **The Core Concept:**
    
*   `x == y`**:** Loose equality triggers **implicit type coercion**. When comparing a number to a string, JavaScript converts the string to a number under the hood. `"0"` becomes `0`, matching `x`, so it returns `true`.
    
*   `x == z`**:** JavaScript converts boolean values to numbers during loose comparison. `false` converts to `0` (and `true` to `1`). Because `0 == 0`, this also evaluates to `true`.
    

### Q2: `null` vs `undefined` Equality Checks

**Question:** What do `null == undefined` and `null === undefined` return, and what are their data types?

*   **The Perfect Answer:**
    
*   `null == undefined` evaluates to `true` because JavaScript explicitly treats them as loosely equal to each other.
    
*   `null === undefined` evaluates to `false` because they are entirely different data types.
    
*   **The Types:** `typeof undefined` is `"undefined"`, whereas `typeof null` returns `"object"`. Note: The candidate in the video incorrectly claimed that the type of null is "undefined" or an object; remember, it is treated as an object due to a historical quirk in JavaScript.
    

### Q3: Direct Primitive Evaluation: `NaN`, `[] + []`, and `{} + {}`

**Question:** What are the evaluation outputs of `typeof appointments/NaN`, `[] + []`, and `{} + {}`?

*   **The Perfect Answer:**
    
*   `typeof NaN` returns `"number"`. Even though `NaN` stands for "Not-a-Number", it is technically categorized under the numeric type system in JavaScript.
    
*   `[] + []` returns an **empty string (**`""`**)**. The candidate incorrectly guessed `2` or `0`. During addition, JavaScript attempts to convert arrays into primitive values. An empty array strings out to `""`, resulting in `"" + ""` which yields an empty string.
    
*   `{} + {}` behaves conditionally based on context. In standard console evaluations, JavaScript interprets the first `{}` as an empty code block and treats `+ {}` as a unary plus operation on an empty object, returning `NaN`. However, if evaluated directly as an expression (e.g., `console.log({} + {})`), it treats both as objects, coerces them to strings, and returns `"[object Object][object Object]"`.
    

### Q4: JavaScript Native Errors

**Question:** Name the common native error types built into JavaScript.

*   **The Perfect Answer:** The candidate mistakenly guessed "UI Error". The native error structures are:
    
*   `ReferenceError`**:** Thrown when referencing a variable that does not exist in the current scope.
    
*   `TypeError`**:** Thrown when an operation is performed on an incompatible data type (e.g., invoking a string as a function).
    
*   `SyntaxError`**:** Thrown when the interpreter encounters code that breaks standard language syntax rules.
    
*   `RangeError`**:** Thrown when a numeric value falls outside its allowable range limits.
    

### Q5: Understanding the Temporal Dead Zone (TDZ)

**Question:** Explain what the Temporal Dead Zone (TDZ) is and point out exactly where it lies in a program.

*   **The Perfect Answer:** The Temporal Dead Zone is the specific region of a program execution block from the start of the block scope until the moment the variable is officially declared.
    
*   **The Rules:** Variables declared with `let` and `const` are hoisted, but they are not initialized with a default value. Attempting to access them within this zone throws a `ReferenceError`.
    

```javascript
function checkTDZ() {
  // === TEMPORAL DEAD ZONE FOR variable 'a' START ===
  console.log(a); // Throws ReferenceError!
  // === TEMPORAL DEAD ZONE FOR variable 'a' END ===
  
  let a = 10; // Variable is initialized here.
  console.log(a); // Safe to access: prints 10.
}

```

### Q6: Function Expressions vs Anonymous Functions & Hoisting

**Question:** What is an anonymous function, and are function expressions or arrow functions hoisted?

*   **The Perfect Answer:** An **anonymous function** is a function declaration defined without an explicit name identifier. **Function expressions** and **arrow functions** are not structurally hoisted as functions. If they are assigned to `var`, the variable initialization hoists as `undefined`. If assigned to `let` or `const`, they remain trapped inside the Temporal Dead Zone until execution reaches their assignment lines.
    

### Q7: Mathematical Operations on Strings

**Question:** What does evaluation of `"Raza" - "Ali"` return?

*   **The Perfect Answer:** It evaluates to `NaN` **(Not-a-Number)**. The subtraction operator (`-`) forces arithmetic type conversion. Because standard text strings cannot be mathematically converted into numbers, the operation fails and yields `NaN`.
    

### Q8: Optional Chaining Purpose (`?.`)

**Question:** Why do we use optional chaining (`?.`) instead of standard dot notation?

*   **The Perfect Answer:** Optional chaining permits developers to safely read nested properties deep within an object tree without explicitly checking every step of the reference path for validity. If a reference is `null` or `undefined`, the expression short-circuits gracefully and returns `undefined` rather than throwing a runtime crash error.
    

### Q9: Native Pop-up Frameworks

**Question:** Name the three types of native window pop-up interfaces provided by JavaScript.

*   **The Perfect Answer:**
    
*   `alert()`**:** Displays a simple informational modal window with an "OK" confirmation option.
    
*   `confirm()`**:** Displays a modal interface offering conditional "OK" and "Cancel" choices, returning a boolean value.
    
*   `prompt()`**:** Opens an input dialog window enabling users to submit text entries directly back to the script.
    

* * *

## ─── LIVE RUN CODING EXERCISES ───

### Q10: Printing Only Negative Numbers from an Array

**Question:** Write a function that takes an array containing positive and negative values and returns a clean array containing only the negative entries.

*   **The Perfect Answer:**
    

```javascript
const mixedNumbers = [100, -200, 300, -400, 800, -800];

function extractNegatives(arr) {
  // Use filter to cleanly isolate values below 0
  return arr.filter(num => num < 0);
}

console.log(extractNegatives(mixedNumbers)); // Output: [-200, -400, -800]

```

### Q11: Calculate a Factorial via Recursion

**Question:** Write a recursive function to compute the factorial of a given number (e.g., 10).

*   **The Perfect Answer:**
    

```javascript
function getFactorial(num) {
  // Base case termination check
  if (num === 0 || num === 1) {
    return 1;
  }
  // Recursive case: multiply number by structural decrement
  return num * getFactorial(num - 1);
}

console.log(getFactorial(10)); // Output: 3628800

```

### Q12: Character Count Tracker

**Question:** Given a string string variable like `"Ali Raza"`, write a function that returns an object mapping the total occurrences of each unique character present.

*   **The Perfect Answer:**
    

```javascript
const targetString = "Ali Raza";

function countCharacters(str) {
  const charMap = {};
  // Sanitize string to lowercase and remove spacing if needed
  const cleanStr = str.toLowerCase().replace(/\s+/g, '');

  for (let char of cleanStr) {
    charMap[char] = (charMap[char] || 0) + 1;
  }
  
  return charMap;
}

console.log(countCharacters(targetString)); 
// Output: { a: 3, l: 1, i: 1, r: 1, z: 1 }

```

* * *

## ─── STRUCTURAL HTML & STYLING ───

### Q13: Image Maps & Semantic Entities

**Question:** What are HTML Entities and what is an HTML Image Map?

*   **The Perfect Answer:**
    
*   **HTML Entities:** Specialized string character sets (e.g., `&nbsp;` for non-breaking spaces or `&copy;` for the copyright icon) used to display reserved characters or symbols that could otherwise interfere with HTML parsing engines.
    
*   **Image Map:** A system achieved by nesting `<map>` and `<area>` tags inside an image component. It maps out coordinates over an image layout, turning specific coordinates into independent, clickable hyperlinks.
    

### Q14: CSS Implementation Models & Specificity Priorities

**Question:** What are the three methods of styling, and which holds the highest specificity weight?

*   **The Perfect Answer:** The three core integration patterns are **Inline**, **Internal (Embedded)**, and **External stylesheets**. **Inline styles** (written directly inside the element's `style` attribute) command the highest priority weight and override rules declared externally.
    

### Q15: Advanced Syntax Selectors: Pseudo-Classes vs Pseudo-Elements

**Question:** What is the distinction between a Pseudo-Class and a Pseudo-Element, and how are they syntactically denoted?

*   **The Perfect Answer:**
    
*   **Pseudo-Class:** Targets a specific state changes of an existing element (e.g., `:hover`, `:active`, `:focus`). They are denoted using a **single colon (**`:`**)**.
    
*   **Pseudo-Element:** Targets a specific sub-part of an element's structural content layout (e.g., `::before`, `::after`, `::first-line`). They are denoted using a **double colon (**`::`**)**.
    

* * *

Review these rules—especially the exact data outputs of array string additions (`[]+[]`) and pseudo-selector notation definitions (`:` vs `::`). They are common traps that highlight deep architectural knowledge!