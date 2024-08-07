> The UI has a render loop. Let's use it in React.

The `useValue` and the `useDetector` hooks allow multiple components to observe changes to a variable and trigger re-renders automatically whenever the variable changes. You can use it for "sharing state" between components without using React's Context API.

### Install

```bash
# npm:
npm install use-detector
# bun:
bun add use-detector
# jsr:
npx jsr add @tsx/use-detector
```

> `requestAnimationFrame` is used under the hood by default. You may use `setTimeout` instead with `configureWithTimeout(interval)`. You may find more about it in this documentation.

### Simple Example

```jsx
import { useValue } from "use-detector"

let count = 0

function Count() {
  useValue(() => count)

  return <p>{count}</p>
}

function DoubleCount() {
  const doubleCount = useValue(() => count * 2)

  return <p>{doubleCount}</p>
}

export default function App() {
  return (
    <div>
      <Count />
      <DoubleCount />
      <button onClick={() => count++}>Increment</button>
      <button onClick={() => count--}>Decrement</button>
    </div>
  )
}
```

### Example using `setTimeout`

```jsx
import { useValue, configureWithTimeout } from "use-detector"

const interval = 50

configureWithTimeout(interval)

let count = 0

function Count() {
  useValue(() => count)

  return <p>{count}</p>
}

export default function App() {
  return (
    <div>
      <Count />
      <button onClick={() => count++}>Increment</button>
    </div>
  )
}
```

### Full Example Usage

```tsx
import { useDetector } from "use-detector"

// shared state example
let count = 0

function DoubleCount() {
  // for derived state
  const double = () => count * 2

  useDetector(double(), double)

  return <p>{double()}</p>
}

// optional compare function, useful for reference types
function compare<T>(prev: T, next: T) {
  return prev === next
}

// optional key
const key = "count"

function Count() {
  useDetector(count, () => count, compare, key)

  return <p>{count}</p>
}

export default function App() {
  return (
    <div>
      <Count />
      <DoubleCount />
      <button onClick={() => count++}>Increment</button>
      <button onClick={() => count--}>Decrement</button>
    </div>
  )
}
```

### Example: state in an object

```ts
// file: store.ts

export const state = {
  count: 0,
  textInput: "",
}
```

Then you may use the state object in your components like this:

```tsx
import { useValue } from "use-detector"

import { state } from "./store"

function Count() {
  useValue(() => state.count)

  return <p>{state.count}</p>
}

function TextInput() {
  useValue(() => state.textInput)

  return (
    <input
      value={state.textInput}
      onChange={(e) => {
        state.textInput = e.target.value
      }}
    />
  )
}

export default function App() {
  return (
    <div>
      <Count />
      <TextInput />
      <button onClick={() => state.count++}>+</button>
      <button onClick={() => state.count--}>-</button>
    </div>
  )
}
```

### Full type definition

```ts
function useValue<T>(
  getValue: () => T,
  compare?: Comparator<T>, // optional
  key?: string, // optional
): T
```

```ts
function useDetector<T>(
  oldValue: T,
  getNewValue: () => T,
  compare?: Comparator<T> = (prev, next) => prev === next, // optional
  key?: string, // optional
): T
```

```ts
// To use setTimeout instead of requestAnimationFrame
function configureWithTimeout(interval?: number): void
```

### License

[The MIT License](./license)

### Author

Arafat Husayn
