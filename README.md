# CSSType

[![npm](https://img.shields.io/npm/v/csstype.svg)](https://www.npmjs.com/package/csstype)

TypeScript and Flow definitions for CSS, generated by [data from MDN](https://github.com/mdn/data). It provides autocompletion and type checking for CSS properties and values.

```ts
import * as CSS from 'csstype';

const style: CSS.Properties = {
  colour: 'white', // Type error on property
  textAlign: 'middle', // Type error on value
};
```

## Getting started

```sh
$ npm install csstype
$ # or
$ yarn add csstype
```

## Table of content

- [Style types](#style-types)
- [At-rule types](#at-rule-types)
- [Pseudo types](#pseudo-types)
- [Usage](#usage)
- [What should I do when I get type errors?](#what-should-i-do-when-i-get-type-errors)
- [Version 2.0](#version-20)
- [Contributing](#contributing)
  - [Commands](#commands)

## Style types

Properties are categorized in different uses and in several technical variations to provide typings that suits as many as possible.

|                | Default              | `Hyphen`                   | `Fallback`                   | `HyphenFallback`                   |
| -------------- | -------------------- | -------------------------- | ---------------------------- | ---------------------------------- |
| **All**        | `Properties`         | `PropertiesHyphen`         | `PropertiesFallback`         | `PropertiesHyphenFallback`         |
| **`Standard`** | `StandardProperties` | `StandardPropertiesHyphen` | `StandardPropertiesFallback` | `StandardPropertiesHyphenFallback` |
| **`Vendor`**   | `VendorProperties`   | `VendorPropertiesHyphen`   | `VendorPropertiesFallback`   | `VendorPropertiesHyphenFallback`   |
| **`Obsolete`** | `ObsoleteProperties` | `ObsoletePropertiesHyphen` | `ObsoletePropertiesFallback` | `ObsoletePropertiesHyphenFallback` |
| **`Svg`**      | `SvgProperties`      | `SvgPropertiesHyphen`      | `SvgPropertiesFallback`      | `SvgPropertiesHyphenFallback`      |

Categories:

- **All** - Includes `Standard`, `Vendor`, `Obsolete` and `Svg`
- **`Standard`** - Current properties and extends subcategories `StandardLonghand` and `StandardShorthand` _(e.g. `StandardShorthandProperties`)_
- **`Vendor`** - Vendor prefixed properties and extends subcategories `VendorLonghand` and `VendorShorthand` _(e.g. `VendorShorthandProperties`)_
- **`Obsolete`** - Removed or deprecated properties
- **`Svg`** - SVG-specific properties

Variations:

- **Default** - JavaScript (camel) cased property names
- **`Hyphen`** - CSS (kebab) cased property names
- **`Fallback`** - Also accepts array of values e.g. `string | string[]`

## At-rule types

At-rule interfaces with descriptors.

|                      | Default        | `Hyphen`             | `Fallback`             | `HyphenFallback`             |
| -------------------- | -------------- | -------------------- | ---------------------- | ---------------------------- |
| **`@counter-style`** | `CounterStyle` | `CounterStyleHyphen` | `CounterStyleFallback` | `CounterStyleHyphenFallback` |
| **`@font-face`**     | `FontFace`     | `FontFaceHyphen`     | `FontFaceFallback`     | `FontFaceHyphenFallback`     |
| **`@page`**          | `Page`         | `PageHyphen`         | `PageFallback`         | `PageHyphenFallback`         |
| **`@viewport`**      | `Viewport`     | `ViewportHyphen`     | `ViewportFallback`     | `ViewportHyphenFallback`     |

## Pseudo types

String literals of pseudo classes and pseudo elements

- `Pseudos`

  Extends:

  - `AdvancedPseudos`

    Function-like pseudos e.g. `:not(:first-child)`. The string literal contains the value excluding the parenthesis: `:not`. These are separated because they require an argument that results in infinite number of variations.

  - `SimplePseudos`

    Plain pseudos e.g. `:hover` that can only be **one** variation.

## Generics

All interfaces has two optional generic argument to define length and time: `CSS.Properties<TLength = string | 0, TTime = string>`

- **Length** is the first generic parameter and defaults to `string | 0` because `0` is the only [length where the unit identifier is optional](https://drafts.csswg.org/css-values-3/#lengths). You can specify this, e.g. `string | number`, for platforms and libraries that accepts any numeric value as length with a specific unit.
  ```tsx
  const style: CSS.Properties<string | number> = {
    width: 100,
  };
  ```
- **Time** is the second generic argument and defaults to `string`. You can specify this, e.g. `string | number`, for platforms and libraries that accepts any numeric value as length with a specific unit.
  ```tsx
  const style: CSS.Properties<string | number, number> = {
    transitionDuration: 1000,
  };
  ```

## Usage

```ts
import * as CSS from 'csstype';

const style: CSS.Properties = {
  width: '10px',
  margin: '1em',
};
```

In some cases, like for CSS-in-JS libraries, an array of values is a way to provide fallback values in CSS. Using `CSS.PropertiesFallback` instead of `CSS.Properties` will add the possibility to use any property value as an array of values.

```ts
import * as CSS from 'csstype';

const style: CSS.PropertiesFallback = {
  display: ['-webkit-flex', 'flex'],
  color: 'white',
};
```

There's even string literals for pseudo selectors and elements.

```ts
import * as CSS from 'csstype';

const pseudos: { [P in CSS.SimplePseudos]?: CSS.Properties } = {
  ':hover': {
    display: 'flex',
  },
};
```

Hyphen cased (kebab cased) properties are provided in `CSS.PropertiesHyphen` and `CSS.PropertiesHyphenFallback`. It's not **not** added by default in `CSS.Properties`. To allow both of them, you can simply extend with `CSS.PropertiesHyphen` or/and `CSS.PropertiesHyphenFallback`.

```ts
import * as CSS from 'csstype';

interface Style extends CSS.Properties, CSS.PropertiesHyphen {}

const style: Style = {
  'flex-grow': 1,
  'flex-shrink': 0,
  'font-weight': 'normal',
  backgroundColor: 'white',
};
```

## What should I do when I get type errors?

The goal is to have as perfect types as possible and we're trying to do our best. But with CSS Custom Properties, the CSS specification changing frequently and vendors implementing their own specifications with new releases sometimes causes type errors even if it should work. Here's some steps you could take to get it fixed:

_If you're using CSS Custom Properties you can step directly to step 3._

1.  **First of all, make sure you're doing it right.** A type error could also indicate that you're not :wink:

    - Some CSS specs that some vendors has implemented could have been officially rejected or haven't yet received any official acceptance and are therefor not included
    - If you're using TypeScript, [type widening](https://blog.mariusschulz.com/2017/02/04/typescript-2-1-literal-type-widening) could be the reason you get `Type 'string' is not assignable to...` errors

2.  **Have a look in [issues](https://github.com/frenic/csstype/issues) to see if an issue already has been filed. If not, create a new one.** To help us out, please refer to any information you have found.
3.  Fix the issue locally with **TypeScript** (Flow further down):

    - The recommended way is to use **module augmentation**. Here's a few examples:

      ```ts
      // My css.d.ts file
      import * as CSS from 'csstype';

      declare module 'csstype' {
        interface Properties {
          // Add a missing property
          WebkitRocketLauncher?: string;

          // Add a CSS Custom Property
          '--theme-color'?: 'black' | 'white';

          // ...or allow any other property
          [index: string]: any;
        }
      }
      ```

    - The alternative way is to use **type assertion**. Here's a few examples:

      ```ts
      const style: CSS.Properties = {
        // Add a missing property
        ['WebkitRocketLauncher' as any]: 'launching',

        // Add a CSS Custom Property
        ['--theme-color' as any]: 'black',
      };
      ```

    Fix the issue locally with **Flow**:

    - Use **type assertion**. Here's a few examples:

      ```js
      const style: $Exact<CSS.Properties<*>> = {
        // Add a missing property
        [('WebkitRocketLauncher': any)]: 'launching',

        // Add a CSS Custom Property
        [('--theme-color': any)]: 'black',
      };
      ```

## Version 3.0

- **All property types are exposed with namespace**  
  TypeScript: `Property.AlignContent` (was `AlignContentProperty` before)  
  Flow: `Property$AlignContent`
- **All at-rules are exposed with namespace**  
  TypeScript: `AtRule.FontFace` (was `FontFace` before)  
  Flow: `AtRule$FontFace`
- **Data types are NOT exposed**  
  E.g. `Color` and `Box`. Because the generation of data types may suddenly be removed or renamed.
- **TypeScript hack for autocompletion**  
  Uses `(string & {})` for literal string unions and `(number & {})` for literal number unions (https://github.com/microsoft/TypeScript/issues/29729). Utilize `PropertyValue<T>` to unpack types from e.g. `(string & {})` to `string`.
- **New generic for time**  
  `Properties<TLength = string | 0, TTime = string>` gives you an opportunity to change type for time. E.g.
  ```tsx
  const style: CSS.Properties<string | number, number> = {
    transitionDuration: 1000,
  };
  ```
- **Flow types improvements**  
  Flow Strict enabled and exact types are used.

## Contributing

**Never modify `index.d.ts` and `index.js.flow` directly. They are generated automatically and committed so that we can easily follow any change it results in.** Therefor it's important that you run `$ git config merge.ours.driver true` after you've forked and cloned. That setting prevents merge conflicts when doing rebase.

### Commands

- `yarn build` Generates typings and type checks them
- `yarn watch` Runs build on each save
- `yarn test` Runs the tests
- `yarn lazy` Type checks, lints and formats everything
