# OOPs - Accidental object oriented programming

ES6 brought the all too familiar "class" syntax to the world of Javascript. About time! Right? ...Maybe not.

Let's revisit the ideas behind object-oriented programming (OOP) through a slightly different lens.

## Reconstructing OOP

We'll start with a simple function that just returns the argument passed in to it. Let's call it `identity`.

```javascript
const identity = a => a

// Prints: 1
console.log(identity(1))
```

Let's re-write `identity` slightly to defer its execution. We'll return a function instead of a value:

```javascript
const deferredIdentity = a => () => a

// Prints: [Function]
console.log(deferredIdentity(1))
// Prints: 1
console.log(deferredIdentity(1)())
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

Now we can access these functions:

```javascript
const [tenPlusFive, justTen, tenMinus] = someFunctions(10)

// Prints: 15
console.log(tenPlusFive())
// Prints: 10
console.log(justTen())
// Prints: 5
console.log(tenMinus(5))
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

// Prints: 15
console.log(ten.plusFive())
// Prints: 10
console.log(ten.just())
// Prints: 5
console.log(ten.minus(5))
```

Progress!

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
// Prints: 1
console.log(counter.count())
counter.increment()
// Prints: 2
console.log(counter.count())
counter.decrement()
// Prints: 1
console.log(counter.count())
```

Now we support state! Truly private state!

Note that there is no way to access the `sharedState` variable from the outer scope. It can only be manipulated by calling `increment` and `decrement`. No special keywords or syntax is required to make it private.

If our functions can share private variables, surely they can share functions too. Let's make a counter which will print its current count whenever someone interacts with it:

```javascript
const makeShoutyCounter = () => {
  let count = 0
  const shout = () => console.log(
      `My current count is ${count}!`
  )

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

// No need to print manually anymore,
// the counter will do it for us.

// Prints: My current count is 1!
shoutyCounter.increment()
// Prints: My current count is 2!
shoutyCounter.increment()
// prints: My current count is 1!
shoutyCounter.decrement()
```

Now we have something that's starting to resemble a class. Our constructor is `makeShoutyCounter`, we have public methods like `increment`, private methods like `shout`, and even state like `count`.

Finally, let's tackle inheritance. To inherit from a class we can start by creating an instance of the desired superclass. We can then intercept calls to any methods we wish to override and forward everything else to the superclass instance. Let's try that now by creating a counter which inherits from `ShoutyCounter` and overrides its `increment` method:

```javascript
const makeInheritedShoutyCounter = () => {
  // Counter is our super-class, so let's make one of those
  const superRef = makeShoutyCounter()

  return {
    ...superRef,
    increment: () => {
      console.log("Incrementing!")
      return superRef.increment()
    },
  }
}

const inheritedShoutyCounter = makeInheritedShoutyCounter()
// Prints: Incrementing! My current count is 1!
inheritedShoutyCounter.increment()
 // Prints: Incrementing! My current count is 2!
inheritedShoutyCounter.increment()
// Prints: My current count is 1!
inheritedShoutyCounter.decrement()

```

So far we've implemented the core features of classes in OOP. We have constructors, private and public methods, state encapsulation, and inheritance. All built without any traditional OOP syntax.

## Conclusion

The point of all this is not that you should build your programs in this way. The point is to show that we can decompose the ideas behind OOP into fundamentally simpler units: functions, variables, and maps.

We should also decompose our programs in the same way. By doing so, we can express the essence of our software in fundamentally simpler ways. By making classes our primary unit of abstraction, we're stuck building our programs on an inherently complicated idea, which is leading to inherently complicated software.
