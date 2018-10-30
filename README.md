# ES6

We use `for` loops to iterate over every item in an array. Usually, we do something to each item along the way.

One simple example would be capitalizing every string in an array.

```js
const strings = ['arielle', 'are', 'you', 'there'];
const capitalizedStrings = [];

for (let i = 0; i < strings.length; i += 1) {
    const string = strings[i];
    capitalizedStrings.push(string.toUpperCase());
}

console.log(capitalizedStrings);
```

In this snippet, we start a list of lowercase names. Then, we initialize an empty array, in which we'll store our capitalized strings.

Inside of the `for` loop, we simply grab the next string on every iteration; capitalize it; and push it into `capitalizedStrings`. At the end
of the loop, `capitalizedStrings` contains every word in `strings` except capitalized.

This brings us to our first function: `forEach`. This is a method on arrays that automatically loops through the list for us. Instead of the above where we manually index into `strings`, we can simply call
`forEach`, and receive the next string on every iteration. The updated version would look like:

```js
const strings = ['arielle', 'are', 'you', 'there'];
const capitalizedStrings = [];

strings.forEach(function (string) {
    capitalizedStrings.push(string.toUpperCase());
})
```

This also introduces a major pattern we'll see time and time again. Namely: we prefer to use methods on `Array.prototype` that abstract away details like initializing and incrementing counters.
That way we can focus on the important logic, rather than the boilerplate.



In the snippets below, we'll use encrypting and decrypting strings in our examples of `map`, `reduce`, and `filter`.

For example:

```js
// Thanks to EvanHahn for this: https://gist.github.com/EvanHahn/2587465
const caesar = require('./caesar');

// We can encrypt the string: 'this is my secret message' with `caesar`
// Here, we scramble the message by shifting each letter forward by 2 letters: 'a' becomes 'c'; 's' becomes  'u'; etc.
const encryptedString = caesar('this is my super secret message.', 2);
console.log(encryptedString); // 'vjku ku oa uwrgt ugetgv uvtkpi.'
```



Transforming every element in a list into a new one and collecting the results is called *mapping*. JavaScript has a built-in
function for just this use case, called, appropriately enough `map`.

We used `forEach` because it abstracts away the need to manage the iteration variable, `i`. We don't have to manually index into the array so we can focus on the logic that matters.

Similarly, we use `map` because it abstracts away initializing an empty array and pushing to it. Just like `forEach` accepts a callback that does something with each string value, `map` accepts
a callback that does something with each string value.

In this snippet, I'm using a function to "encrpyt" a list of messages. We _could_ use a `for` loop, and we _could_ use `forEach`.. but it's better with `map`

```js
const caesar = require('../caesar');

const key = 12;
const messages = [
    'arielle, are you there?',
    'the ghost has killed the shell',
    'the liziorati attack at dawn'
]

const encryptedMessages = messages.map(function (string) {
    return caesar(string, key);
})

console.log(encryptedMessages);
```



Another common pattern is using a `for` loop to process items in a list, but only pushing/preserving _some_ of them.

We usually decide which items to keep, and which to throw away, by doing an `if` check.

A snippet might look like this:

```js
// Secret message! This was a string encrypted with a key between 1 and 24.
const encryptedMessage = 'mduqxxq, mdq kag ftqdq?'];

// We can break this code by just decrypting with _every_ possible key.
const possibilities = tryAll(encryptedMessage);

// Most of these decryption attempts aren't readable. Sad.
// We can speed up finding the ones that are probably junk with an if check
const likelyPossibilities = [];
possibilities.forEach(function (decryptionAttempt) {
    // Keep the string if it looks like an English sentence
    if (isEnglish(decryptionAttempt)) {
        likelyPossibilities.push(decryptionAttempt);
    }
})
```

First, we try to decrypt the encrypted message with every possible key. That means we end up with an array of 24 possibilties.

In this loop, we test each one to see if it looks like an English string. If so, we keep it. If not, we throw it away.

This is clearly a common use case. So, there's a built-in for it, aptly named `filter`.

As with `map`, we pass `filter` a callback, which also gets a string. The difference is that `filter` will only save items in an array if the callback returns true.

```js
// Secret message! This was a string encrypted with a key between 1 and 24.
const encryptedMessage = 'mduqxxq, mdq kag ftqdq?'];

// We can break this code by just decrypting with _every_ possible key.
const possibilities = tryAll(encryptedMessage);

// Most of these decryption attempts aren't readable. Sad.
// We can speed up finding the ones that are probably junk with an if check
const likelyPossibilities = possibilities.filter(function (string) {
    // If isEnglish(string) returns true, filter saves in the output array
    return isEnglish(string);
})
```



So far, we've seen abstractions for three common use cases around arrays:

- `forEach`, which allows us to iterate over a list with `for`, but eliminates the need to manually manage the iteration variable `i`
- `map`, which lets us transform each element of a list, and collect the results in an array
- `filter`, which lets us choose which elements of a list to keep

Another common use case is: Iterating over a list to _collect_ its elements into a single results.

The prototypical example is adding up a bunch of numbers.

