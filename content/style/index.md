---
title: Style
menu:
  main:
    weight: 50
author: Amos Wenger
copyright: 2009-2014 Amos Wenger
license: CC BY-SA 3.0
---

# magic/ooc style guide
**Last updated 2015-01-20 with rock 1.0.13 and magic 0.1.21**

Applies to all `magic`/`ooc` code.

Observe that design structure is a huge topic. Keep in mind that a style guide is **not** an absolute law, but a document to refer to in discussions and for learning best practices.


## The ten main rules
The following short rules should always serve as your main guideline:

- Optimize for readability, rather than performance
- Optimize for performance only when proven needed
- Try to avoid long files, instead write long lines, but not longer than 200 characters.
- Maintain an object-oriented approach
- Do not write any excess symbols like `()`, `{}`, etc.
- Comment where needed, but no more
- Try to make variable and function names self-explanatory
- Use hard tabs for indentation
- Never use semicolons
- Think `C#`, rather than `C`

These rules imply certain best practices, detailed below.

## Names and capitalization
Classes and covers should preferably be split into separate files. Their names are written in *PascalCase*.

Files that, for some reason, contain several _independent_ classes, or perhaps just global functions, are written in *camelCase*. Note the word independent. Certain classes have private helper classes defined in the same file, in this case the filename is still the main class name.

Methods, properties, and global functions are written in *camelCase*. Functions and methods should always be in a verb/action form. Try to avoid ambigous terms (does `clean()` actually clean or does it check if the class is clean?).

Properties that return `Bool` should be prefixed with `is` or `has` depending on what's more appropriate. That means `isEmpty`, rather than `empty`.

Since `rock` does not support `private` and `public` keywords, all private types are prefixed by `_` (underscore). No other uses of underscore in names are allowed, as they degrade the readability of the generated C code. The only exception is in `for` loops where the index variable is not needed, as in:

```ooc
for (_ in 0 .. 10)
	callMeTenTimes()
```

Long and descriptive names are strongly preferred over short, cryptic names. This goes for variables, properties and function names. Standard C keywords are not allowed as function names. One-letter variable names are almost always bad, even as indexers.

Rock does not support the same name for member functions and static (class) functions, which may be called a bug.

Acrs R bd. What was that? Acronyms are bad. Do not use them. `temporary` is better than `temp`.

Avoid field names inside methods. The following is not recommended:

```ooc
Rectangle: class {
	leftTop, rightBottom: Point
	moveTo: func (leftTop, rightBottom: Point) {
		this leftTop = leftTop
		this rightBottom = rightBottom
	}
}
```

## Class structure
All classes and covers shall follow this structure, from top to bottom:

- Member variables
- Properties
- Constructors
- (`free`)
- Methods
- Operators
- Static variables
- Static properties
- Static methods
- (unmangled)

Below the class, any public operators can be written. Private classes, enums etc. should be written above the class that use them.

Exceptions to this rule can be found and are allowed if they improve code readability.

Do not have any public variables in classes, only access them via properties. This is to not break the ABI.

Define objects *before* you use them, even though this is not strictly necessary. This means that any private classes are placed above classes that use them.

Constructors that only call `super` can be written as `init: super func`, however this is not allowed, as it is highly unstable.

Overriden `abstract` or `virtual` methods must always use the `override` keyword.

The generic type of generic classes or methods is generally denoted `<T>`, or `<S, T>` if you need two.

When talking about class members, properties or methods from inside the class, the `this` keyword is absolutely mandatory.

## Spacing
Only add empty lines inside function definitions if removing them significantly impacts code readability. Otherwise, adhere to the maxim of as few lines as possible. Consider splitting the function into two parts instead.

Do not in general add empty lines between methods or properties. Sometimes they may increase readability, but only rarely.

Always surround operators with spaces, including ranges (`..`). Also write a space after each keyword, such as `if`, `while`, `for`, and so on.

Chained `if` statements are written as follows:

```ooc
if (statement1) {
	...
} else if (statement2) {
	...
} else {
	...
}
```

Enumerations are written with one element per line, as such:

```ooc
FoodScore: enum {
	noFood = -1
	pancakes = 35
	lasagna = Int maximumValue
}
```

The default initial value is `0`, and if that's what you use it should not be explicitly set. Do not add commas to separate elements.

Generic type information has a space surrounding the keyword `class`, but not otherwise. Ergo, `ThreadFuture: class <T> extends Future<T>` is correct.

There is also always a space after a comma in variable declarations, function arguments etc.

## Comparative (boolean) statements
Minimizing the amount of code and maximizing readability easily come into conflict here. Properties, methods or variables that are boolean need not be compared with `== true` or `== false`, but rather using the `!` operator if needed. For example: `if (!queue isEmpty)`, not `if (queue isEmpty == false)`.

When the return type is not `Bool`, always be explicit. Although `if (queue count)` is equivalent to `if (queue count > 0)`, the latter is preferred as it is easier to understand.

The same approach is used for checking that pointers are not `null`. The following is allowed:

```ooc
if (video)
	video free()
```

However, when checking that they *are* null, this is preferred:

```ooc
if (video == null)
	raise ("no video found")
```

Version blocks *should* behave as if-statements, but rarely do, because of parsing errors for `!`. Nevertheless, version flags should be compared using their name and possibly `!`, nothing more.

```ooc
version ((!swedishFlag) && norwegianFlag && (!danishFlag)) {
	...
}
```

Note the parentheses around flags with `!` in order to get around possible parsing errors.

## Types and default values
Only specify type information where needed. `myInteger := 0` is better than `myInteger: Int = 0`. However, `myUnsigned := 12U` may not be better than `myUnsigned: UInt = 12`.

Built-in types we use are mainly `Int`, `UInt` (possibly suffixed by 8, 16, 32 or 64), `Float`, `Bool`, `Char`, `Text`. The use of `SizeT` and `SSizeT` is discouraged in anything but low-level C-interfacing code.

Older code may still use `String`, but it may be considered deprecated. Wherever possible, use `Text` instead.

Code such as `myBool := false` is in general preferred over `myBool: Bool`. However, if the value is definitely set afterwards, the latter option is equally good, for example:

```ooc
sandwiches: Int
if (sandwichList isAlmostEmpty)
	myInt = 1
else
	myInt = sandwichList count
```

Use explicit conversion only where needed. For example, `myInt: Int = 2.3f as Int` is unnecessary. Conversion between numeric types and/or `Bool` generally works well.

Never use `VarArgs`.

## One liners
Just as for action heroes, one-liners are preferred. The only exception to the "minimize line count" rule is to write single-line `if` statements on two lines, as follows:

```ooc
if (video isStabilized)
    relax()
else
    stabilize()
```

Consider changing simple `if-else` statements using the `?:` operator, especially for properties. If your `?:` operation cannot be written on one line, it should probably be an `if-else` statement instead.

Use the `??` operator whenever possible:

```ooc
device = getMyAndroidPhone()
if (device == null)
	device = buyNewPhone()
```

The above can, and should, be written simply as `device = getMyAndroidPhone() ?? buyNewPhone()`.

One-line functions may be written on one line, as follows:

```ooc
getBestLanguage: func -> Text { t"ooc" }
```

If this style fits the current context, it is preferred.

However, this rule does not apply to statements using `if`, `for`, etc.: `if (this isEmpty) { this _count = 0 }` is *not* allowed. This is generally true for `version` blocks as well, although exceptions for debug code can be made. Debug code and/or one-line version blocks may be written as `version (debug) { doDebugStuff() }`.

## Comments
Comments should be sparsely used and only used to clarify why something is done. Preferably, the code is written in such a way as to avoid the need for comments. Use `//`, not `/* */`.

A comment that describes a method or a block is placed on the line above with the same indentation:

```ooc
// Chosen by fair dice roll, guaranteed to be random
random: func -> Int {
	result := 4
	result
}
```

A comment describing a line or a few lines is placed on the line above them, not on the same line, unless it is very short.

Commented-out code is generally not accepted, and not allowed for larger portions of code. If you need to look back, use version control.

