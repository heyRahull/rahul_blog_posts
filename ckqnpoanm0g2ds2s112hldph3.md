---
title: "Hoisting in Js"
datePublished: 2021-07-03T11:57:48.567Z
cuid: ckqnpoanm0g2ds2s112hldph3
slug: hoisting-in-js
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1625313431065/EHVejHptW.png

---

If you are sitting for a js interview, there will be a high priority that this topic will be in the interviewer's list. So lets see what is hoisting in JavaScript?

"Hoisting in Javascript is the default behaviour of moving function and variables declarations to the top."

There might be a possibility that you might not get in one go by looking at the definition, that what is hoisting so let's look at it in a little detail.


![undraw_Code_review_re_woeb.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625313355224/L8qoObauk.png)

**Before moving further you must be familiar with the concept of Execution context and call stack !**

We know that in the execution context, the variables are scanned and made undefined in the creation phase, Let's visualize this.

```
console.log(a);
var a = 7;

//output - undefined(in creation phase)

``` 
In the creation phase the value of x is undefined, now when it comes to the second line of code and sees the value of x it gives the value back to x in the execution phase.

```
console.log(a);
var a = 7;

//output - 7(in execution phase)

``` 

Hoisting can be categorised into :- 

1. Function Hoisting 

2. Variable Hoisting.  

 

**Variable Hoisting** 

In JavaScript, a variable can be declared after it has been used. 

In other words, a variable can be used before it has been declared. 

```
a=5 

console.log(a) 

var a 
```
 

**Function Hoisting** 

In Js, a function definition is hoisted at the top of the current scope, no matter wherever you define your function at any place in your code.That is why if we call the function before defining it, it will still give the output.  

```
hoisted(); 

 hoisted( ){ 

 console.log('This function has been hoisted.');  

}; 
```

**Note : Function expressions and fat arrow functions in JavaScript are not hoisted. 
**

Ex:-(function expression)  

const myFunction = function [name](param1, param2, ...) {  [statements]} 

 

(fat arrow function)  

const myFunction = (param1, param2, ...) => {  [statements]} 

 

**Note : variables and functions declared with let and const are not hoisted. ** 
 
![undraw_Code_thinking_re_gka2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1625313183989/k1xyGvSek.png)

Hoisting is (to many developers) an unknown or overlooked behavior of JavaScript.

If a developer doesn't understand hoisting, programs may contain bugs (errors).

To avoid bugs, always declare all variables at the beginning of every scope.

Since this is how JavaScript interprets the code, it is always a good rule.