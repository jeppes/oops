# OOPs - Accidental object oriented programming

ES6 brought the all too familiar "class" syntax to the world of Javascript. About time! Right??

Maybe not. Let's revisit the ideas behind object-oriented programming (OOP) through a slightly different lens.

---

We'll start with a simple function that just returns the argument passed in to it. Let's call it `identity`.

```javascript
const identity = a => a

print(identity(1)) // prints: 1
```

Let's re-write `identity` slightly to defer its execution. We'll return a function instead of a value:

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

Now we can access these fucntions with a [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment):

```javascript
const [tenPlusFive, justTen, tenMinus] = someFunctions(10)

print(tenPlusFive()) // prints: 15
print(justTen())     // prints: 10
print(tenMinus(5))   // prints: 5
```


But returning the functions in a list requires us to remember the order, so let's use a map instead. This will let us name our functions:

```javascript
const someNamedFunctions = a => ({
  plusFive: () => a + 5,
  just:     () => a,
  minus:    (x) => a - x,
})
```

Like before, we could pick out the functioning with a destructuring assignment - but let's just name the map that we return instead:

```javascript
const ten = someNamedFunctions(10)

print(ten.plusFive()) // prints: 15
print(ten.just())     // prints: 10
print(ten.minus(5))   // prints: 5
```

Progress! :tada:

It _almost_ looks like using an object in OOP, but there's really no reason that we *have* to return our functions right away. If we give them some breathing room, we might just open up the ability for them to collaborate.

Let's try building a little machine that knows how to count by declaring a local variable before returning our functions. Our counter can use this variable to keep track of its current state:

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

Now we support state! Truly private state too!

Note that there is no way to access the `sharedState` variable directly. It can only be manipulated by calling `increment` and `decrement` - no special keywords or syntax required.

If our functions can share private variables, surely they can share functions too. Let's make a counter which will print it's current count whenever someone interacts with it:

```javascript
const makeShoutyCounter = () => {
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

const shoutyCounter = makeShoutyCounter()

// No need to print manually anymore, the counter will do it for us!
shoutyCounter.increment() // prints: My current count is 1!!!
shoutyCounter.increment() // prints: My current count is 2!!!
shoutyCounter.decrement() // prints: My current count is 1!!!
```

Now we have something that's starting to resemble a class. Our constructor is `makeShoutyCounter`, we have public methods like `increment`, private methods like `shout`, and even state like `count`.

In any OOP course, you'll learn about inheritance - the idea that one class classes can inherit the functionality of other classes.

Let's try that now by recreating our `shoutyCounter` by "inheriting" from the first counter we made.

```javascript
const makeInheritedShoutyCounter = () => {
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

const inheritedShoutyCounter = makeInheritedShoutyCounter()

inheritedShoutyCounter.increment() // prints: My current count is 1!!!
inheritedShoutyCounter.increment() // prints: My current count is 2!!!
inheritedShoutyCounter.decrement() // prints: My current count is 1!!!
```

So far we've implemented the core features of classes in OOP. We have constructors, private and public methods, state encapsulation, and inheritance. All built without any traditional OOP syntax.

---

The point of all this is not that you should build your programs in this way. The point is to show that we can decompose the ideas behind OOP into fundamentally simpler units: functions, variables, and maps.

We should also decompose our programs in the same way. By doing so, we can express the essence of our software in fundamentally simpler ways. By making classes our primary unit of abstraction, we're stuck building our programs on an inherently complicated idea, which is leading to inherently complicated software.

If you are keen on learning more about this style of programming, I highly recommend [Professor Frisby's Mostly Adequate Guide to Functional Programming](https://github.com/MostlyAdequate/mostly-adequate-guide).
