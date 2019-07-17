# OOPs - Accidental object oriented programming

ES6 brought the all too familiar "class" syntax to the world of Javascript. About time! Right??

Maybe not. Let's revisit the ideas behind object-orientated programming (OOP) through a slightly different lens.

---

We'll start with a simple function that just returns the argument passed to it. We'll call it `identity`.

```javascript
const identity = a => a

print(identity(1)) // prints: 1
```

`identity` is a perfectly fine function, but let's just re-write it slightly to delay its execution. We'll return a function instead of a value:

```javascript
const deferredIdentity = a => () => a

print(deferredIdentity(1))   // prints: [Function]
print(deferredIdentity(1)()) // prints: 1
```

Not very useful in and of itself, perhaps, but this trick will come in handy now. Let's give ourselves some options by returning multiple functions:

```javascript
const someFunctions = a => [
  () => a + 5,
  () => a,
  // Let's spice things up with an additional argument!
  (x) => a - x,
]
```

We can pick out these functions with some destructuring:

```javascript
const [tenPlusFive, justTen, tenMinus] = someFunctions(10)

print(tenPlusFive()) // prints: 15
print(justTen())     // prints: 10
print(tenMinus(5))   // prints: 5
```


But remembering the order is hard and pointless. Let's give them names by putting them in a map instead:

```javascript
const someNamedFunctions = a => ({
  plusFive: () => a + 5,
  just:     () => a,
  minus:    (x) => a - x,
})
```

Like before, we could pick out the functioning with destructuring. But let's just name the map that we return instead:

```javascript
const ten = someNamedFunctions(10)

print(ten.plusFive()) // prints: 15
print(ten.just())     // prints: 10
print(ten.minus(5))   // prints: 5
```

Progress!

If you squint it almost looks like using an object in OOP. But there's really no reason that we *have* to return our functions right away. If we give them some breathing room, we might just open up the ability for them to collaborate.

Let's try building a little machine that knows how to count by putting a variable in said breathing room that our functions can access:

```javascript
const makeCounter = () => {
  let sharedState = 0
  return {
    increment: () => ++sharedState,
    decrement: () => --sharedState,
    count:     () => sharedState,
  }
}

const counter = makeCounter()

counter.increment()
print(counter.count()) // prints: 1
counter.increment()
print(counter.count()) // prints: 2
counter.decrement()
print(counter.count()) // prints: 1
```

Now we support state! Truly private state too! Note that there is no way to access `sharedState` variable directly. It can only be manipulated by calling `increment` and `decrement`. No special keywords or syntax required.

If our functions can share private variables, surely they can share functions too. Let's try it out by building a slightly louder counter that will make it very clear what its up to:

```javascript
const makeLouderCounter = () => {
  let count = 0

  const shout = () => print(`My current count is ${count}!!!`)

  return {
    increment: () => {
      count++
      shout()
      return count
    },
    decrement: () => {
      count--
      shout()
      return count
    },
  }
}

const louderCounter = makeLouderCounter()

// No need to print manually anymore, the counter will do it for us!
louderCounter.increment() // prints: My current count is 1!!!
louderCounter.increment() // prints: My current count is 2!!!
louderCounter.decrement() // prints: My current count is 1!!!
```

Now we have something that's starting to resemble a class. Our constructor is `makeLouderCounter`, we have public methods like `increment`, private methods like `decrement`, and even state like `count`.

In any OOP course, you'll learn about inheritance - the idea that one class classes can inherit the functionality of other classes.

Let's try that now by reproducing our `loudCounter` by "inheriting" from the first counter we made.

```javascript
const makeLoudInheritedCounter = () => {
  // Counter is our super-class, so let's make one of those
  let superRef = makeCounter()

  const shout = () => print(`My current count is ${superRef.count()}!!!`)

  return {
    // @override (because who doesn't love annotations)
    increment: () => {
      superRef.increment()
      shout(superRef.count())
      return superRef.count()
    },
    decrement: () => {
      superRef.decrement()
      shout(superRef.count())
      return superRef.count()
    },
    count: superRef.count,
  }
}

const loudInheritedCounter = makeLoudInheritedCounter()

loudInheritedCounter.increment() // prints: My current count is 1!!!
loudInheritedCounter.increment() // prints: My current count is 2!!!
loudInheritedCounter.decrement() // prints: My current count is 1!!!
```

---

The point of all this is not that you should build your programs in this way. The point is to show that we can decompose the ideas behind OOP into fundamentally simpler units - namely functions, variables and maps.

We should also decompose our programs in the same way. By doing so we can express the essence of our software in fundamentally simpler ways. By making classes our primary unit of abstraction, we're stuck building our programs on an inherently complicated idea, which is leading to inherently complicated software.

If you are keen on learning more about this style of programming, I highly recommend [Professor Frisby's Mostly adequate guide to Functional Programming](https://github.com/MostlyAdequate/mostly-adequate-guide)
