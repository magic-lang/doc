---
title: Classes
menu:
  main:
    parent: Language
    weight: 60
author: Amos Wenger
copyright: 2009-2014 Amos Wenger
license: CC BY-SA 3.0
---

# Defining classes

Define classes with the `class` keyword:

{{% highlight ooc %}}
Dog: class {
  name: String
  race: Race

  init: func (=name, =race)
  bark: func { "Woof!" println() }
}
{{% /highlight %}}

Then, call `new` on it to make an instance of it:

{{% highlight ooc %}}
dog := Dog new()
{{% /highlight %}}

An instance of a class is also called an object.

# Members

Members are variables tied to an instance:

{{% highlight ooc %}}
Dog: class {
  // declare a field named 'name'
  name: String

  init: func (=name)
}

d1 := Dog new("Pluty")
d2 := Dog new("Snoopo")

"d1's name is = %s" printfln(d1 name)
"d2's name is = %s" printfln(d2 name)
{{% /highlight %}}

## Built-in members

There are a few members always available on classes. You can access the
class of any object via the `class` member. For example:

{{% highlight ooc %}}
// will be equal to 'Dog'
dog class name

// since objects are reference, will be the size of a pointer
dog class size

// the actual size of a dog object, including members
dog class instanceSize
{{% /highlight %}}

## Static members

Static members belong to a class, rather than to an instance.

{{% highlight ooc %}}
Node: class {
  count: static Int = 0

  init: func {
    This count += 1
  }
}

for (i in 0..10) {
  Node new()
}
"Number of nodes: %d" printfln(Node count)
{{% /highlight %}}

In the code above, `count` is "shared" among all instances of node - hence,
incrementing it in the constructor will be "remembered" the next time a node
is created. So, we really are counting the number of nodes being created.

Static fields can also be accessed without explicitly referring to `This`.
The declare-assignment operator, `:=`, also works with the `static` keyword before
the right-hand-side value:

{{% highlight ooc %}}
Node: class {
  count := static 0

  init: func {
    count += 1
  }
}

// etc.
{{% /highlight %}}

## Properties

The shortest and sweetest way to define a property is to use the `::=` operator:

{{% highlight ooc %}}
Rectangle: class {
  width, height: Int
  area ::= width * height
}
{{% /highlight %}}

Contrary to a variable declaration, the value of `area` will be
recomputed every time it is being accessed. Contrary to a function
call, one does not need parenthesis to call its getter, nor can it
pass any argument.

Properties are mostly useful as shorthands for an expression that
is often computed, but that would be overkill as a method.

In the technical jargon, we say that properties are, `virtual`
members that exist as read-only, write-only, or read-write behind
getters and setters.

Here's an example of long-form, read-only property:

{{% highlight ooc %}}
Person: class {
  lastName, firstName: String

  fullName: String {
    get {
      "%s %s" format(lastName, firstName)
    }
  }
}
{{% /highlight %}}

Note that when specifying a getter, one does not need a return type,
as it is the type of the property itself.

Similarly, when specifying a setter, one only needs an argument name
not its type:

{{% highlight ooc %}}
Person: class {
  lastName, firstName: String

  name: String {
    set (name) {
      tokens := name split(" ")
      assert(tokens size == 2)
      (firstName, lastName) = (tokens[0], tokens[1])
    }
  }
}
{{% /highlight %}}

Empty getters and setters are valid as well, for a simple read-write property:

{{% highlight ooc %}}
Person: class {
  name: String { get set }
}
{{% /highlight %}}

The advantage is the following - since a property is only accessed via its
getters and setters, which are methods, changing the structure of the `Person`
class will not necessarily trigger a recompile on the modules which use it,
nor will they need to explicitly import that module, if they get a `Person` instance
from somewhere else.

This is a way to work around what is known as the [Fragile Base Class Problem](fragile).

[fragile]: http://c2.com/cgi/wiki?FragileBaseClassProblem

# Methods

Methods are function declarations in the class body, that are called
on a particular instance:

{{% highlight ooc %}}
Dog: class {
  bark: func {
    "Woof!" println()
  }
}

dog := Dog new()
dog bark()
{{% /highlight %}}

## this and This

In a method, the special variable `this` is accessible, and refers to the object
the method is being called on.

Example usage of `this`

{{% highlight ooc %}}
Building: class {
  height: Int

  // argument name shadows member name
  setHeight: func (height: Int) {
    if (height < 0 || height > 300) return

    // using `this` explicitly to differenciate them
    this height = height
  }
}
{{% /highlight %}}


`This`, on the other hand, refers to the type currently being defined:

{{% highlight ooc %}}
Engine: class {
  logger := Log getLogger(This name)
}
{{% /highlight %}}

In the example above, we are using the name of the class we are currently defining,
instead of typing out `"Engine"` directly — that way, if we rename the class, the
code will still be valid. It's a good way to [avoid repeating yourself][dry].

[dry]: https://en.wikipedia.org/wiki/Don%27t_repeat_yourself

## Static methods

Static methods also belong to a specific class, but they're not tied to
a particular instance. Hence, you don't have access to `this` in a static
method because it's not called on an instance:

