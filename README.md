# JavaScript Complete Interview Guide

A comprehensive guide covering all essential JavaScript concepts for interview preparation.

---

## 1. Primitive Types & Behavior

### Types & Characteristics
```javascript
typeof 42          // "number"
typeof "hello"     // "string"
typeof true        // "boolean"
typeof undefined   // "undefined"
typeof null        // "object" (historical bug!)
typeof Symbol("x") // "symbol"
typeof 123n        // "bigint"
```

### Why Immutable?
- Stored by **value** in memory (stack)
- Operations create **new values**, don't modify original
- Each variable holds its own copy

```javascript
let a = 5;
let b = a;    // b gets copy of value, not reference
a = 10;       // a changes, b remains 5
console.log(b); // 5
```

---

## 2. Non-Primitive Types & Behavior

### Types & Characteristics
```javascript
typeof []         // "object"
typeof {}         // "object"
typeof function(){} // "function"
typeof new Date() // "object"
```

### Why Mutable?
- Stored by **reference** in memory (heap)
- Variables hold **memory addresses**, not actual values
- Multiple variables can reference same object

```javascript
let obj1 = { name: "John" };
let obj2 = obj1;  // obj2 points to same object
obj1.name = "Jane";
console.log(obj2.name); // "Jane" (both changed!)
```

---

## 3. String: The Special Primitive

### Why Strings Behave Differently?
- **Primitive** but has **methods** like objects
- JavaScript **auto-boxes** strings temporarily

```javascript
let str = "hello";
str.toUpperCase(); // "HELLO" - method call works!
str.length;        // 5 - property access works!

// But still immutable
str[0] = "H";      // Fails silently
console.log(str);  // "hello" (unchanged)

// Behind the scenes:
// new String("hello").toUpperCase() - temporary object created & destroyed
```

---

## 4. Truthy & Falsy Values

### Falsy Values (only 8!)
```javascript
Boolean(false)     // false
Boolean(0)         // false
Boolean(-0)        // false
Boolean(0n)        // false
Boolean("")        // false
Boolean(null)      // false
Boolean(undefined) // false
Boolean(NaN)       // false
```

### Everything Else is Truthy
```javascript
Boolean("0")       // true (string)
Boolean("false")   // true (string)
Boolean([])        // true (empty array)
Boolean({})        // true (empty object)
Boolean(function(){}) // true
```

---

## 5. Variables: var, let, const

| Feature | var | let | const |
|---------|-----|-----|-------|
| **Scope** | Function/Global | Block | Block |
| **Hoisting** | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| **Re-declaration** | Yes | No | No |
| **Re-assignment** | Yes | Yes | No |
| **TDZ** | No | Yes | Yes |

### Scope Examples
```javascript
function example() {
  if (true) {
    var a = 1;    // Function scoped
    let b = 2;    // Block scoped
    const c = 3;  // Block scoped
  }
  console.log(a); // 1 ✓
  console.log(b); // ReferenceError ✗
  console.log(c); // ReferenceError ✗
}
```

### Hoisting Behavior
```javascript
console.log(x); // undefined (not error)
var x = 5;

console.log(y); // ReferenceError (TDZ)
let y = 10;

console.log(z); // ReferenceError (TDZ)
const z = 15;
```

---

# JavaScript Equality & Type Coercion Cheatsheet

A quick reference to understand **`==` vs `===`**, type coercion, and how JavaScript converts values behind the scenes. Useful for interview prep.

---

## 🔑 Golden Rules

- Always prefer **`===` / `!==`** for strict comparison (no coercion).
- Use **explicit conversion** (`Number(x)`, `String(x)`, `Boolean(x)`) when needed.
- `==` (abstract equality) follows well-defined coercion rules.

---

## 1. Abstract Equality (`==`) Rules

When comparing `a == b`:

### 1. Same type → compare directly
- Numbers: `0 == -0 → true`, `NaN == NaN → false`
- Strings: `"abc" == "abc" → true`
- Booleans: `true == true → true`
- Objects: equal only if **same reference**

### 2. Boolean with anything → convert Boolean → Number
```javascript
true == 1   // true
false == 0  // true
```

### 3. String with Number → convert String → Number
```javascript
"42" == 42   // true
"  " == 0    // true (whitespace → "" → 0)
```

### 4. Object with Primitive → ToPrimitive(hint="number")
Calls `valueOf()`, if not primitive → calls `toString()`.

