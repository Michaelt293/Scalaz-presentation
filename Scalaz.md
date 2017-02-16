Scalaz
===

### An extension to the core Scala library for functional programming.

---

### Scalaz is split into several sub-projects

* scalaz-core: Main sub-project of Scalaz.
* scalaz-effect: Data structures to represent and compose IO effects in the type system.
* scalaz-concurrent: Actor and Future implementation
* scalaz-iteratee: Experimental new Iteratee implementation

---

### Using Scalaz (core)

- SBT

`libraryDependencies += "org.scalaz" %% "scalaz-core" % "7.2.8"`

`import scalaz._, Scalaz._`

- Ammonite

`import $ivy.`org.scalaz::scalaz-core:7.2.8`, scalaz._, Scalaz._`

---

### Syntax extensions

- Compact syntax for `getOrElse`
```
@ Some("awesome string") | ""
res42: String = "awesome string"
@ None | ""
res43: String = ""
```
- Compact syntax for if-else expression
```
@ true? "yes" | "no"
res44: String = "yes"
@ false? "yes" | "no"
res45: String = "no"
```

---

### `Equal` typeclass

Scala's `==` and `!=` are too permissive and allow different types to be compared.
```
@ 2 == "two"
res31: Boolean = false
```
Scalaz's `===` and `=/=` should be used instead.
```
@ 2 === "two"
cmd32.sc:1: type mismatch;
 found   : String("two")
 required: Int
val res32 = 2 === "two"
                  ^
Compilation Failed
@ 2 == 2
res33: Boolean = true
@ 2 =/= 20
res35: Boolean = true
```
---

### `Order` typeclass

```
@ 1 gt 2
res39: Boolean = false
@ 1 ?|? 2
res40: Ordering = LT
@ 1 max 2
res41: Int = 2
```

---

### `Enum` typeclass

```
@ 1 |-> 10
res36: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
@ 1 |--> (2, 10)
res37: List[Int] = List(1, 3, 5, 7, 9)
@ 'a' |==> 'd'
@ 'a' |=> 'd'
res38: EphemeralStream[Char] = scalaz.EphemeralStream$$anon$5@2f833b71
@ res38.n
```

---

### Working with `Option`

- Using `some` and `none`
```
@ Some(3)
res27: Some[Int] = Some(3)
@ 3.some
res28: Option[Int] = Some(3)
@ val test = None
test: None.type = None
@ val test2 = none[Int]
test2: Option[Int] = None
```
- Using `option`
```
@ if (x > 0) x.some else none
res25: Option[Int] = Some(4)
@ (x > 0).option(x)
res26: Option[Int] = Some(4)
@ Some(3)
```

---

### `NonEmptyList`

`NonEmptyList` communicates to readers that the list cannot be empty. The fact that the list cannot be empty means that methods such as `head` and `tail` are non total functions. 

```
@ NonEmptyList(1,2,3)
res46: NonEmptyList[Int] = NonEmpty[1,2,3]
@ NonEmptyList(1,2,3).head
res47: Int = 1
@ NonEmptyList(1,2,3).tail
res48: IList[Int] = [2,3]
@ NonEmptyList()
cmd49.sc:1: not enough arguments for method apply: (h: A, t: A*)scalaz.NonEmptyList[A] in object NonEmptyList.
Unspecified value parameters h, t.
val res49 = NonEmptyList()
                        ^
Compilation Failed
```