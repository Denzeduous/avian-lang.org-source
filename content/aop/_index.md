---
date: 2020-01-20T20:46:51-07:00
---

# Actor-Oriented Programming

What is Actor-Oriented Programming (AOP)? AOP is a new paradigm branching off from [Object-Oriented Programming (OOP)](https://en.wikipedia.org/wiki/Object-oriented_programming)). AOP takes the focus away from the data and moves it back to the implementation. How is this achieved? Imagine a namespace had a baby with an interface, and you've got actors. Actors are the main focus of AOP.

The Four Pillars of Actor-Oriented Programming are:

- Explicitness over implicitness
- Employment
- Inheritence
- Polymorphism

"Wait," you might say, "two of those are from the three pillars of OOP!" and you'd be right. AOP is a paradigm based heavily on OOP, taking the best parts of it, but taking away all of the power from data to act since they've been too naughty for too long with that privilege. Data should NOT act, it should ask for actors to act on it. Let's first talk about The Four Pillars.
z
### Explicitness Over Implicitness

This is first for a reason. In AOP, everything you do should be explicitly YOUR choice. You have control over the code and how you use it, not anyone else. This can lead to some pitfalls and the possibility of shooting yourself in the foot if done poorly, that's understood. AOP seek to make this as simple and straight-forward as possible with [Actors](#Actors).

### Employment

Employment is the second, and thereby one of the most important, pillar. Employment is how AOP works so well; you take actors and have objects employ them. Through this, the programmer can control what functionality they wish without being impeded. Functionality can be easily removed, added, swapped, and possibly more! AOP is such a new paradigm that things will radically shift in short periods of time, employment probably going to see the most radical changes of all.

### Inheritence

Just like OOP, [Inheritence](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) is a core part of AOP. However, instead of with classes it's with actors and objects. In AOP, actors can inherit [actions](#Actions) and procedures from parent actors. This, like in OOP, allows for some amazing type-based code structures.

### Polymorphism

Though AOP has [Polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(object-oriented_programming)) just like in OOP, here it's a bit different. AOP discourages overloading (though an individual language could implement it) but openly allows for [Overriding](https://en.wikipedia.org/wiki/Override_(object-oriented_programming)) and [Parametric Polymorphism](https://en.wikipedia.org/wiki/Parametric_polymorphism). The reason that overloading is discouraged is that it can complicated the language's ABI and the confusion that implicit overloading causes is not worth it (though of course, explicit compile-time overloading is fine).

## Actors

Finally we get into the meat of Actor-Oriented Programming, the actors! In AOP, actors are essentially both namespaces and interfaces. Actors cannot hold data, that's the job of objects, but they can hold actions and procedures. In OOP, the equivalent to an action would be a method. What's the difference between actors and procedures? First, let's explain the concept of object precedence in parameters.

### Object Precedence

When an object has precedence, that essentially means it's more important in the procedure/action then another parameter. Object precedence is an important concept in AOP. Let's take a car, for example, using Avian:

```
Car :: obj {
    tires: [4]Tire,
}

Tire :: obj {
    ...
}

Vehicle :: actor (tires: []Tire) {
    add_tire :: act (tire: Tire, i: int) (
        'tires[i] = tire;
    }
}

main :: proc() {
    car  := Car[Vehicle('tires)]{};
    tire := Tire{};
    car->add_tire(tire, 0);
}
```

In this example, the car has higher object precedence than the tire, thus the car is the one employing the actor, instead of vice versa.

#### Employment

This example also illustrates the point that objects can't act by themselves. The programmer first has to manually employ the `Vehicle` actor to the car's initialization. This is all up to the programmers using the object, and can be used to remove functionality you're never going to use, new functionality, or even choosing from different types of functionality! That's the true power of Employment, and allows for some amazing things shown later.

### Procedures

Procedures should not have side-effects. Obviously, there are edge cases to this, but for the most part, there shouldn't be unintended side-effects. If your procedure requires a side-effect, make sure to document it well. Other than that, procedures are just normal functions. All parameters have equal precedence in the procedure.

### Actions

Actions should always have side-effects and have lopsided object precedence. However, actions should only have side-effects on the object that's employing that actor. If there's a reason as to why your action has a side-effect on another parameter, either that parameter should have higher precedence and be the one calling the action or there's most likely something wrong with your code structure. If there's a reason both of those are false, be sure to document it well.

### Employment in Action

Though employment was explained a short while ago, here's a practical view of it. This is one of the major features of AOP. Actors handle the implementation, all the programmer has to do do is tell the actor "Hey, use this data please." Example:

```
Foo :: obj {
    list: []?T;
}

BubbleSorter :: actor (list: []?T) {
    sort :: act () {
        ...
    }
}

QuickSorter :: actor (list: []?T) {
    sort :: act () {
        ...
    }
}

main :: proc () {
    bubble_foo := Foo(int)[BubbleSorter('list)]{};
    quick_foo  := Foo(int)[QuickSorter('list)]{};
    
    // Fill bubble_foo with data...
    // Fill quick_foo with data...
    
    bubble_foo->sort();
    quick_foo->sort();
}
```

This example displays both generics AND choosing different functionality by employing different actors! You're able to use different functionality by employing separate actors, allowing the programmer to decide specific implementations instead of the object itself! The previous example of employment is very similar to OOP's clunky workaround way of having an abstract object as a member in it, then calling functions through that member, though this is much more elegant and much more flexible (as in OOP this can't be achieved without accounting for it in the class, which removes power from the programmer and locks the implementation away).

### Casts

Casts are another unique addition to AOP. Consider them a structured union of actors that allow for templating some members across multiple actors quickly and painlessly. Here's an example in Avian (that also shows some more parametric polymorphism!):

```
Adder :: actor ('value: ?T/int) {
    add :: act (other: T) {
        'value += other;
    }
}

Subtractor :: actor ('value: ?T/int) {
    sub :: act (other: T) {
        'value -= other;
    }
}

Multiplier :: actor ('value: ?T/int) {
    mul :: act (other: T) {
        'value *= other;
    }
}

Divider :: actor ('value: ?T/int) {
    div :: act (other: T) {
        'value /= other;
    }
}

Modulator :: actor ('value: ?T/int) {
    mod :: act (other: T) {
        'value %= other;
    }
}

Calculator :: cast ('value: int) {
    Adder('value),
    Subtractor('value),
    Multiplier('value),
    Divider('value),
    Modulator('value),
}

Foo :: obj {
    a: int,
}

main :: proc () {
    foo := Foo[Calculator('a)]{10};
    foo->add(10); // 20
    foo->sub(10); // 10
    foo->div(2);  // 5
    foo->mod(5);  // 0
}
```

See how wonderful that makes adding multiple actors together on a single object? It requires very little work and reduces the inherit verbosity with large groups of actors.

Instead of having to type:
```
foo := Foo[Adder('a), Subtractor('a), Multiplier('a), Divider('a), Modulator('a)]{10};
```

You instead simply do:
```
foo := Foo[Calculator('a)]{10};
```

Wonderful, isn't it?

### Conclusion on Actor-Oriented Programming

There is a lot more to be discovered with this wonderful, new paradigm. As AOP is more fully realized, more will be added to this page. Ideas? Come share them in the official [Discord Server](https://discord.gg/RyN3Sxb)! We'd love to hear your ideas, opinions, and more!