```javascript
[] == 0      // true  ([] → "" → 0)
[42] == 42   // true  ([42] → "42" → 42)
[1,2] == "1,2" // true
{} == {}     // false (different references)
```

### 5. Null & Undefined (special case)
```javascript
null == null        // true
undefined == null   // true
null == 0           // false
undefined == 0      // false
```

### 6. BigInt with Number
Compare after conversion if exact.

```javascript
1n == 1    // true
1n == 1.5  // false
```

### 7. Symbols
Only equal if same reference.

```javascript
Symbol("a") == Symbol("a")  // false
const x = Symbol("a")
x == x   // true
```

---

## 2. Strict Equality (`===`)

- No type coercion.
- Types must match.

```javascript
0 === false   // false
"42" === 42   // false
[] === []     // false
```

---

## 3. ToNumber Conversion (used in coercion)

```javascript
Number("")        // 0
Number("   ")     // 0
Number("123")     // 123
Number("2a")      // NaN
Number(null)      // 0
Number(undefined) // NaN
Number(true)      // 1
Number(false)     // 0
Number([])        // 0   ([] → "" → 0)
Number([1])       // 1   ([1] → "1" → 1)
Number([1,2])     // NaN ([1,2] → "1,2")
Number({})        // NaN ({} → "[object Object]")
```

---

## 4. Common Interview Gotchas

```javascript
[] == true        // false   ([] → "" → 0 vs 1)
[] == 0           // true    ([] → "" → 0)
[] == {}          // false   (different refs)
" " == 0          // true    (" " → "" → 0)
" " == true       // false   (" " → 0 vs 1)
true == 1         // true
"" == "0"         // false
"    " == " "     // false   (four spaces ≠ one space)
NaN == NaN        // false
null == undefined // true
```

---

## 5. Unary Plus (`+x`)

The unary `+` converts its operand to a number.

```javascript
+"2"     // 2
+true    // 1
+false   // 0
+""      // 0
+[]      // 0
+{}      // NaN
```

### Examples:
```javascript
1 + +"2"              // 3
1 + +"2" + +"3" + 2   // 8
1 + +"2" + 3 + +4     // 10
```

---

## 6. Quick Comparison Table

| Expression | Result | Why |
|------------|--------|-----|
| `true == 1` | `true` | Boolean → number |
| `false == 0` | `true` | Boolean → number |
| `"42" == 42` | `true` | String → number |
| `[] == 0` | `true` | [] → "" → 0 |
| `[42] == 42` | `true` | [42] → "42" → 42 |
| `[1,2] == "1,2"` | `true` | Array → string |
| `{} == {}` | `false` | Different references |
| `null == undefined` | `true` | Special case in spec |
| `null == 0` | `false` | Null only equals null/undefined |
| `NaN == NaN` | `false` | NaN not equal to anything |
| `Symbol("a")=="a"` | `error` | Symbols not coerced to primitives |

---

## ✅ Best Practices

- Use `===` for comparisons.
- Explicitly convert values before comparing.
- Avoid tricky coercions in production — but know them for interviews.
- Use `Object.is()` when dealing with `NaN` and `-0`.

```javascript
Object.is(NaN, NaN)   // true
Object.is(-0, 0)      // false
```

---

## 7. Logical Operators (`&&`, `||`, `!`)

### Logical AND (`&&`)
Returns the **first falsy value** or the **last value** if all are truthy.

```javascript
// Falsy values: false, 0, "", null, undefined, NaN
true && "hello"     // "hello"
"hello" && "world"  // "world"
"hello" && 0        // 0
0 && "hello"        // 0
null && "hello"     // null
"a" && "b" && "c"   // "c"
```

### Logical OR (`||`)
Returns the **first truthy value** or the **last value** if all are falsy.

```javascript
false || "hello"    // "hello"
0 || "default"      // "default"
"" || "fallback"    // "fallback"
null || undefined   // undefined
"first" || "second" // "first"
0 || false || ""    // "" (last falsy)
```

### Logical NOT (`!`)
Converts to boolean and negates. Double `!!` converts to boolean.

```javascript
!true        // false
!0           // true
!"hello"     // false
!""          // true
!null        // true
!undefined   // true
!NaN         // true

// Double negation for boolean conversion
!!""         // false
!!"hello"    // true
!!0          // false
!!42         // true
!![]         // true (arrays are truthy)
!!{}         // true (objects are truthy)
```

### Common Patterns & Gotchas

