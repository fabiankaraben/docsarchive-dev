---
linkTitle: "Template Literal Types"
title: "TypeScript: Documentation - Template Literal Types"
description: "Generating mapping types which change properties via template literal strings."
weight: 8
type: docs
next: /typescript/5.1/handbook/classes
---

# Template Literal Types

Template literal types build on [string literal types](/typescript/5.1/handbook/everyday-types#literal-types), and have the ability to expand into many strings via unions.

They have the same syntax as [template literal strings in JavaScript ↗](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals), but are used in type positions.
When used with concrete literal types, a template literal produces a new string literal type by concatenating the contents.

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA6g9gJwDYBMoF4oCIDujVYDcAUMaJFAOIIQTACWAdgOYZQAGAFhEknFABIA3vGQoAvuxIB6aVHkA9APxA)

```ts
type World = "world";

typeGreeting = `hello ${World}`;

type Greeting = "hello world"
```

When a union is used in the interpolated position, the type is the set of every possible string literal that could be represented by each union member:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAogtgQwJYBsAyB7AxglECSAIgM5QC8UARAO4QpYZwQD6EiqlUAPlW8iswAWEBABMkAOwDmlANwAoUJCgAxDBmAQATphx4ipCpQBm6zVubAkwPJx4mz25sQgTRGY8bnzF4aAEEUdGxcAhJyKAADABIAb3h+XVCDblVHHRD9EgBfZiRRSIUAeiKoMoA9AH4gA)

```ts
type EmailLocaleIDs = "welcome_email" | "email_heading";
typeFooterLocaleIDs = "footer_title" | "footer_sendoff";

typeAllLocaleIDs = `${EmailLocaleIDs|FooterLocaleIDs}_id`;

type AllLocaleIDs = "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"
```

For each interpolated position in the template literal, the unions are cross multiplied:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAogtgQwJYBsAyB7AxglECSAIgM5QC8UARAO4QpYZwQD6EiqlUAPlW8iswAWEBABMkAOwDmlANwAoUJCgAxDBmAQATphx4ipCpQBm6zVubAkwPJx4mz25sQgTRGY8bnyA9D6gAtEFYAK7AQQGK4NAAgijo2LgEJORQAAYAJADe8Py6SQbcqo46ifokAL7MSKJpCkrQaAjSqZSudlQAVggdlGDA3lHK+XgAshDExAhSyYbp2U3SVdlxCXqzFXW+-lBQAHoA-EA)

```ts
type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
typeLang = "en" | "ja" | "pt";

typeLocaleMessageIDs = `${Lang}_${AllLocaleIDs}`;

type LocaleMessageIDs = "en_welcome_email_id" | "en_email_heading_id" | "en_footer_title_id" | "en_footer_sendoff_id" | "ja_welcome_email_id" | "ja_email_heading_id" | "ja_footer_title_id" | "ja_footer_sendoff_id" | "pt_welcome_email_id" | "pt_email_heading_id" | "pt_footer_title_id" | "pt_footer_sendoff_id"
```

We generally recommend that people use ahead-of-time generation for large string unions, but this is useful in smaller cases.

### String Unions in Types {#string-unions-in-types}

The power in template literals comes when defining a new string based on information inside a type.

Consider the case where a function (`makeWatchedObject`) adds a new function
called `on()` to a passed object.  In JavaScript, its call might look like:
`makeWatchedObject(baseObject)`. We can imagine the base object as looking
like:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDsHsFECd7XgZwFAGNqRQF1AA4CGKKApgCYDyARgFZkb4C8oA3mqKAGYCWquAHJEAtmQBcoAEQBlItH7kpAGk6gANiSGiJ0gErYikFWqIBzXQCYAbKoC+AbiA)

```ts
const passedObject = {
firstName:"Saoirse",
lastName:"Ronan",
age:26,
};
```

The `on` function that will be added to the base object expects two arguments, an `eventName` (a `string`) and a `callback` (a `function`).

The `eventName` should be of the form `attributeInThePassedObject + "Changed"`; thus, `firstNameChanged` as derived from the attribute `firstName` in the base object.

The `callback` function, when called:

