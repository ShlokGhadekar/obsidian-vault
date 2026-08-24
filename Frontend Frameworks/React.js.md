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
export default function Hello() { } // one default export per file export
const helper = () => {}; // named export
import Hello, { helper } from "./Hello"; // import both
```
### array methods(used for rendering lists)
```javascript
const nums = [1, 2, 3];
const doubled = nums.map(n=>n*2);
const evens = nums.filter(n=>n%2===0);
```


## Step 1 : Rendering elements
src/App.js
```jsx
function App() {
	const name = "ShlokG"; 
	const items = ["React", "Java", "DSA"];
	return(
		<div>
			<h1>Hello, Shlok</h1>
			<p>This is JSX - HTML looking syntax inside js.</p>
			<h1>Hello, {name}</h1>
			<ul>
			{items.map(item => <li key = {item}> {item} </li>)}
			</ul>
		</div>
	);
}
export default App;
```
- npm run dev to run
- jsx must return one root element(one div wrapping everything)
- `.map()` returning JSX is **the** pattern for rendering any list in React.
- The `key={item}` is required — React uses it to track which list item is which when things change.

## Step 2 : Components and Props
- just a function that returns jsx
- UI is split into components(to reuse and organise)
- Data flows in via props(they're like function arguments)

```jsx
function ItemCard({title, category}){
	return(
		<div style={{border: "1px solid gray", padding: "8px", margin: "4px"}}>
			<h3>{title}</h3>
			<p>{category}</p>
		</div>
	);
}
function App(){
	const items = [
		{id : 1, title: "React", category: "Frontend"},
		{id : 2, title: "DSA", category: "Core CS"},
	];
	return (
		<div>
			<h1>My prep list</h1>
			{items.map(item => (
				<ItemCard key={item.id} title={item.title} category={item.category}/>
			))}
		</div>
	);
}
export default App;
```
- a component can never modify its own props
- props are read only inside the child

## Step 3 : Internal Component State (useState)
- props are for data coming from outside
- state is data a component owns and can change itself(like a counter, a toggle, form input text)
src/Counter.jsx
```jsx
import { useState } from "react";

function Counter(){
	const [count, setCount] = useState(0); //[currentValue, setterFunction], starts at 0
	return(
		<div>
			<p>Count: {count}</p>
			<button onClick = {() => setCount(count+1)}>+1</button>
			<button onClick = {() => setCount(count-1)}>-1</button>
		</div>
	);
}
export default Counter;
```
src/App.jsx
```jsx
import Counter from "./Counter";

function App() {
  return (
    <div>
      <h1>My App</h1>
      <Counter/>
    </div>
  );
}
export default App;
```
- never mutate state directly(count=count+1), always call the setter(setCount())
- calling the setter is what tells react to "re-render this component with the updated value"
- direct mutation wont won't trigger a re-render, the screen just wont update(beginner's react bug)
- state is local to each instance of a component(two individual counters, each counter gets its own count)

## Step 4 : Handling Events
```jsx
function EventDemo(){
	const [text, setText] = useState("");
	const handleChange = (event) => {
		setText(event.target.value); //event.target is the actual input DOM element
	};
	const handleSubmit = () => {
		console.log("Submitted:", text);
	};
	return(
		<div>
			<input type="text" value={text} onChange={handleChange}/>
			<button onClick={handleSubmit}>Submit</button>
			<p>You typed : {text}</p>
		</div>
	);
}
```
- `value={text}` + `onChange={handleChange}` together makes this a **controlled input** — React state is the single source of truth for what's in the box, not the DOM.

## Step 5 : Form Validation
**combine state+events+conditional rendering