Comments highlighting something which must/should be done/improved shall be prefixed with `TODO`:

```ooc
sortList: func (unsorted: List) -> List {
	//TODO This will only work sometimes, consider actually sorting it
	unsorted scramble()
}
```

Other keywords such as `FIXME`, `NOTE`, etc. are discouraged.


## Imports and Uses
Try to include files through `use` clauses, such as `use base`, rather than explicit imports such as `Import base/TextBuilder`. Write each `use` clause on a separate line.

`Import` clauses are also to be written on separate lines, except when more than one come from the same (and different) folder. An example:

```ooc
use base
use math
import ../../[one, two]
import ../three/four
import ../[five, six]
import seven
import eight
```

This approach maintains a balance between easy diffs on GitHub, readability, and reduced line count.

Generally, all files should belong to some component, grouped together by a `use` file. Different files inside one component that depend on each other *should* be the only cases where `import` is used.

## Properties vs. Methods
To quote MSDN:

*In general, methods represent actions and properties represent data. Properties are meant to be used like fields, meaning that properties should not be computationally complex or produce side effects.*

When using `OwnedBuffer`, properties may have sideeffects. The following style is a good example:

```ooc
width: Int { get {
	result := this _width
	this free(Owner Receiver)
	result
}}
```

Properties that include both a getter and a setter, which cannot be written just as `{ get set }` should be written as:

```ooc
_myField: BestClass
myField: SomethingElse {
	get { this _myField }
	set (value) {
		this _myField free()
		this _myField = value
	}
}
```

Note the space after `set` just as for `version` because they define a block, like `if`, `while`, and so on. Preferably, what is passed to the setter is always called `value`. Also note that the getter is written on one line, but the setter is not because it does more. The following would be OK:

```ooc
_myField: BestClass
myFieldSpecial: SomethingElse {
	get { this _myField specialized() }
	set (value) { this _myField = value fromSpecialized() }
}
```

## Return value
Functions should always return values rather than modify input arguments.

Never use the `return` keyword. Instead, the last line of a function is what is returned. For example, the following is discouraged:

```ooc
getRacetime: func (car: Racecar) -> Float {
	timeNeeded := this raceTrack length / car speed  // s = v * t
	if (car isBroken)
		return car repairTime + timeNeeded
	else
		return timeNeeded
}
```

The following is better:

```ooc
getRacetime: func (car: Racecar) -> Float {
	timeNeeded := this raceTrack length / car speed
	if (car isBroken)
		timeNeeded += car repairTime
	timeNeeded
}
```

Note that the return variable is often, but not always, named `result`. If you need to return more than one variable, use tuples.

If a method starts exceeding a full screen, it should probably be broken up into two or more smaller functions.

## Defensive programming
Generally, functions should assume that input arguments are correct and valid. Sometimes, though, bounds and/or content checking is good practice. If these are time-consuming, they should be surrounded by a `version (safe)` block.

Do not use `Exception` as a way to check for errors. Instead, call `raise()` or `Debug raise()` when something has gone wrong, to indicate an incorrect scenario has occurred. Never catch and/or rethrow exceptions.


## Indentation
All blocks are indented with hard tabs. The only exception is version blocks that span over an entire class or more. Their contents should not be indented.

Everything inside a `match` block is indented. Furthermore, everything following a `case` block is also indented one line. Be observant of using nested `match-case` blocks as some lines of the file may get very indented fairly quickly.

## Other

### Tests
All public code shall have unit tests associated with them, and preferably tests that will utilize all private parts of those classes as well.

Tests do not need to follow these guide lines as strictly, they are not part of the result.

### Owned
The `Owned` concept is used to facilitate memory management. A separate document has been written on this topic.

### Rule exceptions
Unfortunately, neither `rock` nor `magic` are perfect.

- One unfortunately neccessary part has already been observed earlier in this document when dealing with `version` blocks.

- Although variable names are to be descriptive, whenever `OwnedBuffer` is used, internal member functions are allowed to use `t := this take()`.

## When in doubt
1. Refer to the main ideas at the beginning of this document.

2. Ask the current code reviewer.
