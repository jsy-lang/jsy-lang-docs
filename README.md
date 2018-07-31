# JSY Language Documentation

JSY is **an indented (offside) JavaScript dialect**. We believe indentation is
better at describing code blocks instead of painstakingly matching open/close
sections `{ } () []`.

Think modern JavaScript — ES6, ES2018 — indented similar to [Python][] or [CoffeeScript][].

Inspired by [Wisp][], JSY primarily operates as a scanner-pass syntax
transformation to change indented (offside) code into the corresponding
open/close matching token code. Thus the internal scanning parser only has to
be aware of `/* comments */` and `"string literals"` rules to successfully
transform code. Thus, as a JavaScript dialect, **JSY automatically keeps pace with modern JavaScript editions!**

 [Python]: https://www.python.org
 [CoffeeScript]: https://coffeescript.org
 [Wisp]: http://www.draketo.de/english/wisp


- [Reference]()
  - [Operators](#operators) – `Promise.race @# api_call(), timeout(ms)`
  - [Keyword Operators](#keyword-operators) – `if 0 == arr.length :: «block»`
  - [Uncommon Operators](#operators-uncommon-use-cases)
  - [Idioms](#jsy-idioms)

- [Ecosystem](#ecosystem)
- [Projects Using JSY](#projects-using-jsy)

## Quick Start

<table style='width: 100%'><tr><th>JSY</th></tr><tr><td style='vertical-align: top;'>
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
</tr></table>

Get started with [rollup-plugin-jsy-babel](https://github.com/shanewholloway/rollup-plugin-jsy-babel)



## Reference

There are at-based (`@`), double colon-based (`::`), and keyword operators (`if`, `for`, `while`, etc.). All operators wrap until the indentation is equal to or farther out than the current line, similar to Python or CoffeeScript. We refer to this as the *indented block*. For example:

<table style='width: 100%'><tr><th>JSY</th></tr><tr><td style='vertical-align: top;'>
  ```javascript
  function add( a, b ) ::
    return a + b
  ```
</tr></table>

The double colon `::` in the preceding example opens a brace `{` when used, then closes the matching brace `}` when the indentation level matches that of the line it was first used on. The *indented block* is the return statement.

### Implicit (Leading) Commas

When using any `@`-prefixed operator and starting a new indented line, multiple items can be listed without commas.

Referencing the quick start example: `add`, `modify`, and `retrieve` are at the same indent level, which would normally require commas in JavaScript. JSY will attempt to add those commas automatically. If this would result in a syntax error, then JSY assumes the expression is valid without them.

When using an `@`-prefixed operator with multiple items on the same line, commas must be used.

### Operators

#### `::` Double Colon – Code Blocks

The `::` operator wraps the *indented block* in curly braces `{«block»}`.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
function add( a, b ) ::
  return a + b

``` <td style='vertical-align: top;'>
```javascript
function add( a, b ) {
  return a + b
}
```
</tr></table>

#### `@` At – Calling Functions
The `@` operator on its own wraps the *indented block* in parentheses `(«block»)`, where commas are implicit.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
console.log @
  add @ 2, 3

``` <td style='vertical-align: top;'>
```javascript
console.log(
  add( 2, 3 )
)
```
</tr></table>

#### `@{}` At Braces – Hashes
The `@{}` operator wraps the *indented block* in curly braces `{«block»}`, where commas are implicit.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
fetch @ 'http://api.example.com', @{}
  method: 'POST'
  headers: @{}
    'Content-Type': 'application/json'
  body: JSON.stringify @ body

``` <td style='vertical-align: top;'>
```javascript
fetch( 'http://api.example.com', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify( body )
})
```
</tr></table>

#### `@:` At Colon – Calling with Hash
The `@:` operator wraps the *indented block* in parentheses wrapping curly braces `({«block»})`, where commas are implicit.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
request @:
  url: 'http://api.example.com'
  method: 'POST'
  headers: @{}
    'Content-Type': 'application/json'
  body: JSON.stringify @ body

``` <td style='vertical-align: top;'>
```javascript
request({
  url: 'http://api.example.com',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify( body )
})
```
</tr></table>

#### `@[]` At Brackets - Lists
The `@[]` operator wraps the *indented block* in square brackets `[«block»]`, where commas are implicit.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
const tri = @[]
  @[]  0.0,  1.0, 0.0
  @[] -1.0, -1.0, 0.0
  @[]  1.0, -1.0, 0.0

``` <td style='vertical-align: top;'>
```javascript
const tri = [
  [0.0, 1.0, 0.0],
  [-1.0, -1.0, 0.0],
  [1.0, -1.0, 0.0]
]
```
</tr></table>


#### `@#` At Pound – Calling with a List
The `@#` operator wraps the *indented block* in parentheses wrapping square brackets `([«block»])`, where commas are implicit.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
Promise.all @#
  fetch @ 'http://api.example.com/dosomething'
  fetch @ 'http://api.example.com/dosomethingelse'
  fetch @ 'http://api.example.com/dosomethingmore'

``` <td style='vertical-align: top;'>
```javascript
Promise.all([
  fetch('http://api.example.com/dosomething'),
  fetch('http://api.example.com/dosomethingelse'),
  fetch('http://api.example.com/dosomethingmore')
])
```
</tr></table>

#### `@=>` At Arrow – Arrow Function with No Arguments
The `@=>` operator wraps the *indented block* in parentheses and begins an arrow function `(()=>«block»)`.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
const call = fn => fn()

call @=> ::
  console.log @ 'Do cool things with JSY!'

``` <td style='vertical-align: top;'>
```javascript
const call = fn => fn()

call( () => {
  console.log('Do cool things with JSY!')
})
```
</tr></table>

#### `@=>>` At Double Arrow – Async Arrow Function with No Arguments

The `@=>>` operator wraps the *indented block* in parentheses and begins an async arrow function `(async ()=>«block»)`.

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
describe @ 'example test suite', @=> ::
  it @ 'some test', @=>> ::
    const res = await fetch @ 'http://api.example.com/dosomething'
    await res.json()

``` <td style='vertical-align: top;'>
```javascript
describe('example test suite', ( () => {
  it('some test', (async () => {
    const res = await fetch('http://api.example.com/dosomething')
    await res.json()
  }))
}))
```
</tr></table>

### Keyword Operators

For keywords with expressions, when not followed by a paren, everything between the keyword and the double colon `::` is captured as the keyword expression. When followed by a paren, parses as normal JavaScript.

No special parsing is done for keywords *without* expressions.

#### `if`/`else` and `while`

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
if a > b ::
  console.log @ 'JSY is the best!'
else if a < b ::
  console.log @ 'JSY rocks!'
else ::
  console.log @ 'JSY is still awesome!'
  
while 0 != q.length ::
  console.log @ q.pop()

``` <td style='vertical-align: top;'>
```javascript
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
</tr></table>

#### `for`

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
for let i = 0; i < 10; i++ ::
  console.log @: i
  
for const val of [1,'two',0xa] ::
  console.log @: val
  
for await const ea of someAsyncGenerator() ::
  console.log @: ea

``` <td style='vertical-align: top;'>
```javascript
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
</tr></table>

#### `try`, `catch`, and `finally`

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
```javascript
try ::
  if 0.5 > Math.random() ::
    throw new Error @ 'Oops!'
catch err ::
  console.error @ err
finally ::
  console.log @ 'Finally.'

``` <td style='vertical-align: top;'>
```javascript
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
</tr></table>

#### `switch`

<table style='width: 100%'><tr><th>JSY</th><th>JavaScript</th></tr><tr><td style='vertical-align: top;'>
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

``` <td style='vertical-align: top;'>
```javascript
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
</tr></table>

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

<table style='width: 100%'><tr><th>JSY</th></tr><tr><td style='vertical-align: top;'>
```javascript
res.data.map @
  personData => @:
    fullName: `${ personData.info.fName } ${ personData.info.lName }`
    dateOfBirth: moment @ personData.info.dob
```
</tr></table>

### Idiom #2 – Arrow Function Compose

<table style='width: 100%'><tr><th>JSY</th></tr><tr><td style='vertical-align: top;'>
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
</tr></table>



## Ecosystem

#### Babel 6.x Transpiler (_stable_)

- [rollup-plugin-jsy-babel](https://github.com/shanewholloway/rollup-plugin-jsy-babel)
  – Babel configuration for using JSY in rollup

- [babel-preset-jsy](https://github.com/shanewholloway/babel-preset-jsy) (_stable_) 
  – Babel 6.x preset for offside-based javascript syntax building on babel-preset-env

- [babel-plugin-offside-js](https://github.com/shanewholloway/babel-plugin-offside-js) (_stable_)
  – Babel 6.x and Babylon offside (indention) Javascript syntax extension.

##### Misc Babel-based Tools

- [jsy-rollup-bundler](https://github.com/shanewholloway/jsy-rollup-bundler)
  – JSY-oriented rollup bundling build chain for Web UI projects.

- [babel-convert-jsy-from-js](https://github.com/shanewholloway/babel-convert-jsy-from-js)
  – Convert JavaScript, Babel or Babylon AST into offside indented JSY formatted source code.

#### Pure JavaScript Transpiler (_alpha_)

- [rollup-plugin-jsy-lite](https://github.com/shanewholloway/rollup-plugin-jsy-lite)
  – Rollup JSY syntax transpiler to standard JavaScript — without Babel

- [jsy-transpile](https://github.com/shanewholloway/jsy-transpile) (_alpha_)
  – Offside (indention) JSY syntax transpiler to standard JavaScript — without Babel

- [jsy-register](https://github.com/shanewholloway/jsy-register) (_prototype_)
  – Register runtime require handler for Offside (indention) JSY syntax transpiler to standard JavaScript.



### Needs

#### Syntax highlighters
Most JavaScript hightlighters work okay, but could certainly be better.

- for [VIM / GVIM](https://www.vim.org/)
- for [highlight.js](https://highlightjs.org/)
- for [CodeMirror](https://codemirror.net/)
- for [Prism](https://prismjs.com/)
- for [Pygments](http://pygments.org/)
- for Atom and VSCode editors – use the babel-based highlighter! It works great.


## Projects using JSY

- [shanewholloway/](https://github.com/shanewholloway) projects
  - Featured:
    - [msg-fabric-core](https://github.com/shanewholloway/msg-fabric-core)
    - [node-fate-observable](https://github.com/shanewholloway/node-fate-observable)
    - [js-evtbus](https://github.com/shanewholloway/js-evtbus)
    - [js-hashbelt](https://github.com/shanewholloway/js-hashbelt)

  - More:
    - [node-shamir-tss-gf256](https://github.com/shanewholloway/node-shamir-tss-gf256)
    - [js-object-functional](https://github.com/shanewholloway/js-object-functional)
    - [js-revitalize-object](https://github.com/shanewholloway/js-revitalize-object)
    - [js-consistent-fnvxor32](https://github.com/shanewholloway/js-consistent-fnvxor32)


## Thanks!

Special thanks to [Robert Sirois](@rpsirois) for making this documentation happen!

Thanks to [Brian Brown](@codecleric) for inspiring, pushing, and supporting JSY's creation.

Thanks to [Brandon Brown](@RB-Bro) and Larry King for intensive use and feedback on JSY.

## Licenses

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">
<img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" />
</a><br/>
Documentation is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

Examples and source code are licensed under [BSD 2-Clause](./LICENSE).

