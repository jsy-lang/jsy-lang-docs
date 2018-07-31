# JSY Language Documentation

Offside (indention) Javascript syntax extension.

Sick of these `{}` and these `;`? Start with JSY today!

- [Operators](#operators)
- [Keyword Operators](#keyword-operators)
- [Uncommon Operators](#operators-uncommon-use-cases)
- [Idioms](#jsy-idioms)

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

There are at-based (`@`), double colon-based (`::`), and keyword operators (`if`, `for`, `while`, etc.). All operators wrap until the indentation is equal to or farther out than the current line, similar to Python or CoffeeScript. We refer to this as the *indented block*. For example:

```javascript
function add( a, b ) ::
  return a + b
```

The double colon `::` in the preceding example opens a brace `{` when used, then closes the matching brace `}` when the indentation level matches that of the line it was first used on. The *indented block* is the return statement.

### Implicit (Leading) Commas

When using any `@`-prefixed operator and starting a new indented line, multiple items can be listed without commas.

Referencing the quick start example: `add`, `modify`, and `retrieve` are at the same indent level, which would normally require commas in JavaScript. JSY will attempt to add those commas automatically. If this would result in a syntax error, then JSY assumes the expression is valid without them.

When using an `@`-prefixed operator with multiple items on the same line, commas must be used.

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

### Keyword Operators

For keywords with expressions, when not followed by a paren, everything between the keyword and the double colon `::` is captured as the keyword expression. When followed by a paren, parses as normal JavaScript.

No special parsing is done for keywords *without* expressions.

#### `if`/`else` and `while`

```javascript
if a > b ::
  console.log @ 'JSY is the best!'
else if a < b ::
  console.log @ 'JSY rocks!'
else ::
  console.log @ 'JSY is still awesome!'
  
while 0 != q.length ::
  console.log @ q.pop()

// Becomes:
if (a > b) {
  console.log('JSY is the best!')
} else if (a < b) {
  console.log('JSY rocks!')
} else {
  console.log('JSY is still awesome!')
}

while (0 != q.length) {
  console.log(q.pop())
}
```

#### `for`

```javascript
for let i = 0; i < 10; i++ ::
  console.log @: i
  
for const val of [1,'two',0xa] ::
  console.log @: val
  
for await const ea of someAsyncGenerator() ::
  console.log @: ea

// Becomes:
for (let i = 0; i < 10; i++) {
  console.log({i})
}
  
for (const val of [1,'two',0xa]) {
  console.log({val})
}

for await (const ea of someAsyncGenerator()) {
  console.log({ea})
}
```

#### `try`, `catch`, and `finally`

```javascript
try ::
  if 0.5 > Math.random() ::
    throw new Error @ 'Oops!'
catch err ::
  console.error @ err
finally ::
  console.log @ 'Finally.'

// Becomes:
try {
  if (0.5 > Math.random()) {
    throw new Error('Oops!')
  }
} catch (err) {
  console.error(err)
} finally {
  console.log('Finally.')
}
```

#### `switch`

```javascript
switch command ::
  case 'play':
    player.play()
    break
  case 'pause':
    player.pause()
    break
  default:
    player.stop().eject()

// Becomes:
switch (command) {
  case 'play':
    player.play()
    break
  case 'pause':
    player.pause()
    break
  default:
    player.stop().eject()
}
```

### Operators (Uncommon Use Cases)

Uncommon use cases for `::`-prefixed operators require explicit commas, whereas the `@`-prefixed operators allow for the implicit use of commas.

| Uncommon Operator | Alias for | Use Instead |
| ----------------- | --------- | ----------- |
| `::{}`            | `::`      | `::`        |
| `::[]`            |           | `@[]`       |
| `::()`            |           | `@`         |
| `::@`             | `::()`    | `@`         |
| `@()`             | `@`       | `@`         |

## JSY Idioms

A few examples of why JSY is awesome.

### Idiom #1 – Arrow Function Returning Hash

```javascript
res.data.map @
  personData => @:
    fullName: `${ personData.info.fName } ${ personData.info.lName }`
    dateOfBirth: moment @ personData.info.dob
```

### Idiom #2 – Arrow Function Compose

```javascript
function double(x) :: return x + x
function add(x, y) :: return x + y

const clamp = (min, max, score) =>
  Math.max @ min,
  	Math.min @ max, score

const calcScore = v => @
  v = double @ v
  v = add @ 7, v
  v = clamp @ 0, 100, v
```

## Licenses

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" />
</a><br/>
Documentation is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

Examples and source code are licensed under [BSD 2-Clause](./LICENSE).