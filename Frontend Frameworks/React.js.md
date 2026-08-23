- frontend javascript library(created by meta)
- lets devs break down web pages into reusable pieces called components
- Virtual DOM(document object model): keeps a fast copy of webpage in memory and updates the only parts that change(saves time)
- JSX: A special syntax that lets you write HTML tags directly inside your JavaScript code.
- Declarative: You state what the screen should look like based on your data, and React handles the updates

## Basic Syntax

### declaration
```js
const name = "Shlok"; //let if it changes
let count = 0;
```
### arrow functions
```javascript
const add = (a,b) => a+b;
const greet = () => console.log("hi");
```
### string interpolation
```javascript
	const msg = console.log(`Hello, ${name}!`);
```
### Destructuring : pulling values out of objects/arrays
```javascript
const user = {id: 1, name: "shlok"};
const { id, name: userName } = user;

const arr = [1, 2, 3];
const [first, second] = arr
```
this just means storing id=1 and userName = shlok(renamed to avoid name conflicts)
and first = 1, second = 2
### Spread/copy/merge without mutating the original
```javascript
const updatedUser = {...user, name = "Shlok G"};
const morenumbers = [...arr, 4, 5];
```
### import/export (how files share code)
```javascript

```