- Should be passed a value of the type associated with the name `attributeInThePassedObject`; thus, since `firstName` is typed as `string`, the callback for the `firstNameChanged` event expects a `string` to be passed to it at call time. Similarly events associated with `age` should expect to be called with a `number` argument
- Should have `void` return type (for simplicity of demonstration)

The naive function signature of `on()` might thus be: `on(eventName: string, callback: (newValue: any) => void)`. However, in the preceding description, we identified important type constraints that we’d like to document in our code. Template Literal types let us bring these constraints into our code.

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEDsHsFECd7XgZwFABMCmBjANgIbxagBmArpDgC4CW0koAtgQNZYDqB1OAFlhgDyAIwBWuagApoYgFygCkAJ4BKeYqUBuNCFABaAznLUDetDgYpqoAA5ZUDUAF5mbTtz4CR4mpIDeaKBktKjUAHIETFjyAEQAygTQIShYMQA0gaCEVhFRsQBKDIrpmQQA5tGgAEwAbBkAviraOmAs7Fw8-EJiEqC8BCgKGNgYoAAGDGOg1NDT-AowykzQ5IPeEmhodg6QAHQMkjGkyeGRWADC-ZAVGOmgkpBYAO4AagR45FgqzgB8oAFBCyQFDQPBYXZ4aBlSRjY6hXIkJ4DUB8RQ3aazAAkfker3en3qAEIxk00I1NEA)

```ts
const person = makeWatchedObject({
firstName:"Saoirse",
lastName:"Ronan",
age:26,
});

// makeWatchedObject has added `on` to the anonymous Object

person.on("firstNameChanged", (newValue) => {
console.log(`firstName was changed to ${newValue}!`);
});
```

Notice that `on` listens on the event `"firstNameChanged"`, not just `"firstName"`. Our naive specification of `on()` could be made more robust if we were to ensure that the set of eligible event names was constrained by the union of attribute names in the watched object with “Changed” added at the end. While we are comfortable with doing such a calculation in JavaScript i.e. `Object.keys(passedObject).map(x => `${x}Changed`)`, template literals *inside the type system* provide a similar approach to string manipulation:

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBACgTgezAUQG4QHbAMoIK5wDGEAPACrgQB8UAvFAN4BQUrUCGAFBOlgHIBDALYQAXFAAGAEgYBnYHACWGAOZQAZFADWEEAgBmUCpAC+AYQAWA1RAAmEgDRRCAgDauARgMJbxnDBAA7gBqbnhiUNYgAJR0NKgIirbR4glJANxMJplMAPT5UGZwEALA0AJQAESBpYQWduweAFYQhMCVUIGKwBaRGJIcElAiPQi2eQWyCFA9pVB6eM7WnbW9+ghwzlY2sjPTYIiQcMCKELIAdEy2ra4CxVD6eBhtihzDAjoA6qt2APLNrWA5EoVE4CGa4mMEBSRkoGlghzQmBw+CIpChVHSQA)

```ts
type PropEventSource<Type> = {
on(eventName: `${string&keyofType}Changed`, callback: (newValue: any) =>void): void;
};

/// Create a "watched object" with an `on` method
/// so that you can watch for changes to properties.
declarefunctionmakeWatchedObject<Type>(obj: Type): Type & PropEventSource<Type>;
```

With this, we can build something that errors when given the wrong property:

[Try this code ↗](https://www.typescriptlang.org/play#code/PTAEAEFMCdoe2gZwFygEwGYAsBWAUAC4CeADpKAArwkCiAbpAHYEDKcArtAMaQA8AKqUgA+UAF5QAbzyhZoOIwAUkBswByAQwC2kVAAMAJJMQFoAS0YBzUADJQAa0hE4AM1CCyAXwDCACw1WkAAmegA0oFwaADZRAEYaXPaoioyQAO4AatHsuqABRACU4qJ0cGZBBail5QDceJ51eEGQXFEa0OQu7IxcBGYKoFoajgDqGgRcvsEA8rEAVi0EAsKKcPOo-JXutpTU9EysHNx8-MJ1IKAAtNdc7ATXl3hcCiagZEgDEkOj45Mz84tFNJZC4zEgCJodKgAEQsDRlJCQaGhGSgNomSG5aEAJQUAWRqI0llyaAAbPUCo13ogFAA6BSKaGg8GYvwBYlBZGgRRFMSiSSeSl4PAXKgqA6gSAaRBEUC+dhDRiS2AIbnsRAWawEKYOJygCwmKVBeRubXkcXMUCMbSQAp4al0hlMsEYm1cnnFKSCxoXACSBAA5IhQMQSHBLh0NSYAgR7TAaYx6UomSybWzApzwh6+V7KUA)

```ts
const person = makeWatchedObject({
firstName:"Saoirse",
lastName:"Ronan",
age:26
});

person.on("firstNameChanged", () => {});

// Prevent easy human error (using the key instead of the event name)
person.on("firstName", () => {});

// It's typo-resistant
person.on("frstNameChanged", () => {});
Argument of type '"frstNameChanged"' is not assignable to parameter of type '"firstNameChanged" | "lastNameChanged" | "ageChanged"'.2345Argument of type '"frstNameChanged"' is not assignable to parameter of type '"firstNameChanged" | "lastNameChanged" | "ageChanged"'.
```

```text {filename="Generated error"}
Argument of type '"firstName"' is not assignable to parameter of type '"firstNameChanged" | "lastNameChanged" | "ageChanged"'.
```

### Inference with Template Literals {#inference-with-template-literals}

Notice that we did not benefit from all the information provided in the original passed object. Given change of a `firstName` (i.e. a `firstNameChanged` event),  we should expect that the callback will receive an argument of type `string`. Similarly, the callback for a change to `age` should receive a `number` argument. We’re naively using `any` to type the `callback`’s argument. Again, template literal types make it possible to ensure an attribute’s data type will be the same type as that attribute’s callback’s first argument.

The key insight that makes this possible is this: we can use a function with a generic such that:

1. The literal used in the first argument is captured as a literal type
2. That literal type can be validated as being in the union of valid attributes in the generic
3. The type of the validated attribute can be looked up in the generic’s structure using Indexed Access
4. This typing information can *then* be applied to ensure the argument to the
  callback function is of the same type

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBACgTgezAUQG4QHbAMoIK5wDGEAPACrgQB8UAvFAN4BQUrUCGJA0hCFBAA9gmACYBnKGOBwAlhgDmUAGRQA1rwQAzKBUhUWbQwAoI6LADkAhgFsIALigADACQMeIAL4BhABaWFECKOADRQhJYANhEARpaEqg5GGBAA7gBqkXj2OpQA2u4AugCUdDSoCDIiRQ7llQDcTB4NTCIQhBGWcNCaeBiEwDIcUNaW6gDqlsCEPoEA8tEAVm3A5JRURgiLDroQ1TmQyrCIKGY4+ESkO1TNhBxSUJBwYkP0I+OT03OLy0bMrJoyJ7AKy2BwAImwlgqTwgYOCBg6UhB2TBACUOP44QZLPJsgAmABsjSKzUezwwADoOEYwQCgcjfP5cSI4VBkilkaVGAZWAB6XmGQVC4WsAB6AH4eWE7ggIhAKREEPIjI52WybNAZBJXOzkRTgAgAKpgR5eSxiCBGIoeRwk4mkiBPDhUjA0nEQRkBFmhdkAQVxXL+bH5ItDUAlUpk2iSqX90BIUAADCUg0LbhhnnKKSlOq6wTm4Bg5PIAIRsiDySYydBQd1gu2GDzEoA)

```ts
type PropEventSource<Type> = {
on<Keyextendsstring & keyofType>
        (eventName: `${Key}Changed`, callback: (newValue: Type[Key]) =>void): void;
};

declarefunctionmakeWatchedObject<Type>(obj: Type): Type & PropEventSource<Type>;

constperson = makeWatchedObject({
firstName: "Saoirse",
lastName: "Ronan",
age: 26
});

person.on("firstNameChanged", newName=> {

(parameter) newName: string
console.log(`new name is ${newName.toUpperCase()}`);
});

person.on("ageChanged", newAge=> {

(parameter) newAge: number
if (newAge < 0) {
console.warn("warning! negative age");
    }
})
```