```js
// prices of: (big) box of oreos, girl scout cookies, fidget spinner, gold-plated Arch linux magnet
const prices = [12, 19, 7, 209];

// variable to store our total prices in
let totalPrice = 0;
for (let i = 0; i < prices.length; i += 1) {
    totalPrice += prices[i];
}

// Report our total prices: $247
console.log(`Your total is ${totalPrice}.`);
```

Refactoring the above loop with `reduce` looks like:

```js
const prices = [12, 19, 7, 209];

prices.reduce(function (totalPrice, nextPrice) {
    // totalPrice is the price so far
    console.log(`Total price so far: ${totalPrice}`)
    console.log(`Next price to add: ${nextPrice}`)

    // update totalPrice by adding the next price
    totalPrice += nextPrice

    // return the total price, and start over again
    return totalPrice
}, 0)
// ^ the second argument to `reduce` is the totalPrice to start with on the first iteration
```

Like `map` and `filter`, `reduce` accepts a callback, which it runs on each element of the array.

Unlike `map` and `filter`, the callback we pass to `reduce` accepts _two_ arguments: a `totalPrice`, and a `nextPrice`.

`totalPrice` is like `total` in the first example: It's the price we've gotten by adding up all the prices we've seen so far.

`nextprice` is the number we got by doing `prices[i]` in the first example. Recall that `map` and `reduce` automatically index into the array for us,
and pass this value to their callbacks automatically. `reduce` does the same thing, but passes that value as the second argument to its callback.

Just like with `map` and `reduce`, on each iteration, we have to return the value we're interested in getting back at the end of the loop. Namely, `totalPrice`.

Finally, note that we pass another argument after the callback to `reduce`. In this case, we pass `0`. This is analogous to the line in the first example where we set `const total = 0`.
That second argument is the initial value of `totalPrice`.

`reduce` is harder to grok than `map` or `filter`, so let's take a look at another example.

In the previous example, we used  `reduce` to collect an array of numbers into a sum. But `reduce` is versatile. We can use it to turn a list into a any single result. For example, we can use it to build up a string.

```js
const courses = ['Introduction to Programming', 'Algorithms & Data Structures', 'Discrete Math'];

const curriculum = courses.reduce(function (courseList, course) {
    // add the name to the class, with a preceding newline and tab for indentation
    return courseList += `\n\t${course}`;
}, 'The Computer Science curriculum consists of:');

console.log(curriculum);
```
which generates the output of:
```js
The Computer Science curriculum consists of:
    Introduction to Programming
    Algorithms & Data Structures
    Discrete Math
```

`reduce` is versatile: We can use it to turn a list into any kind of "single" result. Even if that single result is another array.

```js
const names = ['arielle', 'jung', 'scheherazade'];

const titleCase = function (name) {
    // uppercase first letter of name
    const first = name[0];
    const capitalizedFirst = first.toUpperCase();

    // get rest of name
    const rest = name.slice(1);

    // create list with all letters of the name, including capitalized first letter
    const letters = [capitalizedFirst].concat(rest);

    // join letters, return result
    return letters.join('');
}

const titleCased = names.reduce(function (titleCasedNames, name) {
    // title-case the next name
    const titleCasedName = titleCase(name);

    // add the title-cased name to a list
    titleCasedNames.push(titleCasedName);

    // return list of capitalizedNames
    return titleCasedNames
}, [])
// ^ start with an empty list

console.log(titleCased);
// ["Arielle", "Jung", "Scheherazade"]
```

So... This is neat. We used `reduce` to turn a list of lower-cased names into a list of title-cased names.

```js
const map = (list, mapFunction) => {
    const output = list.reduce((transformedList, nextElement) => {
        // use the mapFunction to transform the nextElement in the list 
        const transformedElement = mapFunction(nextElement);

        // add transformedElement to our list of transformed elements
        transformedList.push(transformedElement);

        // return list of transformed elements
        return transformedList;
    }, [])
    // ^ start with an empty list

    return output;
}
```

We can get the same results as above with:
```js
// congratulations...you just implemented `map` with `reduce`. badass.
const titleCased = map(names, titleCase);

console.log(titleCased);
// ["Arielle", "Jung", "Scheherazade"]
```

## Raising the level of abstraction with these functions brings immediate benefits to readability, and maintainability, by corollary.

```js
const nodeList = document.querySelectorAll('ul li');

// Method 1: Convert to true array
Array.from(nodeList);

// Method 2: Convert to true array
[...nodeList]

// Now you can use map and other methods to loop!
```

Arrow functions are terrific but not suitable in all situations. Avoid them in objects because `this` is always scoped to the parent -- which is the `window` in this case.

```js
const burger = {
  type: 🍔,
  eat: () => { // Should use function() instead
    console.log(this); // `this` is Window, not burger
  }
}

// Avoid Arrow Functions in Objects
```

```js
// ES6's shorthand for defining methods in objects

const Rain = {
  ❌ bringUmbrella.function() { }

  ✅ bringUmbrella() {
    return ☔
  }
}
```

Easier way to create objects with dynamic keys. Previously, we always had to use bracket notation to use a dynamic key. With ES6, we can finally create dynamic variable key in the object declaration.
```js
var key = "🔑";

// ❌ Old way
var lock = {}
lock[key] = 'unlock'

// ✅ New way
var lock = {
  [key]: 'unlock'
}
```

