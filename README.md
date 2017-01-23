# Decox

(WIP) Minimal state manager by decorating a class.

```javascript
import { wrapAsStore, createConnector } from 'decox'

// Define a state as a normal class.
class CounterState {
  constructor(init = 0) {
    this.value = init
  }

  getCount() {
    return this.value
  }

  // Annotate updates by `$`.
  $increment() {
    this.value += 1
  }

  // Annotate updates with side effects by `$$`.
  $$incrementAsync(delay) {
    setTimeout(() => {
      this.$increment()
    }, delay)
  }

  takeSnapshot() {
    return { value: this.value }
  }
}

// Create store as a wrapper of the state.
const CounterStore = wrapAsStore(CounterState)

const store = new CounterStore()

store.onUpdate(({ method }) => {
  console.log(`Updated by ${method}: `, store.takeSnapshot())
})

const connect = createConnector(store)

// ---- view ----

// React Component
const Counter = ({ title, count, increment, incrementAsync }) => (
  <div>
    <p>{title}</p>
    <div>Count: {count}</div>
    <button onClick={increment}></button>
    <button onClick={incrementAsync}></button>
  </div>
)

const CounterContainer = connect(Counter, {
  mapToProps: (store, { title }) => {
    title,
    count: store.getCount(),
    increment: store.$increment,
    incrementAsync: store.$$incrementAsync,
  }
})

ReactDOM.render(
  <CounterContainer title="Counter example" />,
  document.getElementById("root")
)
```
