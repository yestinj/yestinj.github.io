---
title: A better way to instantiate Java Collections
date: 2022-07-02T14:20:43.000Z
categories: [Java]
tags: [programming]
---

Using static factories.\netc..\nCompare to new HashSEt, etc..\nBenefits - immutable\n\n\nIntroduction\n\n\n\n\nWhat are static factories?\n\nRefer to effective Java item on this.\n\nhttps://www.geeksforgeeks.org/difference-between-constructor-and-static-factory-method-in-java/\n\nhttps://www.baeldung.com/java-constructors-vs-static-factory-methods\n\n\nWhen to use them\n\n\n\n\nStatic Factories for Java Collections\n\nIn Java 9, and particularly in https://openjdk.java.net/jeps/269 ,  new static factories for Java collections were introduced. Notably, Arrays.asList(), List.of(), Set.of(), Map.of()\n\n\nMap\n\nMap.of()\n\nEnumMap.of()\n\nFeatures/comparison to new in a table\n\n\nSet\n\nSet.of()\n\nFeatures/comparison to new in a table\n\n\nList\n\nFeatures/comparison to new in a table\n\nList.of()\n\nArrays.asList()?? Check this one.\n\n\n\n\nSummary\n\n\n\n\n\n\nReferences\n\n 1. https://www.geeksforgeeks.org/difference-between-constructor-and-static-factory-method-in-java\n 2. https://www.baeldung.com/java-constructors-vs-static-factory-methods\n 3. https://www.baeldung.com/java-9-collections-factory-methods\n 4. https://blog.knoldus.com/collection-static-factory-methods-in-java-9/\n 5. https://www.geeksforgeeks.org/java-convenience-factory-methods-for-collections/\n 6. https://www.javatpoint.com/java-9-factory-methods\n 7. https://www.codingame.com/playgrounds/46649/java-9-improvements/collection-factory-methods.

![img-description](/assets/img/hello.jpg)
_Hello World_
