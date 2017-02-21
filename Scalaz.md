<!-- $theme: default -->

Scalaz
===

### An extension to the core Scala library for functional programming.

---

### Scalaz is split into several sub-projects

* scalaz-core: Main sub-project of Scalaz.
* scalaz-effect: Data structures to represent and compose IO effects in the type system.
* scalaz-concurrent: Actor and Future implementation
* scalaz-iteratee: Experimental new Iteratee implementation

Reference: https://github.com/scalaz/scalaz/blob/series/7.3.x/README.md

---

### Using Scalaz (core)

- SBT

`libraryDependencies += "org.scalaz" %% "scalaz-core" % "7.2.8"`

`import scalaz._, Scalaz._`

- Ammonite
```
import $ivy.`org.scalaz::scalaz-core:7.2.8`, scalaz._, Scalaz._
```

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
@ 'a' |=> 'd'
res38: EphemeralStream[Char] = scalaz.EphemeralStream$$anon$5@2f833b71
@ res38.n
```

---

### Working with `Option` datatype

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
```

---

### `NonEmptyList` datatype

`NonEmptyList` communicates to readers that the list cannot be empty. The fact that the list cannot be empty means that methods such as `head` and `tail` are now total functions.

```
@ NonEmptyList(1,2,3)
res46: NonEmptyList[Int] = NonEmpty[1,2,3]
@ NonEmptyList(1,2,3).head
res47: Int = 1
@ NonEmptyList(1,2,3).tail
res48: IList[Int] = [2,3]
@ NonEmptyList()
cmd49.sc:1: not enough arguments for method apply:
(h: A, t: A*)scalaz.NonEmptyList[A] in object NonEmptyList.
Unspecified value parameters h, t.
val res49 = NonEmptyList()
                        ^
Compilation Failed
```

---

### Tagged types

```
@ trait AgeTag
defined trait AgeTag
@ trait NameTag
defined trait NameTag

@ type Age = Int @@ AgeTag
defined type Age
@ type Name = String @@ NameTag
defined type Name

@ val michael = Tag.of[NameTag]("Michael")
michael: String @@ NameTag = Michael
@ Tag.unwrap(michael)
res8: michael.Self = "Michael"
@ val ages: List[Age] = Tag.subst(List(34,11,55,25))
ages: List[Age] = List(34, 11, 55, 25)
@ Tag.unsubst(ages)
res10: List[Int] = List(34, 11, 55, 25)
```

---

### `Validation` datatype

Not a monad but allows the accumulation of errors.

```
@ implicit class TagInts(n: Int) {
    def mkAge: Validation[NonEmptyList[String], Age] =
      (n > 0)? Tag.of[AgeTag](n).successNel[String] |
      "Age is negative".failureNel[Age]
    }
@ implicit class TagStrings(str: String) {
    def mkName: Validation[NonEmptyList[String], Name] =
      str.nonEmpty? Tag.of[NameTag](str).successNel[String] |
      "Name not provided".failureNel[Name]
    }
```

Note: The return types above could be written as `ValidationNel[String, Age]` and `ValidationNel[String, Name]`

---

### `Applicative syntax` with `Validation`

```
@ case class Person(name: Name, age: Age)
defined class Person
@ ("Michael".mkName |@| 25.mkAge) { Person }
res14: Validation[NonEmptyList[String], Person] =
    Success(Person(Michael, 25))
@ ("Michael".mkName |@| -100.mkAge) { Person }
res15: Validation[NonEmptyList[String], Person] =
    Failure(NonEmpty[Age is negative])
@ ("".mkName |@| -100.mkAge) { Person }
res16: Validation[NonEmptyList[String], Person] =
    Failure(NonEmpty[Name not provided,Age is negative])
```

---

### Resources

Source code -
https://github.com/scalaz/scalaz

Examples -
https://github.com/scalaz/scalaz/tree/series/7.3.x/example/src/main/scala/scalaz/example

Learning Scalaz -
http://eed3si9n.com/learning-scalaz/
