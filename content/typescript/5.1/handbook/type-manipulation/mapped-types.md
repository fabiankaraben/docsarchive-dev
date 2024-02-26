---
linkTitle: "Mapped Types"
title: "TypeScript: Documentation - Mapped Types"
description: "Generating types by re-using an existing type."
weight: 7
type: docs
---

# Mapped Types

When you don’t want to repeat yourself, sometimes a type needs to be based on another type.

Mapped types build on the syntax for index signatures, which are used to declare the types of properties which have not been declared ahead of time:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAEg9gJwM7QLxQN4F8DcAoAegKgFoyBjAV2DJL1EigHkA7AGxACE442kBBFgBN4yCEijoMeKFADaAawggAXFCTAEASxYBzALpqARjzYQAhiygAfWIhT5cePOTgsNUVywBmiALZIaqwc3LwCwqIoElIyUEIQbGqalBAANLEIcEIsymre5nxpeLhAA)

```ts
type OnlyBoolsAndHorses = {
  [key: string]: boolean | Horse;
};

constconforms: OnlyBoolsAndHorses = {
del:true,
rodney:false,
};
```

A mapped type is a generic type which uses a union of `PropertyKey`s (frequently created [via a `keyof`](/typescript/5.1/handbook/type-manipulation/indexed-access-types)) to iterate through keys to create a type:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA8mwEsD2A7AzgMQDYEMDmaAPACrgQB8UAvFAN4BQUUA2gAoBOSk7oUCKUANYQQSAGZRSkALoAuKACMkSLBBwoA3PQC+GoA)

```ts
type OptionsFlags<Type> = {
  [PropertyinkeyofType]: boolean;
};
```

In this example, `OptionsFlags` will take all the properties from the type `Type` and change their values to be a boolean.

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA8mwEsD2A7AzgMQDYEMDmaAPACrgQB8UAvFAN4BQUUA2gAoBOSk7oUCKUANYQQSAGZRSkALoAuKACMkSLBBwoA3PQC+WgPR6oAWhMBjAK7ATR+qEhQMa4OfYQ01OoygATHO0EAskjeEPIAFACU1JQAbkgI3lpMKBAA7gCqaBDsHOIIquFRVLHxiTpatmQOTi4QcIio7jT1yOjY+ESOOM6uaOT6hkwAegD8QA)

```ts
type Features = {
darkMode: () =>void;
newUserProfile: () =>void;
};

typeFeatureOptions = OptionsFlags<Features>;

type FeatureOptions = {
    darkMode: boolean;
    newUserProfile: boolean;
}
```

### Mapping Modifiers {#mapping-modifiers}

There are two additional modifiers which can be applied during mapping: `readonly` and `?` which affect mutability and optionality respectively.

You can remove or add these modifiers by prefixing with `-` or `+`. If you don’t add a prefix, then `+` is assumed.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAECUFMFsHsDdIGdQHIBOkCGATWA7AGwE9VQsAXC9ASwCMBXC5UAM3VmnNAuIAdIqFHw4D0FGsgBQvAaADCmSpACyTLHUKQAPABV+kAHygAvKADeU0KAC0SvEWKgA2gAVRkcU5r5QAa0hiWFZQfQEAXQAuUIM3Dy9wgG4pAF9kmQNQABlYAGMAnABBXNzYBnwKUwsrUHsCElAaHGikah8Ac2TrOsdQfCxoSBa2-E7U9NlIUABVIjyC4tLyyrNFbGY1Cg0tbRz8yCKSsorDZJBrUAA9AH4gA)

```ts
// Removes 'readonly' attributes from a type's properties
typeCreateMutable<Type> = {
  -readonly [PropertyinkeyofType]: Type[Property];
};

typeLockedAccount = {
readonlyid: string;
readonlyname: string;
};

typeUnlockedAccount = CreateMutable<LockedAccount>;

type UnlockedAccount = {
    id: string;
    name: string;
}
```

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAECUFMFsHsDdIGdQHJYAcAuBLWA7AQwBtVRCssAnHAIwFctlQAzK2ac0LATw0lQoM7flVzIAUL36gAwgQDGVSEwA8AFT6QAfKAC8oAN4TQoANoAFEZDE9QOfKADWkHrBahN-ALoBaAPwAXJ5alta23gDcEgC+0VJaoACyhDy0kACqSDb6Rib2ACbBSNQOAObRpkTQkEGgJTT4FfmEZbXB+PTQ6VTRcRIJMlk5BvL4SiqQqilpmdlU2tEgpqAAev5AA)

```ts
// Removes 'optional' attributes from a type's properties
typeConcrete<Type> = {
  [PropertyinkeyofType]-?: Type[Property];
};

typeMaybeUser = {
id: string;
name?: string;
age?: number;
};

typeUser = Concrete<MaybeUser>;

type User = {
    id: string;
    name: string;
    age: number;
}
```

## Key Remapping via `as` {#key-remapping-via-as}