ES6 Destructuring to the rescue. Easily swap two variables using ES6 destructuring.
```js
// Swapping Variables with Destructuring

let oscar = 'La La Land';
let nominee = 'Moonlight';

[oscar, nominee] = [nominee, oscar];

oscar // 'Moonlight'
nominee // 'La La Land'
```

```js
// Trick to adding String and Number

const string = '100'
const number = 5

// This doesn't return the sum, it's concatenated
string + number // 1005

// Prepend string with a "+" to calculate the sum
+string + number // 105
```

Remove Array Duplicates using ES6 `Set`
```js
// Remove Array Duplicates using ES6 Set

const duplicates = [1, 2, 3, 4, 4, 1]

const uniques = Array.from(new Set(duplicates))

uniques // [1, 2, 3, 4]
```

Set Default Parameters with ES6. The old way was to use a ternary operator to assign the default value _if_ it was undefined. With ES6, you can set the default value right inside the function parameters
```js
// Setting Default Parameters

// Old way
function coffeeOrTea(drink) {
  drink = drink !== undefined ? drink : 🍵
}

// ES6 way
function coffeeOrTea(drink = 🍵) {

}
```

```js
// Other options for returning 1 or the other
function coffeeOrTea(coffee,tea) { 
  this.coffee = coffee;
  this.tea = tea;
}

coffeeOrTea.prototype.all= function() { 
  return this.coffee+" or "+this.tea
}

var mySelect = new coffeeOrTea("mocha","chai tea");
console.log(mySelect.all());
```

Split string using ES6 Spread. Convert a string to an array of characters using the `spread` syntax
```js
// Split string using ES6 spread

const pizza = 'pizza'

// Old way
const slicedPizza = pizza.split('')
// ['p', 'i', 'z', 'z', 'a']

// ES6 way
const slicedPizza = [...pizza]
// ['p', 'i', 'z', 'z', 'a']
```

Skip values in destructuring. You can use blanks to skip over unwanted values. This way you can use avoid useless variable assignments for values you don't want during destructuring
```js
// Skip values in destructuring

// Useless variable assignment
let [a, b, c] = ['ignore', 'ignore', 'keep']

// Use blanks to skip over unwanted values
let [ , , c] = ['ignore', 'ignore', 'keep']
c // 'keep'
```

Combine multiple arrays using ES6 spread. Instead of using concat() to concatenate arrays, try using the spread syntax to combine multiple arrays into one flattened array.
```js
// Combine multiple arrays used spread

let veggie = ['🍅', '🥑']
let meat = ['🍖']

// Old way
let sandwich = veggie.concat(meat, '🍞')

// ES6 way
let sandwich = [...veggie, ...meat, '🍞']

// Result
['🍅', '🥑', '🍖', '🍞']
```

```js
// 4 ways to combine strings
const mom = '🌷'

// ES6 Template Strings
`Happy Mother's Day ${mom}`

// Join method
['Happy ', 'Mother\'s ', 'Day ', mom].join('')

// Concat method
"".concat('Happy ', 'Mother\'s ', 'Day ', mom)

// + Operator
'Happy Mother\'s Day' + mom
```

```js
// Default Parameter
// If Prince Harry asked me to marry him...
function marryMe(yes = 'I will') {
  console.log(yes)
}

// Keep it traditional with Default Parameter
marryMe() // 'I will'

// My response
marryMe('HECK YEAH 💋')
```

```js
// Print ranges natively in JavaScript
Number.prototype[Symbol.iterator] = function*() {
  for (let i = 0; i <= this; i++) {
    yield i;
  }
}

[...3] // [0, 1, 2, 3]
```

Named parameters using Destructured Objects. JavaScript does not natively support named parameters. This is a cool way around the issue. No more worrying about the specific order of inserting your arguments.
```js
// Named parameters using Destructured objects

// Arguments must be in specific order
function meal(protein, carb, veggie) {
  meal('🍖', '🍚', '🥦')
}

// Arguments can be in any order
function meal({protein, carb, veggie}) {
  meal({carb: '🍚', veggie: '🥦', protein: '🍖'})
}
```

```js
// JS: 3 ways to convert to boolean
const isChamp = 'Golden State Warriors';

new Boolean(isChamp) // true

Boolean(isChamp) // true

!!isChamp // true
```

Array destructuring is terrific at extracting value from your arguments. The first thing we're doing is spreading our arguments, so you can get an array. Next we're assigning them to our variables using array destructuring. So the next time you see the array bracket notation. Swap them out and use the array destructuring syntax instead
```js
// Happy Father's Day
function sendLove(...args) {
  // Old way
  const hug = args[0];
  const gift = args[1];

  // Better way using Array Destructure
  const [hug, gift] = args;
}

sendLove('🤗', '🎁')
```

```js
const canada = '🍁'

// Method 1: Repeat
canada.repeat(3)

// Method 2: Fill
Array(3).fill(canada).join('')

