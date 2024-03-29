---
linkTitle: "Mixins"
title: "TypeScript: Documentation - Mixins"
description: "Using the mixin pattern with TypeScript"
weight: 8
type: docs
---

# Mixins

## How Does A Mixin Work? {#how-does-a-mixin-work}

The pattern relies on using generics with class inheritance to extend a base class.
TypeScript’s best mixin support is done via the class expression pattern.
You can read more about how this pattern works in JavaScript [here ↗](https://justinfagnani.com/2015/12/21/real-mixins-with-javascript-classes/).

To get started, we’ll need a class which will have the mixins applied on top of:

[Try this code ↗](https://www.typescriptlang.org/play#code/MYGwhgzhAEDKAOAnAlgFwKbQN4ChrQDswBbTAXmgCJKBuPaAD2goAY78BPZ6Nne4APYEIqRAFdgqAYgAURUgC5oIlAQDmASmz18qABbIIAOnnlCJdO2gBfHNaA)

```ts
class Sprite {
name = "";
x = 0;
y = 0;

constructor(name: string) {
this.name = name;
  }
}
```

Then you need a type and a factory function which returns a class expression extending the base class.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEBUHtQcwUwC6gM4IIYCcFwCYBpQB3OUAOzl1DVAQE8AHEwgCwEsBjZouAcgBs+oAK7ISCaHAAe2UjgBQIUJATM4GUOz5pko5KABmGSAFsAdBFWhjaVqVAY4yepFLJWAI1Z9WdUKz3ioDhwmphwCmAqaEgqYgwk7nC2MKD02qI4fnb+VBpaOqZycnSMoADCLqgYQuzi6gC8ZHCEoAAUph2YMMgAXFSktADaALoAlKD1AHygAN4AvgDcRYrgbHrGrJK2VDg4etTI7Gh8JPRGjFi0BIQ+XPAI2Bj7siiIj8gRBpDqnGikMMk-Egbip+qA4KQjvRkEItNhMmdWAA3aKnc5qOg9Ir6ISQhCsFygADKRxOAB5wAAhbQkKQyPblSoIaq1b6TVrU0R9Kk08YzOSgeyIIQYOyhHTE0mAukQhmckj8wWCxQAWU2tnWaFoZGUQRCWgcqQwyNRwDOyhC8KNkAu+McAqVimYkGISLUBFokCEGj+wlEoAAokSAEwABjDRpN2AMrDgfD2DsFAH1DscSI0AIxLROvBAktOtVMnPqkITGRIYPk5wUqfymFOk9MoRtLJWgOZFNv3ZsF0YlssV2bVoUIEV2WvIetFuCtpUdwWLORzIA)

```ts
// To get started, we need a type which we'll use to extend
// other classes from. The main responsibility is to declare
// that the type being passed in is a class.

typeConstructor = new (...args: any[]) => {};

// This mixin adds a scale property, with getters and setters
// for changing it with an encapsulated private property:

functionScale<TBaseextendsConstructor>(Base: TBase) {
returnclassScalingextendsBase {
// Mixins may not declare private/protected properties
// however, you can use ES2020 private fields
_scale = 1;

setScale(scale: number) {
this._scale = scale;
    }

getscale(): number {
returnthis._scale;
    }
  };
}
```

With these all set up, then you can create a class which represents the base class with mixins applied:

[Try this code ↗](https://www.typescriptlang.org/play#code/MYGwhgzhAEDKAOAnAlgFwKbQN4ChrQDswBbTAXmgCJKBuPaAD2goAY78BPZ6Nne4APYEIqRAFdgqAYgAURUgC5oIlAQDmASmz18qABbIIAOnnlCJdO2gBfHLdQd4mAMJCVEqYm4F0Ad2gyRkFgiGoQSmAEHADaALpaZAB82NZ0AGZiBJLIQnDAYCDoADwAKgBCkJjoDBgEACYwrsKiHtKJMhUQ6ErllVq4+IjoqGKIBNCgkDCw+SDI6tDVtQ3QnZgD+NAA9FvQALLIDPMwxGBcBAKo0HXok0PQSMgAbmAYW0iXtxh1D4gCTohUMh0BAdNtdnoBL50E90IgADTQDgCMQTSLQMRdaAAUVgACYWATfs9Xpg0sCQA0wQB9CCzMwARjoYK6qBmBXQMjpHKUBDExAARnD+mDdAZjLT6dxuYUrPhbGC1MNlPSZBpefyhV4NptoEMRmNoPpDEZJRy5TZ6Kk7DgdtAALSO4BiVCO+223auYjwARYsCEPwTcBQaBpP7EI16TAIFAYINTeEe6C+NB6SOYA5HcbswrQMDweBzOEKHCCZo45BqPSoMpoGNoMw5zn1jAaZllkSh8AFjgtsw+fzYyvV2tsx4YGSUWuIOqUNs4NLd+C98foIyspsyFhGAAc847AkKRhAAjUMkX+eXffX9LbQA)

```ts
// Compose a new class from the Sprite class,
// with the Mixin Scale applier:
constEightBitSprite = Scale(Sprite);

constflappySprite = newEightBitSprite("Bird");
flappySprite.setScale(0.8);
console.log(flappySprite.scale);
```

## Constrained Mixins {#constrained-mixins}

In the above form, the mixin’s have no underlying knowledge of the class which can make it hard to create the design you want.

To model this, we modify the original constructor type to accept a generic argument.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEBUAsEsGdQO4EN4HsCuAnUAHTBTAN2g3gGNUA7WAF03TJtUwC4AoGgTx31AGEqteo2agAvKEr4EoABQA6RUkwBzWC1BJKnANoBdAJTiAfKADeAXwDcbEKAByqGQl7pYvJKBX4pmaGVBCfExYEkpEGDJIUDItTRwcABtOTRjBOiRoShpQKlswGkheMkSUeARI6MK4UABbaAAPLNAapATE6HwAE1AmDm5eAHEBajoGJkwAHnBxcwtTCSkZBSVVdU1tfSMxU3ArIA)

```ts
// This was our previous constructor:
typeConstructor = new (...args: any[]) => {};
// Now we use a generic version which can apply a constraint on
// the class which this mixin is applied to
typeGConstructor<T = {}> = new (...args: any[]) =>T;
```

This allows for creating classes which only work with constrained base classes:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA4gwgewHYGdgCcCuBjYD0A8AKlALxQDeAvgHxlRIQDuUAFAHScCG6A5igC4oXJCADaAXQCUZOkQDcAKGwAbLihRQAymHQBLYNAqKoDLgFto5AETWlpgB70ADPaggXSk1GzI0WXHxWJAsIIX89JF4ZY1NTYAALPRR2EMt6NIg3KkUcgHo8qABaEuxMYBKixVBIKAAFBBQDPWQuACMVK1hEVAwcPEIKKBQIYAbBNgchJExzNoh0ABp3adn59BlSOgA3BD0AEyhaJRroHX1gds76eD8+wMJzgwgaE-BoABkEXl4rrtvegEBgQhrpIsAhKxNjs9odjkA)

