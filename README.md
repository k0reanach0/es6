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






