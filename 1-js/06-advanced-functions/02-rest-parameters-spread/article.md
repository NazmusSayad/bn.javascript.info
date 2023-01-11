# রেস্ট প্যারামিটার এবং স্প্রেড সিনট্যাক্স

# অনেক জাভাস্ক্রিপ্ট অন্তর্নির্মিত ফাংশন ইচ্ছামত সংখ্যাতে আর্গুমেন্ট নিতে পারে।

এই ক্ষেত্রেঃ

- `Math.max(arg1, arg2, ..., argN)` -- আর্গুমেন্টগুলির সবচেয়ে বড়টি ফেরত দেই।
- `Object.assign(dest, src1, ..., srcN)` -- `dest` এর মধ্যে `src1..N` থেকে প্রপার্টি গুলো অনুলিপি করে.
- ...ইত্যাদি.

এই অধ্যায়ে আমরা শিখব কিভাবে একই জিনিসটা করতে হয়। এবং কিভাবে অ্যারেকে প্যারামিটার হিসেবে এমন ফাংশনে ব্যবহার করতে হয়।
In this chapter we'll learn how to do the same. And also, how to pass arrays to such functions as parameters.

## রেস্ট প্যারামিটার `...`

একটি ফাংশনকে যেকোনো সংখ্যাতে আর্গুমেন্ট দিয়ে কল করা যায়, কিভাবে সংজ্ঞায়িত করা হয় কোন ব্যাপার না।

যেমন এখানে:

```js run
function sum(a, b) {
  return a + b
}

alert(sum(1, 2, 3, 4, 5))
```

এখানে অতিরিক্ত আর্গুমেন্টস এর জন্য কোন ইরর হবে না। তবে অবশ্যই ফলাফলে প্রথম ২ টা গননা করা হবে।

বাকি প্যারামিটারগুলিকে তিনটি বিন্দু `...` ব্যবহার করে যে অ্যারেতে তাদের রাখা হবে সেই নামে দিয়ে ফাংশনে অন্তর্ভুক্ত করা যেতে পারে। তিনটি বিন্দু মানে "একটি অ্যারের মধ্যে অবশিষ্ট প্যারামিটার সংগ্রহ করা"।

এই ক্ষেত্রে, সব আর্গুমেন্ট গুলকে একটা অ্যারে `args` তে সংগ্রহ করতে :

```js run
function sumAll(...args) {
  // args হল অ্যারেটার নাম
  let sum = 0

  for (let arg of args) sum += arg

  return sum
}

alert(sumAll(1)) // 1
alert(sumAll(1, 2)) // 3
alert(sumAll(1, 2, 3)) // 6
```

আমরা চাইলে প্রথম প্যারামিটারকে ভ্যারিয়েবল হিসেবে নিতে পারি, এবং অবশিষ্টগুলোকে সংগ্রহ করতে পারি।

এখানে প্রথম ২ টা আর্গুমেন্ট ভ্যারিয়েবল গুলোতে এবং অবশিষ্টগুলো `titles` অ্যারেতেঃ

```js run
function showName(firstName, lastName, ...titles) {
  alert(firstName + ' ' + lastName) // Julius Caesar

  // অবশিষ্টগুলো `titles` অ্যারেতে
  // i.e. titles = ["Consul", "Imperator"]
  alert(titles[0]) // Consul
  alert(titles[1]) // Imperator
  alert(titles.length) // 2
}

showName('Julius', 'Caesar', 'Consul', 'Imperator')
```

````warn header="রেস্ট প্যারামিটার অবশ্যই সবচেয়ে শেষে হবে"
রেস্ট প্যারামিটার অবশিষ্ট সব আর্গুমেন্ট সংগ্রহ করে। সুতরাং এইটার কোন মানেই হয়না এবং এটা একটা ইরর ঘটায়ঃ

```js
function f(arg1, ...rest, arg2) { // arg2 after ...rest ?!
  // error
}
```

`...rest` অবশ্যই সবচেয়ে শেষে হবে।
````

## "arguments" ভ্যারিয়েবল।

`arguments` নামে আরও একটা বিশেষ অ্যারের মত অবজেক্ট আছে যেটা সবকয়টি আর্গুমেন্টকে তাদের ইনডেক্স ের অনুসারে রাখে।

এই ক্ষেত্রে:

```js run
function showName() {
  alert(arguments.length)
  alert(arguments[0])
  alert(arguments[1])

  // এটা পুনরাবৃত্তিযোগ্য
  // for(let arg of arguments) alert(arg);
}

// shows: 2, Julius, Caesar
showName('Julius', 'Caesar')

// shows: 1, Ilya, undefined (no second argument)
showName('Ilya')
```

In old times, rest parameters did not exist in the language, and using `arguments` was the only way to get all arguments of the function. And it still works, we can find it in the old code.

But the downside is that although `arguments` is both array-like and iterable, it's not an array. It does not support array methods, so we can't call `arguments.map(...)` for example.

Also, it always contains all arguments. We can't capture them partially, like we did with rest parameters.

So when we need these features, then rest parameters are preferred.