// result
'🍁🍁🍁'
```

You can skip the else block if your `if` block always executes a return statement. I find this way makes your code a lot easier to read.
```js
// No else return
function hello(name) {
  if (name) {
    return '👏'
  } else { // You can skip the else block
    return '👻'
  }

  // much easier to read
  return '👻'
}
```

When you use destructuring, make sure to set a default value of empty `{}` to prevent it from throwing an error. Why? Because you can't destructure `undefined` and `null` values.
```js
const {name} = null;
const {name} = undefined;
name // TypeError

// Prevent error with default {} when destructuring
function hi(person) {
  const {age} = person
}
hi() // TypeError

function hi(person = {}) {
  const {age} = person
}
hi() // no errors
```

You can now use ES6 includes() method to quickly search for a substring inside a string. Note the comparison is case-sensitive.
```js
// Checking if string contains substring
const word = 'sunny'

// Old way
word.indexOf('sun') !== -1; // true

// ES6 way
word.includes('sun'); // true
```

How to format a number as currency in ES6. When formatting large numbers of numbers, its better to create a Number Format object and use the function provided by its NumberFormat.format property.
```js
// How to format currency in ES6

const money = 100;

// Old way
money.toLocaleString('en-US', {style: 'currency', currency: 'USD'}) // '$100.00'

// ES6 way
new Intl.NumberFormat('en-US', {style: 'currency', currency: 'USD'}).format(money); // '$100.00'
```

Objects are reference types so you can't just use `===` or `==` to compare 2 objects. One quick way to compare if 2 objects have the same key value is using `JSON.stringify`. Another way is using Lodash `isEqual` function
```js
const k1 = {fruit: '🥝'}
const k2 = {fruit: '🥝'}

// Using JavaScript
JSON.stringify(k1) === JSON.stringify(k2) // true

// Using lodash
_.isEqual(k1, k2) // true
```

For boolean values and naming boolean variables, you can simply prefix it with `is`, `has`, or `can`.
```js
// Better boolean variable names

// bad
const person = true
const age = true
const dance = true

// Prefix with: is, has, can
const isPerson = true
const hasAge = true
const canDance = true
```

```js
// ES6 way to clone an array
const sheeps = ['🐑', '🐑', '🐑']

// Old way
const cloneSheeps = sheeps.slice()

// ES6 way
const cloneSheeps = [...sheeps]
```

```js
// String.prototype.split()
const name = 'samantha'
name.split('') // ['s', 'a', 'm', 'a', 'n', 't', 'h', 'a']
```

```js
// Array.prototype.reverse()
const letters = ['a', 'b', 'c']
letters.reverse(); // ['c', 'b', 'a']
```

How to set dynamic property keys with ES6. Previously we had to do 2 steps - create the object literal and then use bracket notation. With ES6, you can directly use a variable as your property key in your object literal. Beside passing in variables, ES6 also supports commuted property names. This allows you to pass in an expression which will then be computed and used as the property name. example: `let pan {[cake + 1]: "yum"}`
```js
// Dynamic property name with ES6
let cake = '🍰'

// Old way requires 2 steps
let pan = {};
pan[cake] = '🥞'

// ES6
let pan = {
  [cake]: '🥞'
}
```

```js
// Array.prototype.join()
const pieces = ['s', 'a', 'm']
pieces.join(''); // 'sam'

const strings = ['Web', 'Basics', '!']

strings.join(); // defaults to comma (,)
// 'Web,Basics,!'

strings.join(' ') // empty space
// 'Web Basics !'

strings.join('-') // dash
// 'Web-Basics-!'

const nums = [2, 10, 5]
nums.join(' + ') // '2 + 10 + 5'
```

```js
// Reverse a string
function reverseString(str) => {
  return str
    .split('') // split the string into an array
    .reverse() // reverse the order of the array
    .join('') // convert the array back into a string
}

reverseString("hello")
// returns "olleh"
```

```js
// Destructuring

const turtle = {
  name: 'Bob',
  legs: 4,
  shell: true,
  type: 'amphibious',
  meal: 10,
  diet: 'berries'
}

// Bad code
// Notice repeating `animal` several times
function feed(animal) {
  return `Feed ${animal.name} ${animal.meal} kilos of ${animal.diet}`
}

// Good code
// Object destructuring
function feed({ name, meal, diet }) {
  return `Feed ${name} ${meal} kilos of ${diet}`
}

// OR
function feed(animal) {
  const { name, meal, diet } = animal
  return `Feed ${name} ${meal} kilos of ${diet}`
}

const horse = {
  name: 'Topher',
  size: 'large',
  skills: ['jousting', 'racing'],
  age: 7
}

const { name, size, skills } = horse
bio = `${name} is a ${size} skilled in ${skills.join(' & ')}

console.log(bio)
```

```js
// Advanced Tag Example
function horseArge(str, age) {
  const ageStr = age > 5 ? 'old' : 'young'
  return `${str[0]}${ageStr} at ${age} years`
}

const bio = horseAge`This horse is ${horse.age}`
```