#### Default Values
```javascript
let name = user.name || "Anonymous";
let count = user.count || 0;  // ⚠️ Careful: count could be 0!

// Better with nullish coalescing (??)
let count = user.count ?? 0;  // Only null/undefined → 0
```

#### Truthiness Table
| Value | Boolean | `!value` | `!!value` |
|-------|---------|----------|-----------|
| `true` | `true` | `false` | `true` |
| `false` | `false` | `true` | `false` |
| `0` | `false` | `true` | `false` |
| `1` | `true` | `false` | `true` |
| `""` | `false` | `true` | `false` |
| `"hello"` | `true` | `false` | `true` |
| `null` | `false` | `true` | `false` |
| `undefined` | `false` | `true` | `false` |
| `NaN` | `false` | `true` | `false` |
| `[]` | `true` | `false` | `true` |
| `{}` | `true` | `false` | `true` |

#### Interview Traps
```javascript
// What do these return?
0 && 5           // 0 (first falsy)
5 && 0           // 0 (last value, which is falsy)
"" || 0 || null  // null (last falsy)
"a" || "b"       // "a" (first truthy)
!"" && "yes"     // "yes" (true && "yes")
![] && "no"      // false ([] is truthy, ![] is false)

// Tricky ones
[] && {}         // {} (both truthy, returns last)
{} || []         // {} (first truthy)
!!"false"        // true (non-empty string)
!!""             // false (empty string)
```

---

# JavaScript Operator Comparison Matrix

## Complete Operator Behavior by Data Type

| **Type** | `==` loose | `===` strict | `<` / `>` | `+` | `-` | `*` | `/` | `&&` | `||` | `!` | `typeof` |
|----------|------------|--------------|-----------|-----|-----|-----|-----|------|------|-----|----------|
| **Number (5)** | `5 == "5"` → `true` | `5 === "5"` → `false` | `5 < 10` → `true` | `5 + "5"` → `"55"` | `5 - "2"` → `3` | `5 * "2"` → `10` | `5 / "2"` → `2.5` | `5 && 10` → `10` | `0 || 10` → `10` | `!5` → `false` | `"number"` |
| **String ("5")** | `"5" == 5` → `true` | `"5" === 5` → `false` | `"5" < "10"` → `false`* | `"5" + 5` → `"55"` | `"5" - 2` → `3` | `"5" * 2` → `10` | `"5" / 2` → `2.5` | `"a" && "b"` → `"b"` | `"" || "a"` → `"a"` | `!"abc"` → `false` | `"string"` |
| **Boolean (true)** | `true == 1` → `true` | `true === 1` → `false` | `true < 2` → `true` | `true + 1` → `2` | `true - 1` → `0` | `true * 2` → `2` | `true / 2` → `0.5` | `true && false` → `false` | `false || true` → `true` | `!true` → `false` | `"boolean"` |
| **Null** | `null == undefined` → `true` | `null === undefined` → `false` | `null < 1` → `true` | `null + 1` → `1` | `null - 1` → `-1` | `null * 2` → `0` | `null / 2` → `0` | `null && 1` → `null` | `null || 1` → `1` | `!null` → `true` | `"object"` |
| **Undefined** | `undefined == null` → `true` | `undefined === null` → `false` | `undefined < 1` → `false` | `undefined + 1` → `NaN` | `undefined - 1` → `NaN` | `undefined * 2` → `NaN` | `undefined / 2` → `NaN` | `undefined && 1` → `undefined` | `undefined || 1` → `1` | `!undefined` → `true` | `"undefined"` |
| **Array ([])** | `[] == ""` → `true` | `[] === ""` → `false` | `[2] < [10]` → `false`* | `[1] + [2]` → `"12"` | `[10] - 1` → `9` | `[5] * 2` → `10` | `[10] / 2` → `5` | `[] && "abc"` → `"abc"` | `[] || "abc"` → `[]` | `![]` → `false` | `"object"` |
| **Array ([1])** | `[1] == "1"` → `true` | `[1] === "1"` → `false` | `[2] < 3` → `true` | `[1] + 2` → `"12"` | `[5] - 1` → `4` | `[3] * 2` → `6` | `[8] / 2` → `4` | `[1] && [2]` → `[2]` | `[1] || [2]` → `[1]` | `![1]` → `false` | `"object"` |
| **Object ({})** | `{} == {}` → `false`** | `{} === {}` → `false`** | `{} < 5` → `false` | `{} + {}` → `"[object Object][object Object]"` | `{} - 1` → `NaN` | `{} * 2` → `NaN` | `{} / 2` → `NaN` | `{} && "a"` → `"a"` | `{} || "a"` → `{}` | `!{}` → `false` | `"object"` |
| **Function** | `fn == fn2` → `false`** | `fn === fn2` → `false`** | `fn < 5` → `false` | `fn + ""` → `"function(){...}"` | `fn - 1` → `NaN` | `fn * 2` → `NaN` | `fn / 2` → `NaN` | `fn && "a"` → `"a"` | `fn || "a"` → `fn` | `!fn` → `false` | `"function"` |
| **NaN** | `NaN == NaN` → `false` | `NaN === NaN` → `false` | `NaN < 5` → `false` | `NaN + 1` → `NaN` | `NaN - 1` → `NaN` | `NaN * 2` → `NaN` | `NaN / 2` → `NaN` | `NaN && 1` → `NaN` | `NaN || 1` → `1` | `!NaN` → `true` | `"number"` |

