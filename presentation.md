title: Get hooked on React hooks
class: animation-fade
layout: true


<!-- This slide will serve as the base layout for all your slides -->

---

class: impact

# {{title}}

---

# Outline

- react ecosystem in late 2019
- what are hooks?
- why use this style?

- useState
- useEffect

- Examples
-- Load once
-- load on props change
-- async call
-- context

- custom hook
- react-use

- caveats
-- eslint!

---

## Party like it's 2018

picture of redux
picture of enzyme
picture of EcmasScript

---

class: impact

# What are hooks?

---

class: middle 

> *Hooks* are a new addition in **React 16.8**. They let you use state and other React features without writing a class.

???

Released on February 6th, 2019

---

useState example

---

class: impact

# Why hooks?

---

class: center middle

### Functional components over classes

???

- Stylistic reasons
- tree shaking
- lifecycle methods

---

class: center middle

### Encapsulate stateful logic

???

- replace complex solutions like render props or HOC
- avoid changing componen hierarchy

---

class: impact

# useEffect

---

sample useEffect
different configuration options

---

class: impact

# Case Study: REST populated component

---

diagram with flow

---

sample rest loading/error/content

---

class: center middle

### https://github.com/streamich/react-use

---

sample using useAsync

---

class: impact

# Case Study: Context

---

class: center middle

```react
const ConfigurationContext = React.createContext<ConfigurationType>(
  defaultConfiguration
)

const Configuration = (props: Props) => {
  const state = useAsync(async (): Promise<ConfigurationType> => {
    return await configuration()
  }, [])

  const { children } = props
  const { loading, value } = state

  return (
    <ConfigurationContext.Provider value={value || defaultConfiguration}>
      {!loading && children}
    </ConfigurationContext.Provider>
  )
}
```

---

class: middle

```typescript
const useConfig = () =>
  useContext<ConfigurationType>(ConfigurationContext)
```

---

class: middle

## Consuming context

```typescript
const ConfigurationClient = () => {
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
    static contextType = ConfigurationContext
    render() {
      const config: Configuration = this.context
      const props = { config, ...(this.props as P) }
      return <WrappedComponent {...props} />
    }
  }

  return WithConfig
}
```

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

# Endless options

---

useReducer
useIntl
useMemo
useLocalStorage

---

class: impact

# Advantages

---

class: center middle

### Consistency

---

class: center middle

### Avoid props pollution

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

# Links

---

class: middle

- https://reactjs.org/docs/hooks-intro.html
- https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889
- https://wattenberger.com/blog/react-hooks
- https://overreacted.io/a-complete-guide-to-useeffect/
- https://www.robinwieruch.de/react-hooks-fetch-data