Here we made `on` into a generic method.

When a user calls with the string `"firstNameChanged"`, TypeScript will try to infer the right type for `Key`.
To do that, it will match `Key` against the content before `"Changed"` and infer the string `"firstName"`.
Once TypeScript figures that out, the `on` method can fetch the type of `firstName` on the original object, which is `string` in this case.
Similarly, when called with `"ageChanged"`, TypeScript finds the type for the property `age` which is `number`.

Inference can be combined in different ways, often to deconstruct strings, and reconstruct them in different ways.

## Intrinsic String Manipulation Types {#intrinsic-string-manipulation-types}

To help with string manipulation, TypeScript includes a set of types which can be used in string manipulation. These types come built-in to the compiler for performance and can’t be found in the `.d.ts` files included with TypeScript.

### `Uppercase<StringType>` {#uppercasestringtype}

Converts each character in the string to the uppercase version.

##### Example {#example}

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA4gThCwCWA7A5lAvFARACQgBsiB7AGigHdS4iATXAKFEigGUALUgV1HkQoM2KAFUwkOAGMAhgGcIAHgFI06AHxMA9Fqh6AegH4mLcNACC7AMIBJG1ZlTOEANIQQi9sDhQIAD2AIVHo5KDlvNXURAAMbABEAWgASAG9xSVkFT291AF9o0zYAWRk0eJFLW3tHZzcPXABbEAB9GQlcTR09KCMgA)

```ts
type Greeting = "Hello, world"
typeShoutyGreeting = Uppercase<Greeting>

type ShoutyGreeting = "HELLO, WORLD"

typeASCIICacheKey<Strextendsstring> = `ID-${Uppercase<Str>}`
typeMainID = ASCIICacheKey<"my_app">

type MainID = "ID-MY_APP"
```

### `Lowercase<StringType>` {#lowercasestringtype}

Converts each character in the string to the lowercase equivalent.

##### Example {#example-1}

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBA4gThCwCWA7A5lAvFARACQgBsiB7AGigHdS4iATXAKFEigEUBXZJeRFDNigAZUlQhwAxgEMAzhAA8fJGnQA+JgHpNUXQD0A-ExbhoAQQDKAYQCSNq9MkALCAGkIIBReBwoEAB7AEKj0slCyPqpqQgAGyPQAtAAkAN6i4lJyit5wagC+MSZsALLSaDYAIkKWtvaOLu6euMUAmgD6ZgAKnbga2rpQhkA)

```ts
type Greeting = "Hello, world"
typeQuietGreeting = Lowercase<Greeting>

type QuietGreeting = "hello, world"

typeASCIICacheKey<Strextendsstring> = `id-${Lowercase<Str>}`
typeMainID = ASCIICacheKey<"MY_APP">

type MainID = "id-my_app"
```

### `Capitalize<StringType>` {#capitalizestringtype}

Converts the first character in the string to an uppercase equivalent.

##### Example {#example-2}

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAMg9gdwgJwMYEMDOEDiyITACWAdgOZQC8UARABYQA2jcANFAnMowCY0DcAKFCQoeAsXJUoAYXRgiwdIyIAvCAB54SNFlz5CpMgD4hAejNQrAPQD8QA)

```ts
type LowercaseGreeting = "hello, world";
typeGreeting = Capitalize<LowercaseGreeting>;

type Greeting = "Hello, world"
```

### `Uncapitalize<StringType>` {#uncapitalizestringtype}

Converts the first character in the string to a lowercase equivalent.

##### Example {#example-3}

[Try this code ↗](https://www.typescriptlang.org/play#code/C4TwDgpgBAqmkCcDGBDAzhA4giFgEsA7AcygF4oAiACQFEAZegeSgHUmAlegEUoG4AUKEixCSAPYBbAGbiEwFACMANlhx4ipCjDEow+BcvwAvCAB44iVBmy4CJAHyCA9M6juAegH4gA)

```ts
type UppercaseGreeting = "HELLO WORLD";
typeUncomfortableGreeting = Uncapitalize<UppercaseGreeting>;

type UncomfortableGreeting = "hELLO WORLD"
```
