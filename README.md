# rfc-js-null-defaults
A proposal for JS to support defaults for `null` values.

JavaScript now supports defaulting values that are `undefined` in function arguments and in destructuring.

It would be nice to be able to opt-in to defaulting in the case of `null` as well as `undefined`.

Related to these proposals:
* https://github.com/tc39-transfer/proposal-nullish-coalescing
* https://github.com/tc39/proposal-optional-chaining

This could be implemented as a new operator, e.g., `?=`:

```js
const foo = (bar ?= 'a') => {
 // ...
}

const { bar ?= 'a' } = host
```

Equivalent to:

```js
const foo = bar => {
 bar = bar == null ? 'a' : bar
}

const bar = host.bar == null ? 'a' : host.bar
```

Or perhaps as a default feature of the language with the existing defaulting syntax, but opted into with a prologue directive or pragma comment for backwards-compatibility:

```js
// @null-defaults

const foo = (bar = 'a') => {
 // ...
}

const { bar = 'a' } = host
```

or:

```js
'null-defaults';

const foo = (bar = 'a') => {
 // ...
}

const { bar = 'a' } = host
```

May be possible to implement as a helper function (with `Function::toString`) or decorator (https://github.com/tc39/proposal-decorators) and/or a Babel plugin transform, e.g.:

```js
import nullDefaults from 'null-defaults'

const foo = nullDefaults((bar = {}) => {
  // ...
})
```

or as a decorator:

```js
const foo = @nullDefaults (bar = {}) => {
  // ...
}
```

where `nullDefaults`, untranspiled, would:
1. Parse the function definition and find arguments with default values with `Function::toString`.
2. Wrap that function in a function that accepts the same parameters, but applies defaults even when null. Note: this would not work with defaults referring to variables in scope at definition site.

Whereas transpilation would inject the null-checking at the top of the function using the defaults.