**Notes:**
- `*` String comparison is lexicographic: `"5" < "10"` compares as `"5"[0] vs "1"[0]` → `"5" > "1"`
- `**` Objects/Functions are equal only if same reference: `let a = {}; let b = a; a === b` → `true`

---

## Operator Conversion Rules

### Type Coercion Priority
1. **Addition (`+`)**: If either operand is string → string concatenation
2. **Other math (`-`, `*`, `/`)**: Both operands → numbers
3. **Comparison (`<`, `>`)**: Both operands → numbers (except string vs string)
4. **Logical (`&&`, `||`)**: Uses truthiness, returns actual values
5. **Equality (`==`)**: Complex coercion rules (see main guide)

### Quick Reference: ToNumber Conversion
| Value | ToNumber Result |
|-------|----------------|
| `""` (empty string) | `0` |
| `"123"` | `123` |
| `"abc"` | `NaN` |
| `true` | `1` |
| `false` | `0` |
| `null` | `0` |
| `undefined` | `NaN` |
| `[]` | `0` |
| `[5]` | `5` |
| `[1,2]` | `NaN` |
| `{}` | `NaN` |

### Quick Reference: ToString Conversion
| Value | ToString Result |
|-------|----------------|
| `5` | `"5"` |
| `true` | `"true"` |
| `null` | `"null"` |
| `undefined` | `"undefined"` |
| `[]` | `""` |
| `[1,2,3]` | `"1,2,3"` |
| `{}` | `"[object Object]"` |
| `function f(){}` | `"function f(){}"` |

---

## Logical Operators Detailed

### AND (`&&`) - Returns first falsy or last value
| Left | Right | Result | Explanation |
|------|-------|--------|-------------|
| `0` | `"hello"` | `0` | First falsy |
| `"hello"` | `0` | `0` | Last value (falsy) |
| `"hello"` | `"world"` | `"world"` | Last value (both truthy) |
| `null` | `"test"` | `null` | First falsy |
| `[]` | `{}` | `{}` | Last value (both truthy) |

### OR (`||`) - Returns first truthy or last value
| Left | Right | Result | Explanation |
|------|-------|--------|-------------|
| `0` | `"hello"` | `"hello"` | First truthy |
| `""` | `"world"` | `"world"` | First truthy |
| `null` | `undefined` | `undefined` | Last value (both falsy) |
| `"first"` | `"second"` | `"first"` | First truthy |
| `[]` | `{}` | `[]` | First truthy |

---

## Common Interview Traps

### Tricky Comparisons
```javascript
// These are often asked in interviews:
[] == ![]          // true  ([] == false → [] == 0 → "" == 0 → true)
"" == 0            // true  ("" → 0)
" " == 0           // true  (" " → "" → 0)
null == 0          // false (special case)
undefined == 0     // false (special case)
[1] == [1]         // false (different objects)
{} == {}           // false (different objects)
```

### Operator Precedence Traps
```javascript
3 + 4 * 5          // 23 (not 35)
true + false       // 1  (1 + 0)
"5" - 3            // 2  ("5" → 5)
"5" + 3            // "53" (concatenation wins)
+"5" + 3           // 8  (unary + converts to number first)
```

### Mixed Type Operations
```javascript
[] + []            // ""   (empty string)
[] + {}            // "[object Object]"
{} + []            // 0    (in some contexts, {} seen as block)
({}) + []          // "[object Object]"
![] + []           // "false"
```

---

## 6. Arrays & Popular Methods

### Creation & Basic Operations
```javascript
let arr = [1, 2, 3];
arr.length;     // 3
arr[0];         // 1
```