In TypeScript 4.1 and onwards, you can re-map keys in mapped types with an `as` clause in a mapped type:

```ts
type MappedTypeWithNewProperties<Type> = {
    [PropertiesinkeyofTypeasNewKeyType]: Type[Properties]
}
```

You can leverage features like [template literal types](/typescript/5.1/handbook/type-manipulation/template-literal-types) to create new property names from prior ones:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA4hzAgJwM4B4Aq4ID4oF4oBvAKCnKgG0AFJAe0iVCgEsA7KAawhDoDMoWSFACGKKAAMA5vAAkRAMIiwLYCIA2LAF4Q0KYEnZSoAMii0GyUDgC+EgLoAuKAAoAlATxCINeo1D2JDYA3CQk7IhIfCIAxtDUyCh0HKQUUGwiALYQzvqGbFKhaSIyzmwArpkARshFFOp0MSLALMm5BkahNmGgwgAyIlogCajJBLDwkegjSWw4oQD0CxQAegD8QA)

```ts
type Getters<Type> = {
    [PropertyinkeyofTypeas`get${Capitalize<string&Property>}`]: () =>Type[Property]
};

interfacePerson {
name: string;
age: number;
location: string;
}

typeLazyPerson = Getters<Person>;

type LazyPerson = {
    getName: () => string;
    getAge: () => number;
    getLocation: () => string;
}
```

You can filter out keys by producing `never` via a conditional type:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAECUFMFsHsDdKgC4AskHIDWBLAdgCYagAOATrCZGcgJ4BQdVEMCkA0vgQGI6QA2BADwAVWlQB8oALygA3vVBLQAbQAKFKjVqh8oLJFqwAZqDHMAhgGdQAUQAeAY34BXApCEbK1OgBpQAES4hAESALoAXGbikOqaPrRh9AC+ANz09PjI1MYWjkgAwjhkzkgKyvpcUQGOxaUB6RVkFgQ4LlZReC7QAEbU6ckZTEichPyQVlZFJeMyLHCIozx8gkLTpRLpIMoAegD8QA)

```ts
// Remove the 'kind' property
typeRemoveKindField<Type> = {
    [PropertyinkeyofTypeasExclude<Property, "kind">]: Type[Property]
};

interfaceCircle {
kind: "circle";
radius: number;
}

typeKindlessCircle = RemoveKindField<Circle>;

type KindlessCircle = {
    radius: number;
}
```

You can map over arbitrary unions, not just unions of `string | number | symbol`, but unions of any type:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAogbhAdsAwge0QMwJYHMA88SwAzlBAB7BIAmZA3lANbaI0BcUJwATq7lAC+APigBeKPQBQUWVADaMKK1gJkZAIZkY8gEQs2ugLpHOACghrgnGAEpxouGmw0A3FMFSpoSFADKAI4Arho8EETI4pLMrBxQuiTBoRC6ADRQFJyIQQC2AEYQPOkgWbkFPELuPtAo2DwAxgA24VZRjAZxuvV1TSnpPBo02EEkpfmFlV7VUOhYeFERqBg4BIEhYYtQAD4zPc2LwlIA9EdyAHoA-EA)

```ts
type EventConfig<Events extends { kind: string }> = {
    [EinEventsasE["kind"]]: (event: E) =>void;
}

typeSquareEvent = { kind: "square", x: number, y: number };
typeCircleEvent = { kind: "circle", radius: number };

typeConfig = EventConfig<SquareEvent | CircleEvent>

type Config = {
    square: (event: SquareEvent) => void;
    circle: (event: CircleEvent) => void;
}
```

### Further Exploration {#further-exploration}

Mapped types work well with other features in this type manipulation section, for example here is [a mapped type using a conditional type](/typescript/5.1/handbook/type-manipulation/conditional-types) which returns either a `true` or `false` depending on whether an object has the property `pii` set to the literal `true`:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAogHsATgQwMbAAoEksB4Aq4EAfFALxQDeAUFFANoaID2kioUAlgHZQDWEEMwBmUQpAC6ALjFFGLNqAlQICCNwAmAZypQwnTjKQBXaAF8oAfignoM4cgA2WiAG5qZ99VCQoAEQAhADFOCEdtcipaLg0ZSihhZkQAW2RgGQAiHlRECGT1YB4AcwyoT2juZHy4myIZLSRi1z0DI0RTMvdy7yIoAHkAIwArCHQtADkICA1igHE-DAAlPzCIQuZeCngkNEwcXECQsO1idwB6M7ooAD1LIA)

```ts
type ExtractPII<Type> = {
  [PropertyinkeyofType]: Type[Property] extends { pii: true } ? true : false;
};

typeDBFields = {
id: { format: "incrementing" };
name: { type: string; pii: true };
};

typeObjectsNeedingGDPRDeletion = ExtractPII<DBFields>;

type ObjectsNeedingGDPRDeletion = {
    id: false;
    name: true;
}
```
