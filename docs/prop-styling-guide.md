# The prop styling guide

## Basic styling

Use Twin’s tw prop to add Tailwind classes onto jsx elements:

```tsx
import 'twin.macro'

const Component = () => (
  <div tw="flex w-full">
    <div tw="w-1/2" />
    <div tw="w-1/2" />
  </div>
)
```

- Use the tw prop when conditional styles aren’t needed
- Any import from `twin.macro` activates the tw prop
- Add the tw prop automatically using [babel-plugin-twin](https://github.com/ben-rogerson/babel-plugin-twin)

## Conditional styling

To add conditional styles, import `tw` and nest it within an array inside the `css` prop which comes with your css-in-js library:

```js
import tw from 'twin.macro'

const Component = ({ hasBg }) => (
  <div
    css={[
      tw`flex w-full`, // Add base styles first
      hasBg && tw`bg-black`, // Then add conditional styles
    ]}
  >
    <div tw="w-1/2" />
    <div tw="w-1/2" />
  </div>
)
```

<details>

<summary>TypeScript example</summary>

```tsx
import tw from 'twin.macro'

interface ComponentProps {
  hasBg?: string
}

const Component = ({ hasBg }: ComponentProps) => (
  <div
    css={[
      tw`flex w-full`, // Add base styles first
      hasBg && tw`bg-black`, // Then add conditional styles
    ]}
  >
    <div tw="w-1/2" />
    <div tw="w-1/2" />
  </div>
)
```

</details>

- Adding values to an array makes it easier to define base styles, conditionals and vanilla css
- Use multiple lines to organize styles within the backticks (or [template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals))

## Overriding styles

Use the `tw` prop to override styles added by the css prop:

```tsx
import tw from 'twin.macro'

const Component = () => (
  <Component css={tw`text-white`} tw="text-black" /> // This will have black text
)
```

## Keeping jsx clean

It’s no secret that when tailwind class sets become larger, they obstruct the readability of other jsx props.

To clean up the jsx, lift out the styles and group them as named entries in an object:

```js
import tw from 'twin.macro'

const styles = {
  container: ({ hasBg }) => [
    tw`flex w-full`, // Add base styles first
    hasBg && tw`bg-black`, // Then add conditional styles
  ],
  column: tw`w-1/2`,
}

const Component = ({ hasBg }) => (
  <div css={styles.container({ hasBg })}>
    <div css={styles.column} />
    <div css={styles.column} />
  </div>
)
```

<details>
  <summary>TypeScript example</summary>

```js
import tw from 'twin.macro'

interface ContainerProps {
  hasBg?: boolean;
}

const styles = {
  container: ({ hasBg }: ContainerProps) => [
    tw`flex w-full`, // Add base styles first
    hasBg && tw`bg-black`, // Then add conditional styles
  ],
  column: tw`w-1/2`,
}

const Component = ({ hasBg }: ContainerProps) => (
  <div css={styles.container({ hasBg })}>
    <div css={styles.column} />
    <div css={styles.column} />
  </div>
)
```

</details>

## Variants with many values

When a variant has many values (eg: `variant="light/dark/crazy/..."`), name the class set in an object and use a prop to grab the entry:

```js
import tw, { styled } from 'twin.macro'

const containerVariants = {
  // Named class sets
  light: tw`bg-white text-black`,
  dark: tw`bg-black text-white`,
}

const styles = {
  container: ({ variant = 'dark' }) => [
    tw`flex w-full`,
    containerVariants[variant], // Grab the variant style via a prop
  ],
  column: tw`w-1/2`,
}

const Component = ({ variant }) => (
  <div css={styles.container({ variant })}>
    <div css={styles.column} />
    <div css={styles.column} />
  </div>
)

export { Component }
```

<details>
  <summary>TypeScript example</summary>

```tsx
import tw, { styled, TwStyle } from 'twin.macro'

type WrapperVariant = 'light' | 'dark' | 'crazy'

interface ContainerProps {
  variant?: WrapperVariant
}

const containerVariants: Record<WrapperVariant, TwStyle> = {
  light: tw`bg-white text-black`,
  dark: tw`bg-black text-white`,
}

const styles = {
  container: ({ variant = 'dark' }: ContainerProps) => [
    tw`flex w-full`,
    containerVariants[variant], // Grab the variant style via a prop
  ],
  column: tw`w-1/2`,
}

const Component = ({ variant }: ContainerProps) => (
  <div css={styles.container({ variant })}>
    <div css={styles.column} />
    <div css={styles.column} />
  </div>
)

export { Component }
```

</details>

- Due to limitations in Babel, classes can’t be passed in from props, eg: `` tw`bg-${color}` ``

## Custom css

Basic css is added with twin using the “short css” syntax or within the css prop which supports more advanced use cases like dynamic values.

### Simple css styling

To add simple custom styling, use twins “short css” syntax:

```js
// Set content properties for pseudo elements
<div tw="before:(content['hey there'] block)" />

// Set a css variables
<div tw="--my-width-variable[calc(100vw - 10rem)]" />

// Use a css variables
<div tw="width[--my-width-variable]" />

// Set vendor prefixes
<div tw="-webkit-line-clamp[3]" />

// Set grid areas
<div tw="grid-area[1 / 1 / 4 / 2]" />
```

Use short css with twin’s variants and grouping features:

```js
<div tw="block md:(relative max-width[calc(100vw - 2em)])" />
```

Short css also works with the `tw` import:

```js
import tw from 'twin.macro'

const styles = tw`
  block
  md:(relative max-width[calc(100vw - 2em)])
`

const Component = () => <div css={styles} />
```

- Add a trailing bang to make the custom css !important: `max-width[2rem]!`
- To keep short css separate from tw classes, add it in the `cs` prop: `<div cs="max-width[2rem]" />`
- Short css may be added with camelCase properties: `maxWidth[2rem]`
- Interpolation/dynamic values aren’t supported

### Advanced css styling

Advanced css can be added with the css prop which comes with the css-in-js library.

This css prop supports interpolated variables and dynamic styles:

```js
import { css, theme } from 'twin.macro'

const Components = () => (
  <input
    css={css`
      ${tw`text-black`};
      background-color: ${theme`colors.red.500`};
      max-width: calc(100vw - 1em);
      &::selection {
        ${tw`text-purple-500`};
      }
    `}
  />
)
```

- Prefix css styles with the `css` import to apply css highlighting in your editor
- Add semicolons to the end of each line

You can avoid the interpolation cruft above by using object styles instead:

```js
import { css, theme } from 'twin.macro'

const Components = () => (
  <input
    css={{
      ...tw`text-blue-500 border-2`,
      backgroundColor: theme`colors.red.500`,
      maxWidth: 'calc(100vw - 1em)', // css properties are camel cased
      '&::selection': tw`text-purple-500`,
    }}
  />
)
```

### Mixing css with tailwind classes

Mix tailwind classes and custom css in an array:

```js
import tw from 'twin.macro'

<div css={[tw`block`, `max-width: calc(100vw - 1em);`]}>
```

When you move the styles out of jsx, prefix them with the `css` import:

```js
import tw, { css } from 'twin.macro'

const cssStyles = css(`max-width: calc(100vw - 1em);`)

<div css={[tw`block`, cssStyles]}>
```

## Styling tips

### ✓ Keep line lengths shorter with booleans

Ternaries are great for small class sets:

```js
const inputStyles = ({ hasDarkTheme }) => [
  hasDarkTheme ? tw`bg-black` : tw`text-white`,
]
```

With longer class sets, use booleans to keep line lengths short:

```js
const inputStyles = ({ hasDarkTheme }) => [
  hasDarkTheme && tw`bg-black text-white placeholder-gray-700`,
  !hasDarkTheme && tw`bg-white text-black placeholder-gray-300`,
]
```

### ✓ Move common styles to a shared styling file

Reusable styles should be shifted into a shared file:

```js
// commonStyles.js
export const layout = {
  container: ({ hasDarkTheme }) => [
    tw`flex w-full`,
    hasDarkTheme && tw`bg-black text-white`,
  ],
  column: tw`w-1/2 border-t`,
}
```

Attach shared styles to jsx elements with the css prop:

```js
// app.js
import 'twin.macro'
import { layout } from './commonStyles'

const Component = ({ hasDarkTheme }) => (
  <div css={layout.container({ hasDarkTheme })}>
    <div css={layout.column} />
    <div css={layout.column} />
  </div>
)
```

### ✓ Use class grouping with large styling sets

Group large style sets by their type on multiple lines:

```js
const styles = tw`
  block relative
  text-black bg-gray-100
  // ...
`
const Component = () => <div css={styles} />
```

Or use brackets or pipes for single line grouping:

```js
<div tw="block (ml-1 mr-2)" />
// or
<div tw="block | ml-1 mr-2" />
```

### ✓ Avoid using css-in-js for complex animation

If you are - for instance - changing an element’s position frequently, consider using the style prop instead or even better, use [framer-motion](https://www.framer.com/motion/) or [GSAP](https://greensock.com/react/).

## Learn more

- The styled components guide (soon)
- Class docs: '[container](https://github.com/ben-rogerson/twin.macro/blob/master/docs/container.md)', '[group](https://github.com/ben-rogerson/twin.macro/blob/master/docs/group.md)' - More about these classes that work differently
- [Advanced theming](https://github.com/ben-rogerson/twin.macro/blob/master/docs/advanced-theming.md) - Add custom theming the right way using css variables

If you have any questions, feel free to [drop into our Discord server](https://discord.gg/Xj6x9z7).

<a href="https://discord.gg/Xj6x9z7"><img src="https://img.shields.io/discord/705884695400939552?label=discord&logo=discord" alt="Discord"></a>

## Resources

- [babel-plugin-twin](https://github.com/ben-rogerson/babel-plugin-twin) - Use the tw and css props without adding an import
- [React + Tailwind breakpoint syncing](https://gist.github.com/ben-rogerson/b4b406dffcc18ae02f8a6c8c97bb58a8) - Sync your tailwind.config.js breakpoints with react
- [Twin VSCode snippits](https://gist.github.com/ben-rogerson/c6b62508e63b3e3146350f685df2ddc9) - For devs who want to type less
- [VSCode autocomplete extension](https://github.com/ben-rogerson/twin.macro/discussions/227) - Twin works with the official vscode intellisense extension