### Essential Methods

#### `push()` - Add to end
```javascript
arr.push(4);    // Returns new length: 4
// arr = [1, 2, 3, 4]
```

#### `pop()` - Remove from end
```javascript
let last = arr.pop();  // Returns removed element: 4
// arr = [1, 2, 3]
```

#### `shift()` - Remove from start
```javascript
let first = arr.shift();  // Returns removed element: 1
// arr = [2, 3]
```

#### `unshift()` - Add to start
```javascript
arr.unshift(0);  // Returns new length: 3
// arr = [0, 2, 3]
```

#### `splice()` - Add/remove at any position
```javascript
arr.splice(1, 1, 'new');  // Returns removed elements: [2]
// arr = [0, 'new', 3]
```

#### `slice()` - Extract portion (immutable)
```javascript
let copy = arr.slice(0, 2);  // Returns [0, 'new']
// Original arr unchanged
```

#### `indexOf()` - Find element index
```javascript
arr.indexOf('new');  // Returns 1 (index of 'new')
```

#### `includes()` - Check if element exists
```javascript
arr.includes(3);     // Returns true
```

---

## 7. Loops: for & while

### `for` Loop - When you know iteration count
```javascript
// Use when: iterating arrays, known repetitions
for (let i = 0; i < 5; i++) {
  console.log(i);  // 0, 1, 2, 3, 4
}

// Why: Counter-controlled, predictable iterations
```

### `while` Loop - When condition-based
```javascript
// Use when: unknown iterations, condition-based
let count = 0;
while (count < 3) {
  console.log(count++);  // 0, 1, 2
}

// Why: Condition-controlled, dynamic stopping
```

---

## 8. for...of vs for...in

### `for...of` - Iterate VALUES
```javascript
let arr = ['a', 'b', 'c'];
for (let value of arr) {
  console.log(value);  // 'a', 'b', 'c'
}

// Use for: Arrays, Strings, Sets, Maps (iterables)
// Why: Clean syntax, gets actual values
```

### `for...in` - Iterate KEYS/PROPERTIES
```javascript
let obj = {a: 1, b: 2, c: 3};
for (let key in obj) {
  console.log(key);    // 'a', 'b', 'c'
  console.log(obj[key]); // 1, 2, 3
}

// Use for: Objects, Arrays (indices)
// Why: Access keys/properties, includes inherited properties
```

### ⚠️ Common Trap
```javascript
let arr = ['x', 'y', 'z'];
for (let item in arr) {
  console.log(item);   // '0', '1', '2' (indices!)
}
for (let item of arr) {
  console.log(item);   // 'x', 'y', 'z' (values!)
}
```

---

## 9. Functions vs Arrow Functions

### Regular Functions
```javascript
function regular() {
  return this;  // Dynamic 'this' binding
}

let obj = {
  method: regular,
  arrow: () => this  // Lexical 'this' binding
};
```

### Key Differences

| Feature | Regular Function | Arrow Function |
|---------|------------------|----------------|
| **`this` binding** | Dynamic (call-time) | Lexical (definition-time) |
| **`arguments`** | Has arguments object | No arguments object |
| **Hoisting** | Fully hoisted | Not hoisted |
| **Constructor** | Can be constructor | Cannot be constructor |
| **Method definition** | Good for methods | Avoid for methods |

### When to Use Arrow Functions
- **Callbacks**: `arr.map(x => x * 2)`
- **Short functions**: `const add = (a, b) => a + b`
- **Preserving `this`**: Inside event handlers, timers

### When to Use Regular Functions
- **Object methods**: Need dynamic `this`
- **Constructors**: `new` keyword usage
- **Function hoisting**: Call before declaration

---

## 10. Array Iteration Methods

### `map()` - Transform each element
```javascript
let nums = [1, 2, 3];
let doubled = nums.map(x => x**2);  // [1, 4, 9]
// Returns: New array with transformed elements
```

### `forEach()` - Execute function for each element
```javascript
nums.forEach(x => console.log(x));   // Prints: 1, 2, 3
// Returns: undefined (side effects only)

let ans = arr.forEach(a => a**2)
console.log(ans)  //  Returns: undefined
// .forEach() is meant for side effects (like logging, updating something), not for building a new array. That’s why your ans is undefined
```

### `filter()` - Keep elements that pass test
```javascript
let evens = nums.filter(x => x % 2 === 0);  // [2]
// Returns: New array with filtered elements

let res = arr.filter(a => a-5)
console.log(res)
// .filter() expects your callback to return a boolean (true → keep the element, false → drop it).
```