```js
// Objects & Spread syntax
const pikachu = { name: 'Pikachu' }
const stats = { hp: 40, attack: 60, defense: 45 }

// Bad Object Code
pikachu['hp'] = stats.hp
pikachu['attack'] = stats.attack
pikachu['defense'] = stats.defense

// OR

const lvl0 = Object.assign(pikachu, stats)
const lvl1 = Object.assign(pikachu, { hp: 45 })

// Good Object Code
const lvl0 = { ...pikachu, ...stats }
const lvl1 = { ...pikachu, hp: 45 }

// Arrays
let pokemon = ['Arbok', 'Raichu', 'Sandshrew']

// Bad Array Code
pokemon.push('Bulbasaur')
pokemon.push('Metapod')
pokemon.push('Weedle')

// Good Array Code
pokemon = [...pokemon, 'Bulbasaur', 'Metapod', 'Weedle']

// Unshift
pokemon = ['Bulbasaur', 'Metapod', 'Weedle', ...pokemon]

pokemon = ['Bulbasaur', ...pokemon, 'Metapod', 'Weedle']
```

```js
const orders = [500, 30, 99, 15, 223]

// Bad Loop Code
const total = 0
const withTax = []
const highValue = []
for (i = 0; i < orders.length; i++) {
  // Reduce
  total += orders[i]

  //Map
  withTax.push(orders[i] * 1.1);

  // Filter
  if (orders[i] > 100) {
    highValue.push(orders[i])
  }
}
// Ugly and could be mutating values we may want to be immutable and unpredictable

// Good Loop Code in 3 lines of code
// Reduce
const total = orders.reduce((acc, cur) => acc + cur)

// Map
const withTax = orders.map(v => v * 1.1)

// Filter
const highValue = orders.filter(v => v > 100)
```

```js
const random = () => {
  return Promise.resolve(Math.random())
}

// Bad Promise Code
const sumRandomAsyncNums = () => {
  let first;
  let second;
  let third;
  
    return random()
    .then(v => {
      first = v;
      return random();
    })
    .then(v => {
      second = v;
      return random();
    })
    .then(v => {
      third = v;
      return first + second + third
    })
}

// Good Promise Code
const sumRandomAsyncNums = async() => {
  const first = await random();
  const second = await random();
  const third = await random();

  console.log(`Result ${first + second + third}`)
}

sumRandomAsyncNums()

// Conditional async/await
if (await random()) {

}

// Promise.all needs every promise to work. if one promise fails, the entire thing fails unexpectedly. beware
const randos = Promise.all([
  random(),
  random(),
  random()
])

for (const r of await) {

}
```

```js
function square() {
  let example = () => {
    let numbers = [];
    for (let number of arguments) {
      numbers.push(number * number);
    }

    return numbers;
  };

  return example();
}

square(2, 4, 7.5, 8, 11.5, 21); // returns: [4, 16, 56.25, 64, 132.25, 441]
```

### Destructuring

Destructuring allows binding using pattern matching, with support for matching arrays and objects. 
Destructuring is fail-soft, similar to standard object lookup `foo["bar"]`, producing undefined values when not found.
```js
// list matching
var [a, ,b] = [1,2,3];
a === 1;
b === 3;

// object matching
var { op: a, lhs: { op: b }, rhs: c } = getASTNode()

// object matching shorthand
// binds `op`, `lhs` and `rhs` in scope
var {op, lhs, rhs} = getASTNode()

// Can be used in parameter position
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// Fail-soft destructuring
var [a] = [];
a === undefined;

// Fail-soft destructuring with defaults
var [a = 1] = [];
a === 1;

// Destructuring + defaults arguments
function r({x, y, w = 10, h = 10}) {
  return x + y + w + h;
}
r({x:1, y:2}) === 23
```
When I was first learning to program, it helped me to think about functions as machines. These machines can do anything you want them to. They can even accept input and return a value. Each machine has a button on it that you can press when you want the machine to run, ().

```js
function add (x, y) {
  return x + y
}

add(2,3) // 5 - Press the button, run the machine.
```
Whether I press the button, you press the button, or someone else presses the button doesn’t matter. Whenever the button is pressed, like it or not, the machine is going to run.
```js
function add (x, y) {
  return x + y
}

const me = add
const you = add
const someoneElse = add

me(2,3) // 5 - Press the button, run the machine.
you(2,3) // 5 - Press the button, run the machine.
someoneElse(2,3) // 5 - Press the button, run the machine.
```
Now what if we take our add machine and pass it to another machine? Remember, it doesn’t matter who presses the () button, if it’s pressed, it’s going to run.
```js
function add (x, y) {
  return x + y
}

function addFive (x, addReference) {
  return addReference(x, 5) // 15 - Press the button, run the machine.
}

addFive(10, add) // 15
```
Your brain might have got a little weird on this one, nothing new is going on here though. Instead of “pressing the button” on add, we pass add as an argument to addFive, rename it addReference, and then we “press the button” or invoke it.

This highlights some important concepts of the JavaScript language. First, just as you can pass a string or a number as an argument to a function, so too can you pass a reference to a function as an argument. When you do this the function you’re passing as an argument is called a callback function and the function you’re passing the callback function to is called a higher order function.

