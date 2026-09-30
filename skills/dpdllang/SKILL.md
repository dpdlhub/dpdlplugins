# SKILL.md

```markdown
---
name: dpdl-lang
description: >
  Comprehensive skill for writing, reading, and reasoning about Dpdl (dpdl-lang),
  a general-purpose, interpreted, self-contained programming language with
  JVM bytecode compilation, embedded multi-language code sections, and JRE/native
  library interop. Use this skill whenever the user works with `.dpdl` / `.h` Dpdl
  scripts, mentions "Dpdl", "dpdl-lang", DpdlEngine, embedded `>>python` / `>>c` /
  `>>java` sections, or asks to convert/port code to Dpdl.
license: SEE Solutions / Dpdl-io (see https://www.dpdl.io)
---

# Dpdl Language Skill

## Overview

**Dpdl (dpdl-lang)** is a general-purpose, self-contained, interpreted programming language
developed by **SEE Solutions** (www.dpdl.io). It supports dynamic **JVM bytecode** compilation,
is statically and dynamically typed, has a compact memory footprint, and is portable across
most platforms. It uniquely allows **embedding other programming languages** (C, C++, Python,
MicroPython, Julia, JavaScript, Lua, Ruby, Java, PHP, Perl, Groovy, V, Clojure, Wgsl, OpenCL)
directly inside Dpdl code, executed at native speed via **Dpdl language plug-ins** — no extra
installations required.

When to use this skill:
- User asks to write, debug, explain, or refactor Dpdl code.
- User asks about Dpdl types, loops, classes, structs, unions, enums, pointers, threads.
- User asks about embedding C/Python/Java/etc. inside Dpdl.
- User asks to interoperate Dpdl with JRE classes or native C libraries.
- User asks about DpdlEngine, Dpdl API, or Dpdl C API.

## Program Structure

A Dpdl program is either:
1. A flat script that executes top-to-bottom (no `main`), or
2. A script with a `main(args[])` entry point.

```python
println("this line will be printed")
```

```python
func main(args[])
	println("args: " + args + " type: " + typeof(args) + " size: " + args.size())
end
```

## Types

Supported types:
`int` `short` `float` `double` `long` `byte` `string` `char` `bool` `var` `const`
`object` `class` `struct` `union` `enum` `(tuples...)`

Suffixes for literals:
- int → no suffix
- float → no suffix or `f`
- double → `d`
- long → `L`
- short → `s`
- byte → `B`

Hexadecimal: `0x1627F` (int), `0x01B` (byte), `0x...L` (long).
Scientific notation: `1.2345E3d`, `-1.2345e3`.

Examples:

```c++
int i = 1
int ih = 0x67452301
short s = 10s
float f1 = 0.1
float f2 = 0.1f
double d = 10000.0d
long l = 1000L
byte b = 1B
string s = "mystr"
char c = 'a'
bool t = true
var v = "inferred at runtime"
const int x = 10
```

### Strings
- Single `'...'` or double `"..."` quotes both valid.
- Multi-line strings enclosed with `"'` … `"'`.
- UTF-16 Unicode supported.
- Interpolation: `$var` and `${expression}`:

```python
int x = 10
println("x is $x, sum is ${x + 5}")
```

### Inferred types
- `var` — mutable, type inferred at runtime.
- `const` — immutable; re-assignment raises an error.

### `null`
Assignable to any type. `string`, `var`, `object` default to `null` when declared without assignment.

### `typedef`

```c++
typedef byte i8
typedef int i32
```

## Arrays

### Primitive arrays (fixed size)

```c++
int myiarr[32]
int myiarr[] = {23, 369}
var myvarr[] = {1, "str", someObj}
```
Pass to functions as `var` parameter.

### Dynamic arrays (grow/shrink, mixed types)

```python
myarrmix[] = [1, 0.3, 23.0d, 1000L, 0x09B, "mega"]
arr[] = "1 2 3 4 5"
arr[] = "1,2,3,4,5"
arr[] = "1;2;3;4;5"
```
Dynamic arrays expose all `java.util.ArrayList` methods.

## Functions

```go
func myfunction(string s, int x, float y, object o, ...)
	...
end
```

Return type (optional): `func myF() int … end` or `func myF() void … end`.
Multi-value returns: `return (1, "a", c)`, access via `mv.0`, `mv.1`, …

