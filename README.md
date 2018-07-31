# JSY Language Documentation

Offside (indention) Javascript syntax extension.

Sick of these `{}` and these `;`? Start with JSY today!

## Quick Start

```javascript
const apiUrl = 'http://api.example.com'

class ExampleApi extends SomeBaseClass ::
  constructor( credentials ) ::
    const apiCall = async ( pathName, body ) => ::
      const res = await fetch @ `${apiUrl}/${pathName}`, @{}
        method: 'POST'
        headers: @{}
        	'Content-Type': 'application/json'
        body: JSON.stringify @ body

      return await res.json()
      
    Object.assign @ this, @{}
      add: data => apiCall @ 'add', data
      modify: data => apiCall @ 'send', data
      retrieve: data => apiCall @ 'get', data
```

## Reference

There are at-based (`@`), double colon-based (`::`), and keyword operators (`if`, `for`, `while`, etc.). All operators wrap until the indentation is equal to or farther out than the current line, similar to Python or CoffeeScript. For example:

```javascript
function add( a, b ) ::
  return a + b
```
 
 The double colon `::` in the preceding example opens a brace `{` when used, then closes the matching brace `}` when the indentation level matches that of the line it was first used on.

### Operators

#### `::` Double Colon – Code Blocks

The `::` operator wraps the *indented block* in curly braces `{«block»}`.

```javascript
function add( a, b ) ::
  return a + b

// Becomes:
function add( a, b ) {
  return a + b
}
```

#### `@` At – Calling Functions
The `@` operator on its own wraps the *indented block* in parentheses `(«block»)`, where commas are implicit.

```javascript
console.log @
  add @ 2, 3

// Becomes:
console.log(
  add( 2, 3 )
)
```

#### `@{}` At Braces – Hashes
The `@{}` operator wraps the *indented block* in curly braces `{«block»}`, where commas are implicit.

```javascript
fetch @ 'http://api.example.com', @{}
  method: 'POST'
  headers: @{}
    'Content-Type': 'application/json'
  body: JSON.stringify @ body

// Becomes:
fetch( 'http://api.example.com', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify( body )
})
```

#### `@:` At Colon – Calling with Hash
The `@:` operator wraps the *indented block* in parentheses wrapping curly braces `({«block»})`, where commas are implicit.

```javascript
request @:
  url: 'http://api.example.com'
  method: 'POST'
  headers: @{}
    'Content-Type': 'application/json'
  body: JSON.stringify @ body

// Becomes:
request({
  url: 'http://api.example.com',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify( body )
})
```

#### `@[]` At Brackets - Lists
The `@[]` operator wraps the *indented block* in square brackets `[«block»]`, where commas are implicit.

```javascript
const tri = @[]
  @[]  0.0,  1.0, 0.0
  @[] -1.0, -1.0, 0.0
  @[]  1.0, -1.0, 0.0

// Becomes:
const tri = [
  [0.0, 1.0, 0.0],
  [-1.0, -1.0, 0.0],
  [1.0, -1.0, 0.0]
]
```


#### `@#` At Pound – Calling with a List
The `@#` operator wraps the *indented block* in parentheses wrapping square brackets `([«block»])`, where commas are implicit.

```javascript
Promise.all @#
  fetch @ 'http://api.example.com/dosomething'
  fetch @ 'http://api.example.com/dosomethingelse'
  fetch @ 'http://api.example.com/dosomethingmore'

// Becomes:
Promise.all([
  fetch('http://api.example.com/dosomething'),
  fetch('http://api.example.com/dosomethingelse'),
  fetch('http://api.example.com/dosomethingmore')
])
```

#### `@=>` At Arrow – Arrow Function with No Arguments
The `@=>` operator wraps the *indented block* in parentheses and begins an arrow function `(()=>«block»)`.

```javascript
const call = fn => fn()

call @=> ::
  console.log @ 'Do cool things with JSY!'

// Becomes:
const call = fn => fn()

call( () => {
  console.log('Do cool things with JSY!')
})
```

#### `@=>>` At Double Arrow – Async Arrow Function with No Arguments
The `@=>>` operator wraps the *indented block* in parentheses and begins an async arrow function `(async ()=>«block»)`.

```javascript
describe @ 'example test suite', @=> ::
  it @ 'some test', @=>> ::
    const res = await fetch @ 'http://api.example.com/dosomething'
    await res.json()

// Becomes:
describe('example test suite', ( () => {
  it('some test', (async () => {
    const res = await fetch('http://api.example.com/dosomething')
    await res.json()
  }))
}))
```


### Operators (Uncommon Use Cases)

#### `::[]` Double Colon Brackets *(uncommon usage)*

The `::[]` operator wraps the *indented block* in square brackets `[«block»]`, where commas must be explicit.

> See `@[]` or `@#` operator for this syntax with allows implicit commas, which is the preferred usage.

#### `::()` Double Colon Parens *(uncommon usage)*

The `::()` operator wraps the *indented block* in parentheses `(«block»)`, where commas must be explicit.

> See `@` operator for this syntax with does allows implicit commas, which is the preferred usage.

## Licenses

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" />
</a><br/>
Documentation is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

Examples and source code are licensed under [BSD 2-Clause](./LICENSE).