Because vocabulary is important, here’s the same code with the variables re-named to match the concepts they’re demonstrating.
```js
function add (x,y) {
  return x + y
}

function higherOrderFunction (x, callback) {
  return callback(x, 5)
}

higherOrderFunction(10, add)
```
```js
// updateUI, showError, and getLocationURL are irrelevant.
// Pretend they do what they sound like.

const id = 'tylermcginnis'

$("#btn").on("click", () => {
  $.getJSON({
    url: `https://api.github.com/users/${id}`,
    success: (user) => {
      $.getJSON({
        url: getLocationURL(user.location.split(',')),
        success (weather) {
          updateUI({
            user,
            weather: weather.query.results
          })
        },
        error: showError,
      })
    },
    error: showError
  })
})
```
Notice we’ve added a few more layers of callbacks. First we’re saying don’t run the initial AJAX request until the element with an id of btn is clicked. Once the button is clicked, we make the first request. If that request succeeds, we make a second request. If that request succeeds, we invoke the updateUI method passing it the data we got from both requests. Regardless of if you understood the code at first glance or not, objectively it’s much harder to read than the code before. This brings us to the topic of “Callback Hell”.

As humans, we naturally think sequentially. When you have nested callbacks inside of nested callbacks, it forces you out of your natural way of thinking. Bugs happen when there’s a disconnect between how your software is read and how you naturally think.

Like most solutions to software problems, a commonly prescribed approach for making “Callback Hell” easier to consume is to modularize your code.

```js
function getUser(id, onSuccess, onFailure) {
  $.getJSON({
    url: `https://api.github.com/users/${id}`,
    success: onSuccess,
    error: onFailure
  })
}

function getWeather(user, onSuccess, onFailure) {
  $.getJSON({
    url: getLocationURL(user.location.split(',')),
    success: onSuccess,
    error: onFailure,
  })
}

$("#btn").on("click", () => {
  getUser("tylermcginnis", (user) => {
    getWeather(user, (weather) => {
      updateUI({
        user,
        weather: weather.query.results
      })
    }, showError)
  }, showError)
})
```
OK, the function names help us understand what’s going on, but is it objectively “better”? Not by much. We’ve put a band-aid over the readability issue of Callback Hell. The problem still exists that we naturally think sequentially and, even with the extra functions, nested callbacks break us out of that sequential way of thinking.

The next issue of callbacks has to do with inversion of control. When you write a callback, you’re assuming that the program you’re giving the callback to is responsible and will call it when (and only when) it’s supposed to. You’re essentially inverting the control of your program over to another program. When you’re dealing with libraries like jQuery, lodash, or even vanilla JavaScript, it’s safe to assume that the callback function will be invoked at the correct time with the correct arguments. However, for many third party libraries, callback functions are the interface for how you interact with them. It’s entirely plausible that a third party library could, whether on purpose or accidentally, break how they interact with your callback.

```js
function criticalFunction () {
  // It's critical that this function
  // gets called and with the correct
  // arguments.
}

thirdPartyLib(criticalFunction)
```
Since you’re not the one calling criticalFunction, you have 0 control over when and with what argument it’s invoked. Most of the time this isn’t an issue, but when it is, it’s a big one.

### Promises

Have you ever been to a busy restaurant without a reservation? When this happens, the restaurant needs a way to get back in contact with you when a table opens up. Historically, they’d just take your name and yell it when your table was ready. Then, as naturally occurs, they decided to start getting fancy. One solution was, instead of taking your name, they’d take your number and text you once a table opened up. This allowed you to be out of yelling range but more importantly, it allowed them to target your phone with ads whenever they wanted. Sound familiar? It should! OK, maybe it shouldn’t. It’s a metaphor for callbacks! Giving your number to a restaurant is just like giving a callback function to a third party service. You expect the restaurant to text you when a table opens up, just like you expect the third party service to invoke your function when and how they said they would. Once your number or callback function is in their hands though, you’ve lost all control.

Thankfully, there is another solution that exists. One that, by design, allows you to keep all the control. You’ve probably even experienced it before - it’s that little buzzer thing they give you. You know, this one.

If you’ve never used one before, the idea is simple. Instead of taking your name or number, they give you this device. When the device starts buzzing and glowing, your table is ready. You can still do whatever you’d like as you’re waiting for your table to open up, but now you don’t have to give up anything. In fact, it’s the exact opposite. They have to give you something. There is no inversion of control.

The buzzer will always be in one of three different states - pending, fulfilled, or rejected.

pending is the default, initial state. When they give you the buzzer, it’s in this state.

fulfilled is the state the buzzer is in when it’s flashing and your table is ready.

rejected is the state the buzzer is in when something goes wrong. Maybe the restaurant is about to close or they forgot someone rented out the restaurant for the night.

Again, the important thing to remember is that you, the receiver of the buzzer, have all the control. If the buzzer gets put into fulfilled, you can go to your table. If it gets put into fulfilled and you want to ignore it, cool, you can do that too. If it gets put into rejected, that sucks but you can go somewhere else to eat. If nothing ever happens and it stays in pending, you never get to eat but you’re not actually out anything.

Now that you’re a master of the restaurant buzzer thingy, let’s apply that knowledge to something that matters.

If giving the restaurant your number is like giving them a callback function, receiving the little buzzy thing is like receiving what’s called a “Promise”.

As always, let’s start with why. Why do Promises exist? They exist to make the complexity of making asynchronous requests more manageable. Exactly like the buzzer, a Promise can be in one of three states, pending, fulfilled or rejected. Unlike the buzzer, instead of these states representing the status of a table at a restaurant, they represent the status of an asynchronous request.

If the async request is still ongoing, the Promise will have a status of pending. If the async request was successfully completed, the Promise will change to a status of fulfilled. If the async request failed, the Promise will change to a status of rejected. The buzzer metaphor is pretty spot on, right?

```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve() // Change status to 'fulfilled'
  }, 2000)
})
```

```js
function onSuccess () {
  console.log('Success!')
}