{{% highlight ooc %}}
Map: class {
  tiles := Map<Tile> new()

  generate: static func (width, height: Int) -> This {
    m := This new()
    for (y in 0..height) for (x in 0..width) {
      m addTile(x, y)
    }
    m
  }

  addTile: func (x, y: Int) { /* ... */ }
}
{{% /highlight %}}

In some languages, `new` is a keyword used to create objects. In ooc,
it's just a static method doing some allocation and initialization, and
returning a new instance. See "Constructors" for more details.

# Constructors

Define the `init` method (with a suffix to have different constructors), and
a `new` static method will get defined automatically.

{{% highlight ooc %}}
Dog: class {
  name: String

  init: func (=name)
  init: func ~default { name = "Fido" }
}
{{% /highlight %}}

For alternative instantiation strategies, defining a custom, static `new`
method, returning an instance of type `This`, works just as well:

{{% highlight ooc %}}
Dog: class {
  pool := static Stack<This> new()

  new: static func -> This {
    if (pool empty?()) {
      obj := This alloc()
      obj __defaults__()
      obj
    } else {
      pool pop()
    }
  }

  free: func {
    pool push(this)
  }
}
{{% /highlight %}}

We can clearly see that the `alloc` static method here does memory allocation
for the object, but what about `__defaults__`? It contains initializers, discussed
in the next section.

## Initializers

We've discussed methods, but not all code that belong to a class is in an explicit
method. For example, in this code, declaration and initialization are clearly separate:

{{% highlight ooc %}}
Group: class {
  number: Int

  init: func {
    number = 42
  }
}
{{% /highlight %}}

But what happens with the following code?

{{% highlight ooc %}}
Group: class {
  number := 42

  init: func {
  }
}
{{% /highlight %}}

The resulting executable does the same. The class contains a field
of type Int, initially equal to 0, but there's an implicit `__defaults__`
method that contains all code outside of a method, that gets executed before
the `init` method is called.

Above, the example is a pattern you'll see often - however, one can put
any amount of code directly in the class declaration:

{{% highlight ooc %}}
Dog: class {
    "You made a dog!" println()
    init: func
}
{{% /highlight %}}

Every time `Dog new()` is called, the `"You made a dog!"` string will get
printed.

# Inheritance

## Extends

Simple inheritance is achieved through the `extends` keyword:

{{% highlight ooc %}}
Animal: class {}
Dog: class extends Animal {}
{{% /highlight %}}

In this case, an instance of `Dog` will also be an instance of `Animal`,
and it inherits all its methods and members.

For example, a function expecting an `Animal` can be passed a `Dog` instead.
That is, if your code is designed correctly. For some encyclopedic knowledge
on the matter, check out the [Liskov Substition Principle][liskov]

[liskov]: http://en.wikipedia.org/wiki/Liskov_substitution_principle

## Super

Calling `super` will call the definition of a method in the super-class.

{{% highlight ooc %}}
SimpleApp: class {
  init: func {
    loadConfig()
  }

  // ...
}

NetworkedApp: class extends SimpleApp {
  init: func {
    super()
    initNetworking()
  }
}
{{% /highlight %}}

When one just wants to relay a constructor, one can use `super func`:

{{% highlight ooc %}}
MyException: class extends Exception {
  init: super func
}
{{% /highlight %}}

Which is equivalent to the following:

{{% highlight ooc %}}
MyException: class extends Exception {
  init: func {
    super()
  }
}
{{% /highlight %}}

`super func` can take a suffix, and it relays argument as well. It is useful
when you really don't have much more to do in the constructor of the sub-class.

Please bear in mind that `super func` is relatively hackish - it is documented
here for completeness sake, but it is more of a rapid coding trick than a good
practice, really.

## Class hierarchy

The class hierarchy can be explored via built-in members and methods on objects
and classes:

{{% highlight ooc %}}
// in this case, the Object class - otherwise, whatever super class it has dog class super

// evaluates to true
dog instanceOf?(Dog)

// also evaluates to true
dog instanceOf?(Object)

// evaluates to false
dog instanceOf?(Cat)
{{% /highlight %}}

The equivalent of `instanceOf?` called on classes, is `inheritsFrom?`

{{% highlight ooc %}}
// true
Dog inheritsFrom?(Dog)

// true
Dog inheritsFrom?(Object)

// false
Dog inheritsFrom?(Cat)
{{% /highlight %}}

# Adding methods after definition

This applies to classes, covers, and enums alike. The `extend`
keyword can add superficial methods to any type, even if it is
defined in another module.

It is useful to add convenience methods of your own without having
to modify the original library.

{{% highlight ooc %}}
extend Float {
  negated: func -> This { -this }
}

if (-3.14 == 3.14 negated()) {
  "Everything is fine" println()
}
{{% /highlight %}}

Virtual properties (that do not correspond to a real instance variable,
but rather compute their value from other information everytime) can also
be added in an `extend` block:

{{% highlight ooc %}}
extend Int {
  plusFive: This { get {
    this + 5
  } }
}
{{% /highlight %}}

See the [Properties](#properties) section for more info on properties.
