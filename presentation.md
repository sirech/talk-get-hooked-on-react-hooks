title: Get hooked on React hooks
class: animation-fade
layout: true


<!-- This slide will serve as the base layout for all your slides -->

---

class: impact

# {{title}}
## Mario Fernandez

???

- outline: where are we, what are hooks, why use them, intro, patterns

---

class: middle center

### Where is React today?

---

class: center middle

## Times They Are a-Changin

.col-4[
![ts](images/ts.png)
]

.col-4[
![formik](images/formik.png)
]

.col-4[
![react-testing-library](images/react-testing-library.png)
]

---

class: center middle

.col-6[
![react](images/react.png)
]
.col-6[
![hook](images/hook.png)
]

---

class: impact

# What are hooks?

---

class: middle 

> *Hooks* are a new addition in **React 16.8**. They let you use state and other React features without writing a class.

???

Released on February 6th, 2019

---

class: center middle

### Hooks are just functions

---

class: middle

## useState

```typescript
import React, { useState } from 'react'

const Counter = () => {
  const [count, setCount] = useState<number>(0)

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  )
}
```

???

- straight out of the React docs
- value + function

---

class: center middle

### https://dev.to/dan_abramov/making-sense-of-react-hooks-2eib

---

class: impact

# Why hooks?

---

class: center middle

### Functional components over classes

???

- Stylistic reasons
- tree shaking

---

class: center middle

### Encapsulate stateful logic

???

- replace complex solutions like render props or HOC
- avoid changing component hierarchy

---

class: center middle

### Does anybody truly understand lifecycle methods?

???

- lifecycle methods seem like the most complex part of React


---

class: impact

# useEffect

???

- second main hook to learn

---

class: center middle

### perform side effects

???

- alternative to:
-- componentDidMount
-- componentDidUpdate
-- componentWillUnmount

---

class: middle

```typescript
import React, { useEffect } from 'react'

const Counter = ({ count }: { count: number }) => {
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
    </div>
  )
}
```

???

- gets called every time when the component gets updated

---

class: middle

```typescript
// similar to componentDidMount
useEffect(fn, []);
```


???

- second argument is dependency array

---

class: middle

```typescript
// similar to componentDidMount + componentDidUpdate
useEffect(fn, [counter]);
```

---

class: middle

```typescript
// similar to componentWillUnmount
useEffect(() => {
  return unmountFn
}, [counter]);
```

---

class: center middle

### https://overreacted.io/a-complete-guide-to-useeffect/

???

- `useEffect` has a lot of subtle details

---

class: impact

# Case Study: REST populated component

---

class: full-width
background-image: url(images/rest-flow.jpg)

---

class: center

```typescript
const Stuff = ({ id }: { id: number }) => {
  const [error, setError] = useState<boolean>(false)
  const [data, setData] = useState<string | undefined>()

  useEffect(() => {
    const fetchData = async () => {
      try {
        setError(false)
        const result = await axios(`/route/${id}`)
        setData(result)
      } catch (e) { setError(true) } 
    }

    fetchData()
  }, [id])
  
  ...
}
```

---

class: middle

```typescript
const Stuff = ({ id }: { id: number }) => {

  ...

  return (
    <>
      {error && 'Oh noooo'}
      {data && <p>{data}</p>}
    </>
  )
}
```

---

class: center middle

### https://github.com/streamich/react-use

---

class: center

```typescript
import { useAsync } from 'react-use'

const Stuff = ({ id }: { id: number }) => {
  const { loading, error, value } = useAsync(async () => {
    const result = await axios(`/route/${id}`)
    return result
  }, [id])

  return (
    <>
      {loading && 'Loading...'}
      {error && 'Oh noooo'}
      {value && <p>{value}</p>}
    </>
  )
}
```

---

class: impact

# Case Study: Context

---

class: full-height
background-image: url(images/config-flow.jpg)

---

class: middle