```ts
type Positionable = GConstructor<{ setPos: (x: number, y: number) => void }>;
typeSpritable = GConstructor<Sprite>;
typeLoggable = GConstructor<{ print: () =>void }>;
```

Then you can create mixins which only work when you have a particular base to build on:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA4gwgewHYGdgCcCuBjYD0A8AKlALxQDeAvgHxlRIQDuUAFAHScCG6A5igC4oXJCADaAXQCUZOkQDcAKGwAbLihRQAymHQBLYNAqKoDLgFto5AETWlpgB70ADPaggXSk1GzI0WXHxWJAsIIX89JF4ZY1NTYAALPRR2EMt6NIg3KkUc0EgoAAUEFAM9ZC4AIxUrWERUDBw8QgooFAhgYsE2ByEkTHNKiHQAGnc+gaH0GVI6ADcEPQATKFolfOgdfWAqmvp4P0bAwi2DCBp18GgAGQReXl3ag4aA5oJW3UjgIVYZ+cWVmtFAB6YFQAC0kOwmGAkPBikUADNMEhcOUkFAAFIDMCPYgAIXU0AgDkMSCWmi6ZQq1XOrEJ7SERAZEBi3nQHUw6AxqnUmmx5lxtKgJLJFKgLMo3lMACscb8pXE4qCoEQkppzHoHJEoEw9CoVFBkCoPEx8ABrKB6RFW4BWzS4jQQFZcKCVInS5Vg3kaXVJbAJKAJdRtDpdKBLCCIyLOt0QbBcTDtI02xIQT2mFVU4Dox4+Q7oLhfdgZqCJZLsdqdEqsZxjABMzikblMOVbSioQA)

