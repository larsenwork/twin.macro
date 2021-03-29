# The prop-styling guide

- [Basic styling](#basic-styling)
- [Conditional styling](#conditional-styling)
- [Custom css](#custom-css)
- [Styling tips](#styling-tips)

There are two ways to style in twin:

1. [Create styled components with the styled import](https://github.com/ben-rogerson/twin.macro/blob/master/docs/styled-import-guide.md)
2. Style existing elements with the tw and css props **<- You are here**

## Why use prop styling

Here are some reasons why you might choose to style with props:

**It’s similar to styling with tailwind**<br/>Instead of adding classes in the `class` attribute, they get added in the `tw` prop:

```js
// In tailwind
;<div class="bg-black text-white"></div>

// In twin.macro
;<div tw="bg-black text-white"></div>
```

> Twin can also convert classes added in the `className` attribute with the [includeClassNames](https://github.com/ben-rogerson/twin.macro/blob/master/docs/options.md#includeClassNames) feature.

**Better debugging in devtools**<br/>After twin converts your classes, they are added to a `data-tw` prop for easier trace backs:

```js
<div class="n644fs2" data-tw="bg-black text-white"></div>
```

> The [data-tw prop](<(https://github.com/ben-rogerson/twin.macro/blob/master/docs/options.md#dataTwProp)>) only shows in development (by default)

**Less imports to use**<br/>
A single nameless import activates the tw prop:

```js
import 'twin.macro'
;<div tw="bg-black text-white"></div>
```

> Install [babel-plugin-twin](https://github.com/ben-rogerson/babel-plugin-twin) to use the `tw` prop without an import.

## Basic styling

Instead of adding classes in the `class` or `className` attribute - like we do in Tailwind - we use the [tw prop](https://github.com/ben-rogerson/twin.macro/blob/master/docs/options.md):

```tsx
import 'twin.macro'

const Component = () => (
  <div tw="flex w-full">
    <div tw="w-1/2" />
    <div tw="w-1/2" />
  </div>
)
```

- Use the tw prop when conditional styles are not needed
- Any import from twin.macro activates the [tw prop](https://github.com/ben-rogerson/twin.macro/blob/master/docs/options.md)
- Add the tw prop automatically using [babel-plugin-twin](https://github.com/ben-rogerson/babel-plugin-twin)

## Conditional styling

To add conditional or multi-line styles, use twin’s `tw` import within the `css` prop:

```js
import tw from 'twin.macro'

const Component = ({ hasDarkTheme }) => (
  <div css={[tw`flex w-full`, hasDarkTheme && tw`bg-black`]}>
    <div tw="w-1/2" />
    <div tw="w-1/2" />
  </div>
)
```

### Keeping jsx clean

When tailwind class sets become larger, they obstruct the readability of jsx props.

Since we’re in JavaScript, we don’t need to settle for bloated prop lists, so we can move them out of jsx.

To group our styles together outside jsx, we can add them as named entries in an object:

```js
import tw from 'twin.macro'

const styles = {
  container: ({ hasDarkTheme }) => [
    tw`flex w-full`, // Add base styles first
    hasDarkTheme && tw`bg-black`, // Then add conditional styles
  ],
  column: tw`w-1/2`,
}

const Component = props => (
  <div css={styles.container(props)}>
    <div css={styles.column} />
    <div css={styles.column} />
  </div>
)
```

<details>
  <summary>TypeScript example</summary>

```js
import tw from 'twin.macro'

interface containerProps {
  hasDarkTheme?: boolean;
}

const styles = {
  container: ({ hasDarkTheme }: containerProps) => [
    tw`flex w-full`, // Add base styles first
    hasDarkTheme && tw`bg-black`, // Then add conditional styles
  ],
  column: tw`w-1/2`,
}

const Component = props => (
  <div css={styles.container(props)}>
    <div css={styles.column} />
    <div css={styles.column} />
  </div>
)
```

</details>

### Passing props with many values

// ...

## Custom css

Sometimes we need to supplement our tailwind styles with some vanilla css - either when a tailwind class isn’t available or to add custom values (eg: `transform: scale(1.02)`). For this, Twin has a syntax called “short css” which allows us to write custom css beside our tailwind classes.

To add short css, use this syntax: `css-property[css-value]`

### Basic css styling

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

// Set crazy grid values (expand long values over multiple lines)
<div css={tw`grid-template-areas['
  "z z z"
  "a b c"
  "d e f"
']`} />
```

Short css also works seamlessly with twin’s variant grouping features:

```js
<div tw="md:(block max-width[calc(100vw - 2em)])" />
```

#### Tips

- Add a trailing bang to make the custom css !important: `maxWidth[2rem]!`
- You can add short css into twin’s `cs` prop to separate short css from tw classes: `<div cs="maxWidth[2rem]" />`
- Supports camelCased css properties: `maxWidth[2rem]`
- Interpolated variables aren’t supported

### Advanced css styling

[intro about switching to the css prop for this]
[Supports interpolated variables, eg: `${size}`]
[Something about being sass-like with the & selector]

The css prop allows styles in template literals:

```js
import { css } from 'twin.macro'

const Components = () => (
  <input
    css={css`
      max-width: calc(100vw - 1em);
      &::selection {
        ${tw`text-purple-500`};
      }
    `}
  />
)
```

- Wrap the styles with the `css` import to apply css highlighting in your code editor
- Using interpolated variables (`${var}`) make larger style sets harder to read
- Semicolons must be added at the end of each line

Write styles in css object format to lean more into classic css-in-js styling:

```js
import { css } from 'twin.macro'

const Components = () => (
  <input
    css={{
      maxWidth: 'calc(100vw - 1em)',
      marginTop: '400px',
      '&::selection': tw`text-purple-500`,
    }}
  />
)
```

- Shorter and cleaner syntax for styling sub selectors and anything complex
- Slightly faster speeds in the underlying css-in-js library as conversion from backticks is not required

### Mixing css with tailwind classes

Add custom css by placing the styles in an array:

```js
import tw from 'twin.macro'

<div css={[tw`block`, `max-width: calc(100vw - 1em);`]}>
```

Nest vanilla css within the `css` import when they’re extracted from the jsx element:

```js
import tw, { css } from 'twin.macro'

const cssStyles = css(`max-width: calc(100vw - 1em);`)

<div css={[tw`block`, cssStyles]}>
```

## Styling tips

### ✓ Keep line lengths shorter with booleans

Ternaries are great to use for smaller class sets:

```js
const inputStyles = ({ hasDarkTheme }) => [
  hasDarkTheme ? tw`bg-black` : tw`text-white`,
]
```

But for longer sets of classes, use booleans to keep line lengths short:

```js
const inputStyles = ({ hasDarkTheme }) => [
  hasDarkTheme && tw`bg-black text-white placeholder-gray-700`,
  !hasDarkTheme && tw`bg-white text-black placeholder-gray-300`,
]
```

### ✓ Move common styles to a shared styling file

Reusable classes get named and shifted into a shared styles file:

```js
// sharedStyles.js
export const defaultLayout = {
  // Styles with conditionals are functions
  container: ({ hasDarkTheme }) => [
    tw`flex w-full`,
    hasDarkTheme && tw`bg-black text-white`,
  ],
  // Styles with no conditionals
  column: tw`w-1/2 border-t`,
}
```

Import then add the shared styles with the css prop:

```js
// app.js
import 'twin.macro'
import { defaultLayout as layout } from './sharedStyles'

const Component = ({ hasDarkTheme }) => (
  <div css={layout.container({ hasDarkTheme })}>
    <div css={layout.column} />
    <div css={layout.column} />
  </div>
)
```

### ✓ Use class grouping with large styling sets

Group large style sets by type on their own lines:

```js
const styles = tw`
  block relative
  text-black bg-gray-100
  // ...
`
const Component = () => <div css={styles} />
```

Or group classes with parenthesis: `<div tw="block (ml-1 mr-2)" />`

Or separate groups with pipes: `<div tw="block | ml-1 mr-2" />`

## Learn more

- The styled components guide (soon)
- '[container](./container.md)', '[group](./group.md)' - Usage docs for these classes in twin
- [Advanced theming](docs/advanced-theming.md) - Add custom theming the right way using css variables

## Resources

- [babel-plugin-twin](https://github.com/ben-rogerson/babel-plugin-twin) - Use the tw and css props without adding an import
- [React + Tailwind breakpoint syncing](https://gist.github.com/ben-rogerson/b4b406dffcc18ae02f8a6c8c97bb58a8) - Sync your tailwind.config.js breakpoints with react
- [Twin VSCode snippits](https://gist.github.com/ben-rogerson/c6b62508e63b3e3146350f685df2ddc9) - For devs who want to type less
- [VSCode autocomplete extension](https://github.com/ben-rogerson/twin.macro/discussions/227) - Twin works with the official vscode intellisense extension