## Control Flow

```python
if(cond)
	...
elseif
	...
else
	...
fi

for(<expr>)
	...
endfor

while(<expr>)
	...
endwhile
```

Set loops:

```python
for(n in [1, 2, 3])
	...
endfor

for(x in range(1, 1000))
	...
endfor

for(i in list(1, 2, 3))
	...
endfor

for(k, v in map(1::"A", 2::"B"))
	...
endfor
```

## Operators

- Arithmetic: `+ - * / %` (multiplication requires spaces: `1 * 2`)
- Logical: `&& || !`
- Bitwise: `& | ^ ~ << >> >>>`
- Comparison: `> < >= <= == !=`

## Data Function Types

```python
object my_arr   = arr("multi", 1, 2, 3)
object my_vec   = vec(1, 2, 3, "el")
object my_map   = map("a"::1, "b"::2)
object my_list  = list("A", 1, 0.3)
object my_stack = stack()
```

## `class`

```python
class A {
	int id = 1
	string str

	func A(int id_, string str_)
		id = id_
		str = str_
	end

	func printit()
		println("id: " + id)
	end
}

class A mya(23, "test")
mya.printit()
```

- Instantiate with or without constructor: `class A a` vs `class A a(…)`.
- Inheritance: `class B : A { … }` or `class B extends A { … }`.
- Override functions; call super constructor via `super(...)`.
- Java interop: `object o = genObjCode(myA)`; or derive from Java class via `refObj("String")`.
- Embedded java methods via `>>java ... <<` inside class; only the first embedded section is compiled.

## `struct`

```c
struct myStruct {
	int x = 10
	string s = "Test"
	object so = new("String", "obj")
	func myStructCall() ... end
}

struct myStruct a
struct A mya = {23, 0.3, 0.6, "Test", d}
struct A mya1 = {id: 23, x: 0.3, data: d}   # designated initializers
```

Features: inheritance (`struct B : A`), `typedef struct` (instantiate via `new`), compiled to
bytecode via `genObjCode(..)`, C-compatible via `genObjCodeC(..)`.


### **`typedef struct`**


The dpdl type **`struct`** can also be used in conjunction with the **`typedef`** specifier to create a type that can than be instantiated via the *`new`* keyword.


#### **`typedef struct`** instance

```c++

typedef struct A {
	char id[256]
	int a
	int b
	int c
}

char myid[] = {'D', 'P', 'D', 'L'}

object myaobj = new A{myid, 1, 2, 3}

println("myaobj: " + myaobj)
```


#### **`typedef struct`** instance via a *Alias*


```c++

typedef struct B {
	int x
	int y
	float z
} Point


Point point = new Point(100, 200, 33.3f)

println("point: " + point)

```

Or also

```c++

typedef struct B {
	int x
	int y
	float z
} Point


object point = new Point(100, 200, 33.3f)

println("point: " + point)

```

in order to make the syntax also compliant  to C/C++, a semicolon ( ; ) may be optionally appended to the *Alias* (i.e Point)

```c++

typedef struct B {
	int x
	int y
	float z
} Point;

...

```

#### **`typedef struct`** instance like an object

When declaring a 'struct' definition with **`typedef struct`**, the resulting 'struct' can also be initialized like an object as follows:

```c++

typedef struct A {
	char id[256]
	int a
	int b
	int c
}

char myid[] = {'D', 'P', 'D', 'L'}

object myaobj = new(A, myid, 1, 2, 3}

println("myaobj: " + myaobj)
```


## `enum`

```c
enum myStatus { PENDING, DONE, ERROR, RUNNING=23 }
enum myStatus status
println(status.RUNNING)
```

## `union`

```c
union myU1 { int x, y, z }
union myU2 : myU1 { char a, b, c }

union myU1 u1
u1.x = 888
```

C-interop: `genObjCodeC(u)` then `u_unionC.setType("int")` before passing.

## Pointers

```c
int i = 10
int *i_p = &i
println("i_p: " + *i_p)
```

Supported for: int, byte, string, char, float, double, short, var, object, struct.
> When passing a pointer to a function, the function must use the same parameter name
> (e.g. `func f(int *px)` uses `*px`).

## Threads

