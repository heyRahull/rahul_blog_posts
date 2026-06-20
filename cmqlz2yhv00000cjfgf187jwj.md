---
title: "1.1 ReactJs Interview Experience"
datePublished: 2026-06-20T06:24:25.167Z
cuid: cmqlz2yhv00000cjfgf187jwj
slug: 1-1-reactjs-interview-experience

---

### Q1: Problem Statement – Alternately Merging Two Strings

**Problem:** You are given two strings, `str1` and `str2`. Write a function to merge them by alternating characters, starting with the first character of `str1`. If one string is longer than the other, append the remaining characters of the longer string to the end of the merged result.

#### Implementation:

```javascript
const str1 = "Pravin";
const str2 = "Kumar";

function mergeAlternately(s1, s2) {
    let result = "";
    // Find the maximum length to loop completely through both strings
    const maxLength = Math.max(s1.length, s2.length);
    
    for (let i = 0; i < maxLength; i++) {
        if (i < s1.length) result += s1[i]; // Add character from first string
        if (i < s2.length) result += s2[i]; // Add character from second string
    }
    return result;
}

console.log(mergeAlternately(str1, str2)); 
// Output: "Pkruatmarvin"

```

* * *

### Q2: What is Function Currying, and what actual problem does it solve?

**Answer:** Function currying is a technique where a function that expects multiple arguments is transformed into a nested series of functions that each take exactly **one** argument at a time (e.g., turning `sum(a, b)` into `sum(a)(b)`).

**The Actual Need:** Without currying, if you have a function where certain arguments stay the same but the final argument changes, you are forced to type those identical configurations over and over again. Currying allows you to lock in those common values *once* to create highly specialized helper functions. This prevents code repetition and ensures that if a configuration value changes, you only have to update it in one single place.

#### The Shopping Cart E-Commerce Example:

Imagine you have an e-commerce store and want to calculate final prices after adding a flat **$5 shipping fee**.

```javascript
// The Curried Function
const curriedAdd = (shippingFee) => (itemPrice) => shippingFee + itemPrice;

// Lock in the $5 configuration fee once
const addFiveDollarShipping = curriedAdd(5); 

// Now you don't repeat the '5' throughout your app! Just pass the unique item prices
const item1Total = addFiveDollarShipping(20); // $25 (Shirt)
const item2Total = addFiveDollarShipping(45); // $50 (Shoes)
const item3Total = addFiveDollarShipping(12); // $17 (Socks)

```

*(Note: Simple constants like* `const SHIPPING = 5` *only work for static, hardcoded values. Currying is essential when this configuration data is dynamic—like user roles, language preferences, or theme settings fetched on the fly at runtime.)*

* * *

### Q3: What is the difference between Synchronous and Asynchronous code execution in JavaScript?

**Answer:** \* **Synchronous:** JavaScript is natively a single-threaded language. It executes code sequentially, line-by-line. If a line of code takes a long time to run, it blocks everything beneath it.

*   **Asynchronous:** Non-blocking behavior. When JavaScript encounters a time-consuming task (like a network request or a timer), it offloads it to the browser's Web APIs to execute in the background. This allows the main thread to keep running the remaining synchronous code without freezing the user interface.
    

* * *

### Q4: What is Event Loop Latency, and how does it happen?

**Answer:** **Event Loop Latency** is the time delay between an asynchronous task landing in the callback queue (meaning it is 100% ready to run) and the moment it actually gets pushed into the Call Stack to execute.

This happens because the Event Loop has one golden rule: **Never move tasks from the queue if the Call Stack is busy.** If your main thread is executing a heavy, time-consuming synchronous block of code, ready background tasks are forced to sit and wait in line, creating latency.

#### The Timeline Example:

```javascript
setTimeout(() => console.log("Hello"), 1000); // 1-second background timer
blockStackForFiveSeconds(); // Intense synchronous loop that takes 5 seconds

```

1.  **At 1 Second:** The background timer finishes. The callback `() => console.log("Hello")` lands in the **Callback Queue**. It is ready.
    
2.  **From 1 to 5 Seconds:** The callback sits frozen in the queue. It cannot move because the Call Stack is completely jammed executing the 5-second loop. This 4-second gap of waiting is the **Event Loop Latency**.
    
3.  **At 5 Seconds:** The loop finishes, the stack clears, and the Event Loop finally runs your callback.
    

* * *

### Q5: What is the difference between Null and Undefined?

**Answer:** \* `undefined`**:** JavaScript's default state. It means a variable has been declared, but has not been assigned a value yet.

*   `null`**:** An intentional assignment. A developer explicitly sets a variable to `null` to indicate that it is empty or has no value right now.
    

* * *

### Q6: What is the difference between Functional Scope and Block Scope?

**Answer:** \* **Functional Scope (**`var`**):** Variables are accessible anywhere inside the function they were created in, ignoring smaller blocks like loops or `if` conditions.

*   **Block Scope (**`let`**,** `const`**):** Variables are strictly locked inside the nearest pair of curly braces `{}` (such as an `if` statement or a `for` loop). Trying to access them outside that block causes an error.
    

* * *

### Q7: What is the difference between Primitive and Non-Primitive data types?

**Answer:** \* **Primitive Types:** Hold a single, unchangeable (immutable) value directly in stack memory (e.g., `String`, `Number`, `Boolean`, `null`, `undefined`).

*   **Non-Primitive Types:** Hold a reference to a complex collection of mutable data stored in heap memory (e.g., `Objects` and `Arrays`).
    

* * *

### Q8: What is Hoisting and the Temporal Dead Zone (TDZ)?

