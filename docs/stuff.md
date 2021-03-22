### Theme

Sometimes you may need to access a value from your Tailwind theme directly. You can use the `theme` utility.

```tsx
import { theme } from 'twin.macro'

const blue500 = theme('colors.blue.500')

// I can also be typed!
type Screen = 'sm' | 'md' | 'lg' | 'xl' | '2xl' | 'max'
const screens = theme<Record<Screen, string>>('screens')
```

### Global styles

Global styles are declared in `~/styles/GlobalStyles.tsx`. The same rules apply!

### Custom fonts

Add your own font by:

- Add font files to the `public` directory
- Create font-face rules in `~/styles/fonts.css`
- Preload new fonts in `~/pages/__app.tsx`
- Add font name to Tailwind config `tailwind.config.js`

Next.js: Adding custom font-face declarations directly into `~/styles/GlobalStyles.tsx` is [not recommended](https://styled-components.com/docs/faqs#how-do-i-fix-flickering-text-after-server-side-rendering).