```python
int tId = Thread("myFunc")               # 1000 ms default
int tId = Thread("myFunc", 3000)         # 3 s interval
int tId = Thread("myFunc", 3000, 23)     # max 23 iterations
```

## Exception Handling

```python
raise(object condition)
raise(object condition, string msg)
raise(object condition, string msg, bool exit)
```

Condition rules: string → `!= "null"`; int → `!= -1`; bool → `true`; object → `!= null`.

## Asynchronous Tasks

```python
>>task
	println("runs async")
<<
int exit_code = dpdl_exit_code()
object task_id = dpdl_task_pop_id()
object task_obj = dpdl_task_obj_get(task_id)
```

Named task: `>>task(my_task_name) ... <<` then `dpdl_task_obj_get("my_task_name")`.

## Multi-line Resources

```python
dpdl_stack_var_put("msg", "Hello")
>>res(my_html)
	<html><body><p>{{msg}}</p></body></html>
<<
object resid = dpdl_res_pop_id()
object myhtml = dpdl_res_obj_get(resid)       # or by name
```

## JRE / Java Interop

```python
object calendar = getObj("Calendar")
object str = new("String", "Test")
object myhm = new("HashMap")
myhm.put(1, "element_1")
```

- Load additional JARs: `DPDLSYS_registerLib("./lib/MyLib.jar")`.
- Primitive boxing: `object o = x` where `x` is `int` → `java.lang.Integer`.

## Loading Dpdl code as an object

```python
object mycode = loadCode("LoadCodeFunc.h")
mycode.testFunc("LoadCodeFunc", mystr1, mystr2)
object mycode2 = loadCode("LoadCodeFunc.h", mymap)   # with constructor arg
```

## Imports and Includes

```python
include("testImportInc.h")   # must precede imports
import("dpdllib.h")
```

## Embedded Code Sections

Any of these can be embedded inline:

```python
>>c
	int v = 1000;
	for(int i = 0; i < v; i++){
		printf("Processing: %d\n", i);
	}
<<

>>python
	stories = ['Story 1', 'Story 2']
	for s in stories:
		print(s)
<<

>>js
	console.log("hi from js");
<<

>>java
	int v = 1000;
	return v;
<<
```

Supported plug-ins: C, C++, Python, MicroPython, Julia, JavaScript, Lua, Ruby, Java,
PHP, Perl, Groovy, V, Clojure, Wgsl, OpenCL.

C code can be compiled at runtime in memory via the `dpdl:compile` option.

## Type Handling

- `typeof(x)` returns the type name (`"int"`, `"string"`, `"struct:A"`, `"HashMap"`, …).
- `to_int(..)`, `to_float(..)`, `to_double(..)` for numeric conversions.
- `convert("string", x)` for type conversions.

## Entry Points / Execution

Run Dpdl code via:
- **DpdlEngine** command line client
- **Dpdl Java API**
- **Dpdl C API** — `dpdl_exec_script(file)` / `dpdl_exec_code(src)`

## Best Practices when writing Dpdl

1. Use `println("… $var …")` interpolation rather than concatenation where clean.
2. Prefer `var` in function signatures for multi-type acceptance (e.g. struct vs. primitive arrays).
3. For struct/class interop with Java, compile via `genObjCode(..)`.
4. For C library interop, convert to C types via `genObjCodeC(..)` and use `native.loadLib(..)`.
5. `include` must appear **before** any `import`.
6. `const` variables cannot be reassigned — this raises an error.
7. Multiplication `*` requires spaces around the operator.
8. `>>java` embedded code inside a class/struct: only the *first* section is compiled to bytecode.

## Reference Links (from source docs)

- Dpdl quick tour: https://github.com/Dpdl-io/DpdlEngine/blob/main/Dpdl_lang_quick_tour.md
- Dpdl API: https://github.com/Dpdl-io/DpdlEngine/blob/main/doc/Dpdl_API.md
- Language plug-ins: https://github.com/Dpdl-io/DpdlEngine/blob/main/doc/Dpdl_language_plugins.md
- HowTos: https://github.com/Dpdl-io/DpdlEngine/blob/main/doc/Dpdl_howto.md
- Drafts: https://github.com/Dpdl-io/DpdlEngine/blob/main/doc/Dpdl_drafts.md
- Website: https://www.dpdl.io
```

---