```ts
function Jumpable<TBase extends Positionable>(Base: TBase) {
returnclassJumpableextendsBase {
jump() {
// This mixin will only work if it is passed a base
// class which has setPos defined because of the
// Positionable constraint.
this.setPos(0, 20);
    }
  };
}
```

## Alternative Pattern {#alternative-pattern}

Previous versions of this document recommended a way to write mixins where you created both the runtime and type hierarchies separately, then merged them at the end:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFAigCimiAFqALbwAe8AdqPJKJqHJgCbzTwB7RjlIBlUImyZIkApOlsAUgFdqAB0wAjbHlABvAqFAArVWoAUASn0BfAnblSZoACLLEAay079h0N3cPK1t7AiIwAElGSWVeRgBzTgpdTWlCeWdRNQRoXQMjOlAAXlAABgBuPwBPYrLKh2IAFWTmKoFlCVg8TFz2ZiZc2CxEXQB3CiQqajxYeLxZYmhk0Dw6NTxkPG4aeiY2Ub4qJd1ITGnQYXPpUDblWFBU-AIBmeHdLJzdVdzGbiUzbx4AA0rkCgJCxAAgmo1NgascdgxGGwBgIkik0hInGwAG7wTDhdGgRTiHqgWDKRj8aYETAwuEAWV2yPMHz4wNAAG0VOpASC3J5AQBdSyVAg6aCgWGYKozWqMPCjUBs3JWSrS2WwAB0pnUarkQkgAh0WuwAni5g1My1dBBVu1VVFYSaEzYiEwzGw8Bxug9VXGM10TBu7XuiAE3DwjzwKAI6EpyEEzDpsKqTKRkHMkYQPu4AGFoAJYGg-SDw8i4O5C7BICXGFVOSLfEZyzAKcgi5Ategi2RKOZzNGC0XrEUAHzNoygADymmMG2gWrm0Gno0YAAVYAJ1rBoFUAHJneaDtLD7XZASFvfrSzd3vkCgDy54UcT-JToyz+fILWR9BMPBN23GY93MPwPyMbNvS2M8tQvK8qnWIFwIg59kIgqcvwXJc8BXNcgJ3PcXHmRAEDUasT3wWD4MvRCOWfawAB9GJQjCsJ-UjulVRhlGwbBLFY0AnQ-GxhNAUT6iAA)

```ts
// Each mixin is a traditional ES class
classJumpable {
jump() {}
}

classDuckable {
duck() {}
}

// Including the base
classSprite {
x = 0;
y = 0;
}

// Then you create an interface which merges
// the expected mixins with the same name as your base
interfaceSpriteextendsJumpable, Duckable {}
// Apply the mixins into the base class via
// the JS at runtime
applyMixins(Sprite, [Jumpable, Duckable]);

letplayer = newSprite();
player.jump();
console.log(player.x, player.y);

// This can live anywhere in your codebase:
functionapplyMixins(derivedCtor: any, constructors: any[]) {
constructors.forEach((baseCtor) => {
Object.getOwnPropertyNames(baseCtor.prototype).forEach((name) => {
Object.defineProperty(
derivedCtor.prototype,
name,
Object.getOwnPropertyDescriptor(baseCtor.prototype, name) ||
Object.create(null)
      );
    });
  });
}
```