````smart header="Arrow functions do not have `\"arguments\"`"
If we access the `arguments` object from an arrow function, it takes them from the outer "normal" function.

Here's an example:

```js run
function f() {
  let showArg = () => alert(arguments[0])
  showArg()
}

f(1) // 1
```

As we remember, arrow functions don't have their own `this`. Now we know they don't have the special `arguments` object either.

````


## Spread syntax [#spread-syntax]

We've just seen how to get an array from the list of parameters.

But sometimes we need to do exactly the reverse.

For instance, there's a built-in function [Math.max](mdn:js/Math/max) that returns the greatest number from a list:

```js run
alert( Math.max(3, 5, 1) ); // 5
```

Now let's say we have an array `[3, 5, 1]`. How do we call `Math.max` with it?

Passing it "as is" won't work, because `Math.max` expects a list of numeric arguments, not a single array:

```js run
let arr = [3, 5, 1];

*!*
alert( Math.max(arr) ); // NaN
*/!*
```

And surely we can't manually list items in the code `Math.max(arr[0], arr[1], arr[2])`, because we may be unsure how many there are. As our script executes, there could be a lot, or there could be none. And that would get ugly.

*Spread syntax* to the rescue! It looks similar to rest parameters, also using `...`, but does quite the opposite.

When `...arr` is used in the function call, it "expands" an iterable object `arr` into the list of arguments.

For `Math.max`:

```js run
let arr = [3, 5, 1];

alert( Math.max(...arr) ); // 5 (spread turns array into a list of arguments)
```

We also can pass multiple iterables this way:

```js run
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(...arr1, ...arr2) ); // 8
```

We can even combine the spread syntax with normal values:


```js run
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25
```

Also, the spread syntax can be used to merge arrays:

```js run
let arr = [3, 5, 1];
let arr2 = [8, 9, 15];

*!*
let merged = [0, ...arr, 2, ...arr2];
*/!*

alert(merged); // 0,3,5,1,2,8,9,15 (0, then arr, then 2, then arr2)
```

In the examples above we used an array to demonstrate the spread syntax, but any iterable will do.

For instance, here we use the spread syntax to turn the string into array of characters:

```js run
let str = "Hello";

alert( [...str] ); // H,e,l,l,o
```

The spread syntax internally uses iterators to gather elements, the same way as `for..of` does.

So, for a string, `for..of` returns characters and `...str` becomes `"H","e","l","l","o"`. The list of characters is passed to array initializer `[...str]`.

For this particular task we could also use `Array.from`, because it converts an iterable (like a string) into an array:

```js run
let str = "Hello";

// Array.from converts an iterable into an array
alert( Array.from(str) ); // H,e,l,l,o
```

The result is the same as `[...str]`.

But there's a subtle difference between `Array.from(obj)` and `[...obj]`:

- `Array.from` operates on both array-likes and iterables.
- The spread syntax works only with iterables.

So, for the task of turning something into an array, `Array.from` tends to be more universal.


## Get a new copy of an array/object

Remember when we talked about `Object.assign()` [in the past](info:object-copy#cloning-and-merging-object-assign)?

It is possible to do the same thing with the spread syntax.

```js run
let arr = [1, 2, 3];
let arrCopy = [...arr]; // spread the array into a list of parameters
                        // then put the result into a new array

// do the arrays have the same contents?
alert(JSON.stringify(arr) === JSON.stringify(arrCopy)); // true

// are the arrays equal?
alert(arr === arrCopy); // false (not same reference)

// modifying our initial array does not modify the copy:
arr.push(4);
alert(arr); // 1, 2, 3, 4
alert(arrCopy); // 1, 2, 3
```

Note that it is possible to do the same thing to make a copy of an object:

```js run
let obj = { a: 1, b: 2, c: 3 };
let objCopy = { ...obj }; // spread the object into a list of parameters
                          // then return the result in a new object

// do the objects have the same contents?
alert(JSON.stringify(obj) === JSON.stringify(objCopy)); // true

// are the objects equal?
alert(obj === objCopy); // false (not same reference)

// modifying our initial object does not modify the copy:
obj.d = 4;
alert(JSON.stringify(obj)); // {"a":1,"b":2,"c":3,"d":4}
alert(JSON.stringify(objCopy)); // {"a":1,"b":2,"c":3}
```

This way of copying an object is much shorter than `let objCopy = Object.assign({}, obj);` or for an array `let arrCopy = Object.assign([], arr);` so we prefer to use it whenever we can.


## Summary

When we see `"..."` in the code, it is either rest parameters or the spread syntax.

There's an easy way to distinguish between them:

- When `...` is at the end of function parameters, it's "rest parameters" and gathers the rest of the list of arguments into an array.
- When `...` occurs in a function call or alike, it's called a "spread syntax" and expands an array into a list.

Use patterns:

- Rest parameters are used to create functions that accept any number of arguments.
- The spread syntax is used to pass an array to functions that normally require a list of many arguments.

Together they help to travel between a list and an array of parameters with ease.

All arguments of a function call are also available in "old-style" `arguments`: array-like iterable object.
````
