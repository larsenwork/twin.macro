# The prop styling guide

Why you should choose prop styling:

**✓ Familiar tailwind experience**<br/>Instead of adding classes in the class attribute, we add them to the tw prop:

```js
// In tailwind
<div class="bg-black text-white"></div>

// In twin.macro
<div tw="bg-black text-white"></div>
```

**✓ Better debugging and traceability**<br/>The `data-tw` prop contains a preserved list of tailwind classes in your browser devtools:

```js
<div class="n644fs2" data-tw="bg-black text-white"></div>
```

**✓ Less imports required**<br/>
With [babel-plugin-twin](https://github.com/ben-rogerson/babel-plugin-twin) added, you can use the `tw` prop anytime:

```js
<div tw="bg-black text-white"></div>
```

Otherwise, add the twin import to activate the prop:

```js
import 'twin.macro'
;<div tw="bg-black text-white"></div>
```

Instead of adding tailwind classes in the `class`/`className` attribute - like we do in Tailwind - we add classes to the tw prop. This

Read on to get productive with prop styling in twin...

### This guide covers

- [Basic styling](#basic-styling)
- [Conditional styling](#conditional-styling)
  - [Dealing with long class sets](#move-long-class-sets-out-of-jsx)
- [Adding custom css](#adding-custom-css)
  - [Basic css styling](#basic-css-styling)
  - [Advanced css styling](#advanced-css-styling)
  - [Mixing css with tailwind classes](#mixing-css-with-tailwind-classes)
- [Pro styling tips](#pro-styling-tips)

## Basic styling

You can add basic styles with the tw prop when conditional styles are not needed:

```tsx
import 'twin.macro'

const Component = () => (
  <div tw="flex w-full">
    <div tw="w-1/2" />
    <div tw="w-1/2" />
  </div>
)
```

- Any import from twin.macro activates the tw prop
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

Since we're in JavaScript, we don’t need to settle for bloated prop lists, so we can move them out of jsx.

A great solution is to group our styles together as named entries in an object:

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

## Interpolated

## Adding custom css

Sometimes we need to supplement our tailwind styles with some vanilla css - either when a tailwind class isn’t available or to add custom values (eg: `transform: scale(1.02)`). For this, Twin has a syntax called “short css” which allows us to write custom css beside our tailwind classes.

To add short css, use this syntax: `css-property[ css-value ]`

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

## Pro styling tips

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
