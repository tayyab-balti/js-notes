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

#### Short-circuit Evaluation
```javascript
user && user.name            // Safe property access
isLoggedIn && redirectHome() // Conditional execution
error && console.log(error)  // Log only if error exists
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
