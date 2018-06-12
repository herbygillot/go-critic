[//]: # (This is generated file, please don't edit it yourself.)

## Overview

Go source code linter that brings checks that are currently not implemented in other linters.

## Checkers:

<table>
  <tr>
    <th>Name</th>
  </tr>
      <tr>
        <td><a href="#appendCombine-ref">appendCombine</a></td>
      </tr>
      <tr>
        <td><a href="#builtinShadow-ref">builtinShadow</a></td>
      </tr>
      <tr>
        <td><a href="#comments-ref">comments</a></td>
      </tr>
      <tr>
        <td><a href="#elseif-ref">elseif</a></td>
      </tr>
      <tr>
        <td><a href="#flagDeref-ref">flagDeref</a></td>
      </tr>
      <tr>
        <td><a href="#paramDuplication-ref">paramDuplication</a></td>
      </tr>
      <tr>
        <td><a href="#paramName-ref">paramName</a></td>
      </tr>
      <tr>
        <td><a href="#parenthesis-ref">parenthesis</a></td>
      </tr>
      <tr>
        <td><a href="#ptrToRefParam-ref">ptrToRefParam</a></td>
      </tr>
      <tr>
        <td><a href="#rangeExprCopy-ref">rangeExprCopy</a></td>
      </tr>
      <tr>
        <td><a href="#rangeValCopy-ref">rangeValCopy</a></td>
      </tr>
      <tr>
        <td><a href="#stddef-ref">stddef</a></td>
      </tr>
      <tr>
        <td><a href="#switchTrue-ref">switchTrue</a></td>
      </tr>
      <tr>
        <td><a href="#switchif-ref">switchif</a></td>
      </tr>
      <tr>
        <td><a href="#typeGuard-ref">typeGuard</a></td>
      </tr>
      <tr>
        <td><a href="#underef-ref">underef</a></td>
      </tr>
      <tr>
        <td><a href="#unexportedCall-ref">unexportedCall</a></td>
      </tr>
      <tr>
        <td><a href="#unslice-ref">unslice</a></td>
      </tr>
</table>

**Experimental:**

<table>
  <tr>
    <th>Name</th>
  </tr>
      <tr>
        <td><a href="#longChain-ref">longChain</a></td>
      </tr>
</table>





<a name="appendCombine-ref"></a>
## appendCombine
Detects `append` chains to the same slice that can be done in a single `append` call.

**Before:**
```go
xs = append(xs, 1)
xs = append(xs, 2)
```

**After:**
```go
xs = append(xs, 1, 2)
```




<a name="builtinShadow-ref"></a>
## builtinShadow
Detects when
[predeclared identifiers](https://golang.org/ref/spec#Predeclared_identifiers)
shadowed in assignments.

**Before:**
```go
func main() {
    // shadowing len function
    len := 10
    println(len)
}
```

**After:**
```go
func main() {
    // change identificator name
    length := 10
    println(length)
}
```




<a name="comments-ref"></a>
## comments
Detects comments that aim to silence go lint complaints about exported symbol not having a doc-comment.

**Before:**
```go
// Foo ...
func Foo() {
     ...
}

```

**After:**
```go
// Foo useful comment for Foo 
func Foo() {
     ...
}

```




<a name="elseif-ref"></a>
## elseif
Detects repeated if-else statements and suggests to replace them with switch statement.

Permits single else or else-if; repeated else-if or else + else-if
will trigger suggestion to use switch statement.

**Before:**
```go
if cond1 {
	// Code A.
} else if cond2 {
	// Code B.
} else {
	// Code C.
}
```

**After:**
```go
switch {
case cond1:
	// Code A.
case cond2:
	// Code B.
default:
	// Code C.
}
```




<a name="flagDeref-ref"></a>
## flagDeref
Detects immediate dereferencing of `flag` package pointers.
Suggests using `XxxVar` functions to achieve desired effect.

**Before:**
```go
b := *flag.Bool("b", false, "b docs")
```

**After:**
```go
var b bool
flag.BoolVar(&b, "b", false, "b docs")
```

> Dereferencing returned pointers will lead to hard to find errors
> where flag values are not updated after flag.Parse().






<a name="paramDuplication-ref"></a>
## paramDuplication
Detects if function parameters could be combined by type and suggest the way to do it.

**Before:**
```go
func foo(a, b int, c, d int, e, f int, g int) {}
```

**After:**
```go
func foo(a, b, c, d, e, f, g int) {}
```




<a name="paramName-ref"></a>
## paramName
Detects potential issues in function parameter names.

Catches capitalized (exported) parameter names.
Suggests to replace them with non-capitalized versions.

**Before:**
```go
func f(IN int, OUT *int) (ERR error) {}
```

**After:**
```go
func f(in int, out *int) (err error) {}
```




<a name="parenthesis-ref"></a>
## parenthesis
Detects unneded parenthesis inside type expressions and suggests to remove them.

**Before:**
```go
func foo() [](func([](func()))) {
     ...
}
```

**After:**
```go
func foo() []func([]func()) {
     ...
}
```





<a name="ptrToRefParam-ref"></a>
## ptrToRefParam
Detects input and output parameters that have a type of pointer to referential type.

**Before:**
```go
func f(m *map[string]int) (ch *chan *int)
```

**After:**
```go
func f(m map[string]int) (ch chan *int)
```

> Slices are not as referential as maps or channels, but it's usually
> better to return them by value rather than modyfing them by pointer.




<a name="rangeExprCopy-ref"></a>
## rangeExprCopy
Detects `for` statements with range expressions that perform excessive
copying (big arrays can cause it).

Suggests to use pointer to array to avoid the copy using `&` on range expression.

**Before:**
```go
var xs [256]byte
for _, x := range xs {
	// Loop body.
}
```

**After:**
```go
var xs [256]byte
for _, x := range &xs {
	// Loop body.
}
```




<a name="rangeValCopy-ref"></a>
## rangeValCopy
Detects loops that copy big objects during each iteration.
Suggests to use index access or take address and make use pointer instead.

**Before:**
```go
xs := make([][1024]byte, length)
for _, x := range xs {
	// Loop body.
}
```

**After:**
```go
xs := make([][1024]byte, length)
for i := range xs {
	x := &xs[i]
	// Loop body.
}
```




<a name="stddef-ref"></a>
## stddef
Detects constant expressions that can be replaced by a named constant
from standard library, like `math.MaxInt32`.

**Before:**
```go
intBytes := make([]byte, unsafe.Sizeof(0))
maxVal := 1<<7 - 1
```

**After:**
```go
intBytes := make([]byte, bits.IntSize)
maxVal := math.MaxInt8
```




<a name="switchTrue-ref"></a>
## switchTrue
Detects switch-over-bool statements that use explicit `true` tag value.

**Before:**
```go
switch true {
case x > y:
	// ...
}
```

**After:**
```go
switch {
case x > y:
	// ...
}
```




<a name="switchif-ref"></a>
## switchif
Detects switch statements that could be better written as if statements.

**Before:**
```go
switch x := x.(type) {
case int:
     ...
}
```

**After:**
```go
if x, ok := x.(int); ok {
   ...
}
```




<a name="typeGuard-ref"></a>
## typeGuard
Detects type switches that cab benefit from type guard clause.

**Before:**
```go
switch v.(type) {
case int:
	return v.(int)
case point:
	return v.(point).x + v.(point).y
default:
	return 0
}
```

**After:**
```go
switch v := v.(type) {
case int:
	return v
case point:
	return v.x + v.y
default:
	return 0
}
```




<a name="underef-ref"></a>
## underef
Detects expressions with C style field selection and suggest Go style correction.

**Before:**
```go
(*k).field = 5
_ := (*a)[5] // only if a is array
```

**After:**
```go
k.field = 5
_ := a[5]
```




<a name="unexportedCall-ref"></a>
## unexportedCall
Finds calls of unexported method from unexported type outside that type.

**Before:**
```go
type foo struct{}

func (f foo) bar() int { return 1 }
func baz() {
	var fo foo
	fo.bar()
}

```

**After:**
```go
type foo struct{}

func (f foo) Bar() int { return 1 } // now Bar is exported
func baz() {
	var fo foo
	fo.Bar()
}
```




<a name="unslice-ref"></a>
## unslice
Detects slice expressions that can be simplified to sliced expression itself.

**Before:**
```go
f(s[:]) // s is string
copy(b[:], values...) // b is []byte
```

**After:**
```go
f(s)
copy(b, values...)
```
















<a name="longChain-ref"></a>
## longChain
Detects repeated expression chains and suggest to refactor them.

**Before:**
```go
a := q.w.e.r.t + 1
b := q.w.e.r.t + 2
c := q.w.e.r.t + 3
v := (a+xs[i+1]) + (b+xs[i+1]) + (c+xs[i+1])
```

**After**
```go
x := xs[i+1]
qwert := q.w.e.r.t
a := qwert + 1
b := qwert + 2
c := qwert + 3
v := (a+x) + (b+x) + (c+x)
```

**Experimental**

Gives false-positives for:
* Cases with re-assignment. See `$GOROOT/src/crypto/md5/md5block.go` for example.



























