![repo-banner](./assets/wobble-logo.png)

A tiny (~1.8 KB gzipped) spring physics micro-library that models a [damped harmonic oscillator](https://en.wikipedia.org/wiki/Harmonic_oscillator#Damped_harmonic_oscillator).

# What is this fork?

The original _wobble_ library, while very well implemented, has a long-standing [pending PR](https://github.com/skevy/wobble/pull/10) that reworks the implementation to allow for consumers to use their own `requestAnimationFrame()` loop, and that removes unnecessary allocations on updating a given `spring`'s configuration. This fork merges those changes with the base repo and cleans them up, documenting the added functionality and moving the `raf` loop to be handled in `start()`, as is mentioned [here](https://github.com/skevy/wobble/pull/10#discussion_r217296166).

I couldn't replicate the ~1.7 KB figure on my machine to compare the size of my changes, but it's about a KB (~1.34 KB versus ~1.43 KB gzipped using 7-Zip). I've added .1 KB to the size at the top of the description.

For more information on _wobble_ itself, see its [description](https://github.com/skevy/wobble/blob/develop/README.md).

# API Changes

## Configuration

#### `requestAnimationFrame: boolean`

Whether or not the spring requests its own animation frame. Defaults to `true`.

## Methods

#### `step(timestamp: number = Date.now()): Spring`

Advances the simulation, using the current time if no `timestamp` is provided.

#### `setToValue(value: number): Spring`

Updates the spring config's `toValue` with the provided value.

# Usage of Changes

```jsx
import { Spring } from 'wobble';

function newSpring() {
  // Create a new spring that doesn't animate itself.
  const spring = new Spring({
    toValue: 100,
    requestAnimationFrame: false,
  });

  // Set listeners for spring events, start the spring.
  spring
    .onUpdate((s) => {
      console.log(`Spring's current value: ` + s.currentValue);
      console.log(`Spring's current velocity: ` + s.currentVelocity);
    })
    .start();

  return spring;
}

// Create an array of 5 new springs.
const springs = [];

for (i in [...Array(5).keys()]) {
  springs.push(newSpring());
}

// Simulate each spring with one 'requestAnimationFrame()' call.
function simulate() {
  const time = Date.now();
  const notifyListeners = true;

  springs.forEach((spring) => {
    // Advance the spring's simulation, notifying its listeners.
    spring.step(time, notifyListeners);
  });

  // Set a random spring's 'toValue' to a random number.
  const index = Math.floor(springs.length * Math.random());
  springs[index].setToValue(100 + 50 * Math.random());

  requestAnimationFrame(simulate);
}

// Start the simulation.
simulate();
```

# Credits

Brenton Simpson ([@appsforartists](https://twitter.com/appsforartists)) - For his assistance in creating and testing this library.

Devine Lu Linvega ([@neauoire](https://twitter.com/neauoire)) - The awesome logo!

Jonathan Dumaine ([@JonDum](https://github.com/JonDum)) - The changes in his [fork](https://github.com/JonDum/wobble).