This pattern relies less on the compiler, and more on your codebase to ensure both runtime and type-system are correctly kept in sync.

## Constraints {#constraints}

The mixin pattern is supported natively inside the TypeScript compiler by code flow analysis.
There are a few cases where you can hit the edges of the native support.

#### Decorators and Mixins [`#4881` ↗](https://github.com/microsoft/TypeScript/issues/4881) {#decorators-and-mixins-4881}

You cannot use decorators to provide mixins via code flow analysis:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMA8AdIE4EsC2kB2AXAhgGwCKQDGA9gtpmQM4BQIEiC1AXKAEwDMHAnHWAIKgAJsTIUyoAGYBXdEUxIS6UAHcAFkiJrQCSLFyaKkKqExrIoFEmhJlsCpkTpmNUuiqZQABWzSq2ACNcCwBeUAAKHAQAc0hMVkwAT3gSSW9cbETEAEpQEIA+UABvGlAdOOkEZSIMqhMfP0Dg0BhHdCETKNjPErKyqjUSaVwhADFdSAAvUKk8KkgAblLQAF8ltZoacAb-IMhXWvqMrIRi5eg80AAGJbLEy5uaFc36ABVzdMzEUBrsOuESMZQOgSJ41NgAG4WMwWESkciUBAAck6yQsaBikCELjcHlA+i+pzC6EgKk+J3C2SWBJOADoBkMRuNIFNFi8wO8LAhZAo0KA-vB5D9GUJQAF0dh0NI8Lh7rp9IZHKKIUhsHxTGjhShYCQqEgFEpQBJbI4EJJsER0YhorZorSaEl4KBmVMmpAvMdvmEPYTQAAyYqgBnDMYTaasAIkEjBSWrJauJR4mmIV4qEiXcIksk+im5P6gWQAaxBKnQeZMLsmbpziGpnoQqZI9MGIcriyAA)

```ts
// A decorator function which replicates the mixin pattern:
constPausable = (target: typeofPlayer) => {
returnclassPausableextendstarget {
shouldFreeze = false;
  };
};

@Pausable
classPlayer {
x = 0;
y = 0;
}

// The Player class does not have the decorator's type merged:
constplayer = newPlayer();
player.shouldFreeze;

// The runtime aspect could be manually replicated via
// type composition or interface merging.
typeFreezablePlayer = Player & { shouldFreeze: boolean };

constplayerTwo = (newPlayer() asunknown) asFreezablePlayer;
playerTwo.shouldFreeze;
```

```text {filename="Generated error"}
Property 'shouldFreeze' does not exist on type 'Player'.
```

#### Static Property Mixins [`#17829` ↗](https://github.com/microsoft/TypeScript/issues/17829) {#static-property-mixins-17829}

More of a gotcha than a constraint.
The class expression pattern creates singletons, so they can’t be mapped at the type system to support different variable types.

You can work around this by using functions to return your classes which differ based on a generic:

[Try this code ↗](https://www.typescriptlang.org/play#code/GYVwdgxgLglg9mABAIwIYGcCmAeAKgPgAoBKRAbwChFEIAbDdRAIQ03KusXSlVgkQAOAJzgCAXIlwBuDgF8OQzFBBCkLLDPkVQkWAkQATTEJgA3TAbxFSlanQaIAIsbMXEmAB5RMYA4zRYViTsnFw8fIioYHBQABbGAAoi4pIy1PLUisqqTi7mBpoUFPbojADKApj8nt6+jEYm+djcJmAA5tbkWhVVAHTColKIAPTDYa1tFD0QvVEx8UJJgyNjLTDtQA)

```ts
function base<T>() {
classBase {
staticprop: T;
  }
returnBase;
}

functionderived<T>() {
classDerivedextendsbase<T>() {
staticanotherProp: T;
  }
returnDerived;
}

classSpecextendsderived<string>() {}

Spec.prop; // string
Spec.anotherProp; // string
```
