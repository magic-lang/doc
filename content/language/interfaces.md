---
title: Interfaces
menu:
  main:
    parent: Language
    weight: 75
author: Simon Mika
copyright: 2014 Simon Mika
license: CC BY-SA-4.0
---
# Declaring interfaces

Declare interfaces with the `interface` keyword:

{{% highlight ooc %}}
IDog: interface {
	bark: func
}
{{% /highlight %}}

# Implementing interfaces

{{% highlight ooc %}}
Dog: class implements IDog {
	name: String
	race: Race

	init: func (=name, =race)
	bark: func { "Woof!" println() }
}
{{% /highlight %}}