### `reduce()` - Reduce to single value
```javascript
let sum = nums.reduce((acc, x) => acc + x, 0);  // 6
// Returns: Accumulated single value
```

### Key Differences
- **`map`**: 1-to-1 transformation, returns new array
- **`forEach`**: Side effects, returns undefined
- **`filter`**: Conditional selection, returns new array
- **`reduce`**: Many-to-one, returns single value

---

## 11. Shallow Copy vs Deep Copy

### Shallow Copy
```javascript
// Methods: Object.assign(), spread operator, Array.from()
let original = { a: 1, b: { c: 2 } };

let shallow = { ...original };
shallow.a = 10;      // ✓ Independent
shallow.b.c = 20;    // ✗ Affects original!

console.log(original.b.c); // 20 (changed!)
```

### Deep Copy
```javascript
// Method: JSON.parse(JSON.stringify()) - limited
let deep = JSON.parse(JSON.stringify(original));
deep.b.c = 30;       // ✓ Completely independent

// Limitations: loses functions, undefined, symbols, dates
```

### Pass by Value vs Reference
```javascript
// Primitives: Pass by value
let x = 5;
function changeValue(val) {
  val = 10;  // Only changes local copy
}
changeValue(x);
console.log(x); // 5 (unchanged)

// Objects: Pass by reference
let obj = { name: "John" };
function changeObject(o) {
  o.name = "Jane";  // Changes original object
}
changeObject(obj);
console.log(obj.name); // "Jane" (changed!)
```

---

## 12. Scope Types

### Variable Scope (var)
```javascript
var globalVar = "global";

function example() {
  var functionVar = "function";
  console.log(globalVar);    // ✓ Accessible
  console.log(functionVar);  // ✓ Accessible
}
```

### Block Scope (let, const)
```javascript
{
  let blockScoped = "block";
  const alsoBlock = "block";
}
console.log(blockScoped); // ReferenceError
```

### Function Scope
```javascript
function outer() {
  let outerVar = "outer";
  
  function inner() {
    console.log(outerVar); // ✓ Can access parent scope
  }
  inner();
}
```

### Lexical Scope (Closures)
```javascript
function createCounter() {
  let count = 0;
  return function() {
    return ++count;  // Remembers 'count' from outer scope
  };
}

let counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

---

## 13. Execution Context & Hoisting

### Creation Phase
1. **Memory allocation** for variables and functions
2. **`var`** variables initialized with `undefined`
3. **`let/const`** variables allocated but uninitialized (TDZ)
4. **Function declarations** fully hoisted

### Execution Phase
1. **Line-by-line execution**
2. **Variable assignment** happens here
3. **Function calls** create new execution contexts

### Hoisting Examples
```javascript
// What happens during creation phase:
console.log(a); // undefined (not error)
console.log(b); // ReferenceError (TDZ)
console.log(sayHi()); // "Hi!" (function hoisted)

var a = 5;
let b = 10;

function sayHi() {
  return "Hi!";
}

// Function expressions are NOT fully hoisted
console.log(sayBye()); // TypeError: sayBye is not a function
var sayBye = function() {
  return "Bye!";
};
```

### Temporal Dead Zone (TDZ)
```javascript
console.log(x); // ReferenceError: Cannot access 'x' before initialization
let x = 5;

// TDZ exists from start of block until declaration
{
  // TDZ starts here for 'y'
  console.log(y); // ReferenceError
  let y = 10;     // TDZ ends here
}
```

---

## 14. Additional Essential Topics

### Event Loop & Asynchronous JavaScript
```javascript
console.log(1);
setTimeout(() => console.log(2), 0);
console.log(3);
// Output: 1, 3, 2 (async callback queued)
```

### Promises & async/await
```javascript
// Promise
fetch('/api').then(data => console.log(data));

// Async/await
async function getData() {
  const data = await fetch('/api');
  console.log(data);
}
```

### Destructuring
```javascript
// Array destructuring
let [a, b] = [1, 2];

// Object destructuring
let {name, age} = {name: "John", age: 30};
```

### Template Literals
```javascript
let name = "John";
let message = `Hello, ${name}!`;  // "Hello, John!"
```

### Rest & Spread Operators
```javascript
// Rest - collect elements
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b);
}

// Spread - expand elements
let arr1 = [1, 2];
let arr2 = [...arr1, 3, 4];  // [1, 2, 3, 4]
```

---