function onError () {
  console.log('💩')
}

const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve()
  }, 2000)
})

promise.then(onSuccess)
promise.catch(onError)
```

Now let’s pretend something bad happened and we wanted to change the status of the promise to rejected. Instead of calling resolve, we would call reject.
```js
function onSuccess () {
  console.log('Success!')
}

function onError () {
  console.log('💩')
}

const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject()
  }, 2000)
})

promise.then(onSuccess)
promise.catch(onError)
```

Remember the last async callback example we saw earlier?
```js
function getUser(id, onSuccess, onFailure) {
  $.getJSON({
    url: `https://api.github.com/users/${id}`,
    success: onSuccess,
    error: onFailure
  })
}

function getWeather(user, onSuccess, onFailure) {
  $.getJSON({
    url: getLocationURL(user.location.split(',')),
    success: onSuccess,
    error: onFailure,
  })
}

$("#btn").on("click", () => {
  getUser("tylermcginnis", (user) => {
    getWeather(user, (weather) => {
      updateUI({
        user,
        weather: weather.query.results
      })
    }, showError)
  }, showError)
})
```

Is there anyway we could use the Promise API here instead of using callbacks? What if we wrap our AJAX requests inside of a promise? Then we can simply resolve or reject depending on how the request goes. Let’s start with getUser.

```js
function getUser(id) {
  return new Promise((resolve, reject) => {
    $.getJSON({
      url: `https://api.github.com/users/${id}`,
      success: resolve,
      error: reject
    })
  })
}
```

Nice. Notice that the parameters of getUser have changed. Instead of receiving id, onSuccess, and onFailure, it just receives id. There’s no more need for those other two callback functions because we’re no longer inverting control. Instead, we use the Promise’s resolve and reject functions. resolve will be invoked if the request was successful, reject will be invoked if there was an error.

```js
function getWeather(user) {
  return new Promise((resolve, reject) => {
    $.getJSON({
      url: getLocationURL(user.location.split(',')),
      success: resolve,
      error: reject,
    })
  })
}
```
```js
$("#btn").on("click", () => {
  const userPromise = getUser('tylermcginnis')

  userPromise.then((user) => {

  })

  userPromise.catch(showError)
})
```
```js
$("#btn").on("click", () => {
  const userPromise = getUser('tylermcginnis')

  userPromise.then((user) => {
    const weatherPromise = getWeather(user)
    weatherPromise.then((weather) => {

    })

    weatherPromise.catch(showError)
  })

  userPromise.catch(showError)
})
```
```js
$("#btn").on("click", () => {
  const userPromise = getUser('tylermcginnis')

  userPromise.then((user) => {
    const weatherPromise = getWeather(user)
    weatherPromise.then((weather) => {
      updateUI({
        user,
        weather: weather.query.results
      })
    })

    weatherPromise.catch(showError)
  })

  userPromise.catch(showError)
})
```

Our new code is better, but there are still some improvement we can make. Before we can make those improvements though, there are two more features of promises you need to be aware of, chaining and passing arguments from resolve to then.

#### Chaining

Both .then and .catch will return a new promise. That seems like a small detail but it’s important because it means that promises can be chained.

In the example below, we call getPromise which returns us a promise that will resolve in at least 2000 milliseconds. From there, because .then will return a promise, we can continue to chain our .thens together until we throw a new Error which is caught by the .catch method.

```js
function getPromise () {
  return new Promise((resolve) => {
    setTimeout(resolve, 2000)
  })
}

function logA () {
  console.log('A')
}

function logB () {
  console.log('B')
}

function logCAndThrow () {
  console.log('C')

  throw new Error()
}

function catchError () {
  console.log('Error!')
}

getPromise()
  .then(logA) // A
  .then(logB) // B
  .then(logCAndThrow) // C
  .catch(catchError) // Error!
```

Just so you can see one more example, here’s a common use case when you use the fetch API. fetch will return you a promise that will resolve with the HTTP response. To get the actual JSON, you’ll need to call .json. Because of chaining, we can think about this in a sequential manner.

```js
fetch('/api/user.json')
  .then((response) => response.json())
  .then((user) => {
    // user is now ready to go.
  })
```

```js
function getUser(id) {
  return new Promise((resolve, reject) => {
    $.getJSON({
      url: `https://api.github.com/users/${id}`,
      success: resolve,
      error: reject
    })
  })
}

function getWeather(user) {
  return new Promise((resolve, reject) => {
    $.getJSON({
      url: getLocationURL(user.location.split(',')),
      success: resolve,
      error: reject,
    })
  })
}

