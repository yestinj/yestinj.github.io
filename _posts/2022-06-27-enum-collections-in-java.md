---
title: Enum Collections in Java
date: 2022-06-27T06:32:29.000Z
categories: [Software Development]
tags: [java,efficiency,collections]
---

# Introduction

Recently I had the opportunity to rediscover one of my favourite things about Java, that there are so many different collection implementations to choose from! If you know what implementations are available,
and how to use them (both of which a quick Google search can help with), you can write much more efficient code.

Everyone who has used Java should be familiar with the common collections, and their standard implementations, such as: `Set` and `HashSet`, and `Map` and `HashMap`. These are general-purpose implementations
of the `Set` and `Map` data structures, which all developers should be familiar with. They can generally be used everywhere, however, there exist other implementations which are much better suited for specific
use cases.

In this blog post I'll discuss a few of the Enum-optimised implementations, and when to use them. I assume that you're already familiar with the standard implementations of these collections, and with the
concept of an Enum.

# EnumSet and EnumMap

The two enum-specific implementations that I discuss here are `EnumSet` and `EnumMap`. These implementations are fairly straightforward and easy to understand.

EnumSet and EnumMap were added to Java in version 1.5 (2004), and while they've been around for a long time now (18 years!), in my experience, not many developers know about or use them. Developers,
especially those who have moved to Java from other languages, will often just use the standard collection implementations and not give it a second thought. This is generally okay, but we can do better!

## EnumSet

Simply put, an `EnumSet` is a concrete implementation of the `Set` interface, which is optimised for the storage of a specific enum type. Given the static nature of an enum, and the fact that they are generally
fairly small, consisting of only a few constants, it is possible to use a very efficient implementation to store them, while still implementing the `Set` interface.

If you ever find yourself needing to store enums in a `Set`, you should use the `EnumSet` implementation, instead of the standard `HashSet`, unless you have a very specific reason not to.
`EnumSet` is not thread-safe, it keeps elements in their natural order, and it does not accept `null` values. As with other collections, you could use the `Collections.synchronizedSet()` wrapper if you require
a thread-safe set.

Without going in to too much of the implementation detail, it is good enough to know that `EnumSet` is backed by a bit vector. For up to 64 constant long enums (which is more than enough for almost every
use case), they are simply stored in a long data field (or an array of longs if there are more than 64 constants). This makes `EnumSet` extremely memory efficient, and similarly computationally efficient for all
operations, as they are implemented using arithmetic bitwise operations. All of the basic operations will run in constant time. This is much more efficient than in a `HashSet`, where the hashCode of the value
will always have to be calculated first.

As an example, assume we have the following enum:

```java
public enum Direction {
  NORTH, SOUTH, EAST, WEST
}
```

Then, creating an EnumSet to hold values from the `Direction` enum can be accomplished in two ways:

1. Create a new `EnumSet` containing all constants in the enum:
 - `Set<Direction> directions = EnumSet.allOf(Direction.class);`
2. Create a new empty `EnumSet`:
 - `Set<Direction> directions = EnumSet.noneOf(Direction.class);`
  
All of the usual `Set` methods can be used with an `EnumSet`, with the most common being: `add()`, `contains()`, `forEach()`, and `remove()`. There are a few other useful methods, all of which can be found by
visiting the Java reference.

Note: As `EnumSet` is an abstract class, it cannot be instantiated using the `new` operator, one of the above static factory methods must be used instead.

## EnumMap

Similarly, `EnumMap` is a concrete implementation of the `Map` interface, which is optimised for use with an enum key. As with `EnumSet`, given the characteristics of an enum, it is possible to create a much
more efficient implementation than the general-purpose `HashMap`.

If you ever find yourself using an enum as the key for a map, you should seriously consider using an `EnumMap`. `EnumMap` is not thread-safe, it displays natural order sorting (the order in which the enum keys
are defined), and does not accept `null` keys, but does accept `null` values. As with other collections, you could use the `Collections.synchronizedMap()` wrapper if you require a thread-safe map.

Again, without going into much of the implementation detail, it's good enough to know that `EnumMap` is backed by a simple Java array. This makes it much more memory and computationally efficient than a
`HashMap`, as it does not need to account for hashing a generic key, nor the possibility of hash collisions.  This makes all storage and retrieval operations much faster, and all basic operations run in
constant time.

As an example, assuming we have the same `Direction` enum defined earlier, an `EnumMap` can be created and added to as follows:

```java
Map<Direction, String> directions = new EnumMap<>(Direction.class);
directions.put(Direction.NORTH, "Go north");
directions.get(Direction.NORTH);
```

As with any other implementation of the `Map` interface, all of the usual operations can be performed on an `EnumMap`.

## Benchmarks

In order to get some idea of the practical performance improvements of using `EnumSet` and `EnumMap` over `HashSet` and `HashMap`, I wrote a small microbenchmark that tests the creation, insertion, contains,
and removal operations on each implementation, over many iterations. The code can be found here: [Enum Collections](https://github.com/yestinj/EnumCollections).

I ran these microbenchmarks on my M1 Mac Mini (2020, 8GB) running macOS Monterey 12.4, using JMH version 1.35, on OpenJDK 64-bit Server VM 18.0.1.1+0.

nFirst, looking at HashSet versus EnumSet; when using HashSet the benchmark performed at ~14.64m operations per second, while the EnumSet implementation performed at a whopping ~425.27m operations per second!
This makes EnumSet on average 29x faster than HashSet!

Next, considering the maps; HashMap performs at ~15.54m operations per second, and EnumMap performs at ~70.51m operations per second. This makes EnumMap, on average, 4.5x faster than HashMap!

These microbenchmarks are synthetic, and don't represent a real use case, however, it should still be obvious how much more computationally efficient it is to use EnumSet and EnumMap! And, this doesn't even
take into account the memory efficiency improvements.

# Conclusion

In summary, if you ever find yourself using an `enum` as the key for a map, or as the object you're storing in a set, be sure to use `EnumMap` and `EnumSet` if possible, and benefit from their improved memory
efficieny and computational performance.

As always, 'micro-optimisations' such as this can be counterproductive, I wouldn't recommend refactoring an existing codebase just for this. However, if you're having performance issues, or writing new code,
there's really no reason not to consider using these implementations. Just make sure that you've done your homework! 😊

# Additional Reading

1. [https://www.baeldung.com/java-enum-map](https://www.baeldung.com/java-enum-map)
2. [https://www.geeksforgeeks.org/enummap-class-java-example](https://www.geeksforgeeks.org/enummap-class-java-example)
3. [https://www.baeldung.com/java-enumset](https://www.baeldung.com/java-enumset)
4. [https://www.geeksforgeeks.org/enumset-class-java](https://www.geeksforgeeks.org/enumset-class-java)
