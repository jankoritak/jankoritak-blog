---
title: Narrowing Union Types
description: This is a post on My Blog about agile frameworks. This is a post on My Blog about agile frameworks. This is a post on My Blog about agile frameworks.
date: 2018-05-01
tags:
  - Typescript
layout: layouts/post.njk
---

## Intro

If you've ever used a Typescript Union, I'm pretty confident in stating you have encountered following type error.

```text
  Property 'P' does not exist on type 'T1 | T2'.
    Property 'P' does not exist on type 'T2'.(2339)
```

The reason very likely is that you're getting ahead of yourself.

You've tried to access property `P` by assuming a specific type from union `T1|T2` in context, where Typescript couldn't infer the same assumption.

So, say Thanks to the compiler, it just may have saved you a runtime error. Let's have a look why and what's going on.

## Ways to narrow Union Types

Narrowing types for unions of primitive types is trivial. In such cases, an exclusive condition will do the job. However, things get tiny bit more interesting, when working with object types.

Let's put together a model scenario. Say we take two planets of our Solar system - `Earth` and `Saturn` - and assert `Earth` to contain `water: Water` and `Saturn` to have a planetary `ring: Ring`.We can represent the planets with following types.

```typescript
  type Earth = {
    partOfSolarSystem: true,
    water: Water,
  }

  type Saturn = {
    partOfSolarSystem: true,
    ring: Ring
  }
```

Let's also have a logger function.

```typescript
  type Planet = Earth | Saturn

  const logPlanetInformation = (planet: Planet): void => {
    console.log(`Part of Solar system: ${planet.partOfSolarSystem}`)

    if (planet.water) {
      console.log(`Earth's water content: ${planet.water}`)
    }
  }
```
```text
Property 'water' does not exist on type 'Planet'.
  Property 'water' does not exist on type 'Saturn'.(2339)

Property 'water' does not exist on type 'Planet'.
  Property 'water' does not exist on type 'Saturn'.(2339)
```

TypeScript throws two type errors, both looking oddly the same. Let's go step by step and address the first one. The error originates on `line:6`. We are trying to access `planet.water`, there is nothing wrong about that. In plain Javascript, this would be fine, we would get `undefined` (a `falsy` value) and no log would be emitted.

However, TypeScript recognizes this as an error. Why? Object types in TypeScript ensure that the object has the properties of the type that are declared, but that does not mean, it cannot have any other properties. In other words, the fact, that `Saturn` does not declare `water` property means that at run-time, it can potentially be assigned the property, with any type, which could yield a run-time error.

There are couple ways to resolve our issue.

### Specify the type declaration

The easiest solution to this would be specifying the type declaration itself. When we explicitly declare that Saturn does not have water with `water: undefined`, we reach exhaustiveness. The compiler now knows that type `Planet` has type `water: Water | undefined`.

```typescript
  type Saturn = {
    partOfSolarSystem: true,
    ring: Ring
    water: undefined // <= The change
  }
```

That's semantically correct, however not always possible. The type might be a third-party (generated) library or API definition, in which case, our hands are tied and this solution is a no-go.

### Assert the type in place

We can leave the type as is and assert the type in place in our `logPlanetInformation` function.

```typescript
const logPlanetInformation = (planet: Planet): void => {
  console.log(`Part of Solar system: ${planet.partOfSolarSystem}`)

  if ((planet as Earth).water) { // <= The change
    console.log(`Earth's water content: ${planet.water}`)
  }
}
```
```text
Property 'water' does not exist on type 'Planet'.
  Property 'water' does not exist on type 'Saturn'.(2339)
```

This gets us rid of only one error though. How come?

https://github.com/microsoft/TypeScript/issues/12815#issuecomment-266250230

### Use Type Guards

A Type Guard is a general Typescript construct that helps you narrow (specify) a type.