$("#btn").on("click", () => {
  getUser("tylermcginnis")
    .then(getWeather)
    .then((weather) => {
      // We need both the user and the weather here.
      // Right now we just have the weather
      updateUI() // ????
    })
    .catch(showError)
})
```

It looks much better, but now we’re running into an issue. Can you spot it? In the second .then we want to to call updateUI. The problem is we need to pass updateUI both the user and the weather. Currently how we have it set up, we’re only receiving the weather, not the user. Somehow we need to figure out a way to make it so the promise that getWeather returns is resolved with both the user and the weather.

Here’s the key. resolve is just a function. Any arguments you pass to it will be passed along to the function given to .then. What that means is that inside of getWeather, if we invoke resolve ourself, we can pass to it weather and user. Then, the second .then method in our chain will receive both user and weather as an argument.

```js
function getWeather(user) {
  return new Promise((resolve, reject) => {
    $.getJSON({
      url: getLocationURL(user.location.split(',')),
      success(weather) {
        resolve({ user, weather: weather.query.results })
      },
      error: reject,
    })
  })
}

$("#btn").on("click", () => {
  getUser("tylermcginnis")
    .then(getWeather)
    .then((data) => {
      // Now, data is an object with a
      // "weather" property and a "user" property.

      updateUI(data)
    })
    .catch(showError)
})
```
```js
// Callbacks 🚫
getUser("tylermcginnis", (user) => {
  getWeather(user, (weather) => {
    updateUI({
      user,
      weather: weather.query.results
    })
  }, showError)
}, showError)


// Promises ✅
getUser("tylermcginnis")
  .then(getWeather)
  .then((data) => updateUI(data))
  .catch(showError);
```

Now it’s clear that promises drastically increase the readability of our asynchronous code, but is there a way we can make it even better? Assume that you were on the TC39 committee and you had all the power to add new features to the JavaScript language. What steps, if any, would you take to improve this code?
```js
$("#btn").on("click", () => {
  getUser("tylermcginnis")
    .then(getWeather)
    .then((data) => updateUI(data))
    .catch(showError)
})
```

As we’ve discussed, the code reads pretty nicely. Just as our brains work, it’s in a sequential order. One issue that we did run into was that we needed to thread the data (users) from the first async request all the way through to the last .then. This wasn’t a big deal, but it made us change up our getWeather function to also pass along users. What if we just wrote our asynchronous code the same way which we write our synchronous code? If we did, that problem would go away entirely and it would still read sequentially. Here’s an idea.
```js
$("#btn").on("click", () => {
  const user = getUser('tylermcginnis')
  const weather = getWeather(user)

  updateUI({
    user,
    weather,
  })
})
```

Well, that would be nice. Our asynchronous code looks exactly like our synchronous code. There’s no extra steps our brain needs to take because we’re already very familiar with this way of thinking. Sadly, this obviously won’t work. As you know, if we were to run the code above, user and weather would both just be promises since that’s what getUser and getWeather return. But remember, we’re on TC39. We have all the power to add any feature to the language we want. As is, this code would be really tricky to make work. We’d have to somehow teach the JavaScript engine to know the difference between asynchronous function invocations and regular, synchronous function invocations on the fly. Let’s add a few keywords to our code to make it easier on the engine.

First, let’s add a keyword to the main function itself. This could clue the engine to the fact that inside of this function, we’re going to have some asynchronous function invocations. Let’s use async for this.
```js
$("#btn").on("click", async () => {
  const user = getUser('tylermcginnis')
  const weather = getWeather(user)

  updateUI({
    user,
    weather,
  })
})
```

Cool. That seems reasonable. Next let’s add another keyword to let the engine know exactly when a function being invoked is asynchronous and is going to return a promise. Let’s use await. As in, “Hey engine. This function is asynchronous and returns a promise. Instead of continuing on like you typically do, go ahead and ‘await’ the eventual value of the promise and return it before continuing”. With both of our new async and await keywords in play, our new code will look like this.

```js
$("#btn").on("click", async () => {
  const user = await getUser('tylermcginnis')
  const weather = await getWeather(user.location)

  updateUI({
    user,
    weather,
  })
})
```

```js
// same
async function getPromise(){}

const promise = getPromise()
```
```js
async function add (x, y) {
  return x + y
}

add(2,3).then((result) => {
  console.log(result) // 5
})
```

await without async is bad
If you try to use the await keyword inside of a function that isn’t async, you’ll get an error.
```js
$("#btn").on("click", () => {
  const user = await getUser('tylermcginnis') // SyntaxError: await is a reserved word
  const weather = await getWeather(user.location) // SyntaxError: await is a reserved word

  updateUI({
    user,
    weather,
  })
})
```

Here’s how I think about it. When you add async to a function it does two things. It makes it so the function itself returns (or wraps what gets returned in) a promise and makes it so you can use await inside of it.

You may have noticed we cheated a little bit. In our original code we had a way to catch any errors using .catch. When we switched to Async/Await, we removed that code. With Async/Await, the most common approach is to wrap your code in a try/catch block to be able to catch the error.

```js
$("#btn").on("click", async () => {
  try {
    const user = await getUser('tylermcginnis')
    const weather = await getWeather(user.location)

    updateUI({
      user,
      weather,
    })
  } catch (e) {
    showError(e)
  }
})
```





























