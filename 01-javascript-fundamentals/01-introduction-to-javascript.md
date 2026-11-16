# Introduction to JavaScript

## What is JavaScript?

JavaScript is a **synchronous**, **single-threaded** programming language used to create interactive and responsive content on the web. Alongside HTML and CSS, it is one of the three core technologies of the World Wide Web.

## Synchronous

"Synchronous" means JavaScript executes code **line by line**. Each line must finish executing before the next line starts.

Think of a café. You go to the counter, place your order, and wait for your coffee. You cannot move from your place until you get your coffee. Only after you receive it do you move on.

```javascript
console.log("A");
console.log("B");
console.log("C");
// Output: A, B, C
```

## Single-Threaded

JavaScript has **one thread of execution**, which means it has one call stack and can do only one task at a time.

Think of the café again. There is only one barista. The barista takes one order and finishes it before taking the next. If the barista has to wait 5 minutes for the coffee machine, everyone in the queue waits too. This is what happens when JavaScript runs a slow task synchronously.

## What JavaScript Is Used For

JavaScript makes web pages interactive. Common examples include:

- Handling clicks and other user actions
- Changing text, images, and styles on a page
- Validating form inputs before submission
- Fetching data from a server without reloading the page

## Where JavaScript Runs

- **Browsers** such as Chrome, Firefox, Safari, and Edge
- **Node.js**, which runs JavaScript on servers and in other non-browser environments

JavaScript was created by **Brendan Eich** in 1995. Its standard is called **ECMAScript**.