**Answer:** **Hoisting** is JavaScript's default compilation behavior where variable and function declarations are allocated memory space before any line of code runs.

*   Variables declared with `var` are hoisted and automatically given a value of `undefined`.
    
*   Variables declared with `let` and `const` **are** hoisted, but they are not initialized with a value. They enter a restricted state called the **Temporal Dead Zone (TDZ)** from the start of the block until the line of code that initializes them is executed. Accessing them inside the TDZ throws a `ReferenceError`.
    

* * *

### Q9: What is the output of `isNaN('Pravin')` and why?

**Answer:** The output is `true`.

*   **Why:** The global `isNaN()` function forces its input argument into a number type first. Because the text string `'Pravin'` cannot be logically converted into a number, it turns into `NaN` (Not-a-Number). Since the value is indeed `NaN`, the function returns `true`.
    

* * *

### Q10: Why do we use the Context API in React?

**Answer:** We use the Context API to prevent **prop drilling** (the annoying process of passing props down through multiple layers of intermediate components that don't actually need the data, just to reach a deeply nested child). Context creates a global data broadcast system so any child component can tap into the data directly.

* * *

### Q11: What is the core difference between State and Props in React?

**Answer:** \* **State:** Local data created and managed *inside* the component itself. The component can freely mutate its own state using its state setter function. When state updates, the component re-renders.

*   **Props:** Configuration parameters passed down from a *parent* component to a child. From the child's perspective, **props are strictly read-only and immutable**.
    

* * *

### Q12: What is the difference between the HTML tags `<em>` and `<i>`?

**Answer:** While both visually render text as italics in a browser, their structural meanings are completely different:

*   `<i>` is purely presentational. It italicizes text visually without adding any structural importance.
    
*   `<em>` is a **semantic tag**. It denotes **emphasis**. Screen readers will read the text inside an `<em>` tag with a changed vocal inflection, and search engines recognize it as contextually important text.
    

* * *

### Q13: Explain CSS Combinators with a code layout.

**Answer:** CSS Combinators let you precision-target elements based on how they are nested or situated next to each other.

#### Given this HTML structure:

```html
<div class="parent">
    <p>Direct Child 1</p>
    <span>
        <p>Grandchild (Nested Deep)</p>
    </span>
    <h3>An unrelated heading</h3>
    <p>Sibling 1</p>
    <p>Sibling 2</p>
</div>

```

#### How Combinators work:

*   **Descendant (**`space`**):** Matches any matching element anywhere inside the container.
    

```css
/* Targets ALL three <p> tags (Direct Child 1, and Grandchild) */
.parent p { color: blue; }

```

*   **Child (**`>`**):** Matches **only** direct, immediate children.
    

```css
/* Targets ONLY Direct Child 1 (Will NOT look inside the <span>) */
.parent > p { color: green; }

```

*   **Adjacent Sibling (**`+`**):** Matches the very next element, but only if it sits on the exact same nesting level.
    

```css
/* Targets ONLY Sibling 1 (because it immediately follows the <h3>) */
h3 + p { color: red; }

```

*   **General Sibling (**`~`**):** Matches all matching elements that follow, even if other tags sit in between.
    

```css
/* Targets BOTH Sibling 1 and Sibling 2 */
h3 ~ p { color: purple; }

```

* * *

### Q14: How does the CSS Specificity Hierarchy work?

**Answer:** When multiple CSS rules conflict over the same element, the browser calculates a mathematical score to decide which style wins. You can think of this score as a 4-digit hierarchy scale:

$$\text{Inline Styles (1000)} > \text{ID Selectors (100)} > \text{Class / Pseudo-class (10)} > \text{Element / Tag (1)} > \text{Universal Selector (*) (0)}$$

#### Conflict Example:

```html
<button id="submit-btn" class="btn-primary" style="color: white;">Click Me</button>

```

```css
button { color: red; }         /* Score: 0, 0, 0, 1  (Element score) */
.btn-primary { color: green; } /* Score: 0, 0, 1, 0  (Class beats Element) */
#submit-btn { color: blue; }   /* Score: 0, 1, 0, 0  (ID beats Class) */
/* Inline Style won't even need a CSS rule, style="color: white;" beats them all! */

```

The **Universal Selector (**`*`**)** targets everything globally but has a specificity score of **0**, putting it at the absolute bottom of the priority ladder.

* * *

### The Perfect Blueprint Prompt for Next Time

Save this exact prompt text. Next time you copy-paste an interview transcript, paste this prompt right along with it:

```text
Please convert the following raw interview transcript into a highly polished, beginner-friendly technical blog structured in a clean Question-and-Answer (Q&A) format. Follow these structural constraints perfectly:

1. For actual coding tasks or algorithmic questions, format the question specifically as a "Problem Statement" containing the exact inputs, expected outputs, and a perfectly clean, optimal JavaScript code solution.
2. For conceptual questions, completely ignore any incomplete or incorrect answers given by the candidate in the transcript. Write the technically perfect, easiest-to-understand explanation by yourself.
3. Keep explanations highly conversational, direct, and visual. For advanced concepts like Function Currying, explicitly explain the "Actual Need" (what real-world problem it solves, why static constants fail, and how it handles dynamic runtime configuration). 
4. For complex mechanics like Event Loop Latency, use a literal, step-by-step timeline example to demonstrate where the delay occurs.
5. Include explicit HTML structures and CSS properties when explaining layout selectors or design rules (like Combinators and Specificity math).
6. Organize sections cleanly using markdown headings, dividers, and bullet lists so it is optimized to be copied directly into an interview preparation notebook.

Here is the transcript: 
[PASTE YOUR TRANSCRIPT HERE]

```