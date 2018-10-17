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