```react
const ConfigContext = React.createContext<ConfigType>(
  initConfig
)
```

---

class: middle

```react
const Config = (props: Props) => {
  const state = useAsync(async (): Promise<ConfigType> => {
    return await configuration()
  }, [])

  const { loading, value } = state
  return (
    <ConfigContext.Provider value={value || initConfig}>
      {!loading && props.children}
    </ConfigContext.Provider>
  )
}
```

---

class: middle

```typescript
const useConfig = () =>
  useContext<ConfigType>(ConfigContext)
```

---

class: middle

## Consuming context

```typescript
const ConfigClient = () => {
  const { country } = useConfig()
  return (
    <>
      Country from the config: {country}
    </>
  )
}
```

---

class: middle

## The old way using a HOC

```typescript
const withConfig = <P extends object>(
  WrappedComponent: React.ComponentType<P & WithConfigProps>
) => {
  class WithConfig extends Component<
    Pick<P, Exclude<keyof P, keyof WithConfigProps>>
  > {
    static contextType = ConfigContext
    render() {
      const config: Config = this.context
      const props = { config, ...(this.props as P) }
      return <WrappedComponent {...props} />
    }
  }

  return WithConfig
}
```

---

class: impact

# When all you have is a hook ...

---

class: middle

```typescript
const auth = useMemo<ContextType>(
  () => ({
    user,
    login,
    logout: () => logout(setUser),
    checkLogin: () => checkLogin(setUser)
  }),
  [user]
)
```

---

class: middle

```typescript
const AddressForm = ({ index }: Props) => {
  const { formatMessage } = useIntl()
  return (
    <div className={styles.body}>
      <Input
        name={`addresses.${index}.street`}
        label={formatMessage({ id: 'address.street' })}
      />
    </div>
  )
}
```

---

class: center middle

### useReducer
![redux](./images/redux.png)

---

class: center middle

### https://www.robinwieruch.de/react-usereducer-hook

---

class: impact

# That's kinda cool, huh?

---

class: center middle

### Consistency

---

class: center middle

### Avoid props pollution

---

class: impact

# Custom Hooks

---

class: middle

```typescript
const useFieldValues = () => {
  const { locale } = useIntl()
  const { value, loading, error } = useAsync(
    async () => await getFieldValues(locale)
  )
  return { fieldValues: value, loading, error }
}
```

???

- hooks are just functions
- convention: start with *use*

---

class: impact

# Rules of Hooks

---

class: center middle

### Only Call Hooks at the Top Level

---

class: center middle

### Only Call Hooks from React Functions

???

- It boils down to keeping the same order of calls after every update

---

class: middle

```json
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // Checks rules of Hooks
    "react-hooks/exhaustive-deps": "warn" // Checks effect dependencies
  }
}
```

???

No need to remember anything, just add the rule to eslint

---

class: impact

# Are we going to test this?

---

class: center middle

```typescript
import { waitForElement, render } from '@testing-library/react'
import RecipeDetails from './RecipeDetails'

jest.mock('recipe-details/recipeDetails.service')

describe('RecipeDetails', () => {
  it('renders correctly', async () => {
    const { getByText } = render(<RecipeDetails id={1} />)
    await waitForElement(() => getByText('Pasta Carbonara'))
  })
})
```

---

class: center middle

```typescript
import { renderHook, act } from '@testing-library/react-hooks'
import useCounter from './useCounter'

test('should increment counter', () => {
  const { result } = renderHook(() => useCounter())

  act(() => {
    result.current.increment()
  })

  expect(result.current.count).toBe(1)
})
```

---

class: impact


# Are hooks ready for prime time?

---

class: center middle

### Yes!

---

class: impact

# Links

---

class: middle

- https://reactjs.org/docs/hooks-intro.html
- https://wattenberger.com/blog/react-hooks
- https://www.robinwieruch.de/react-hooks-fetch-data

---

