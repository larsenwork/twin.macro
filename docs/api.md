# API

### Styled

//...

### CSS

//...

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

//...
