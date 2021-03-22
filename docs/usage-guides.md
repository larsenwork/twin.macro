# Usage guides

Twin has two major styling types:

1. **Prop styling**<br/>
   Style existing jsx elements within props:

   ```js
   <input tw="text-black rounded" />
   <input css={tw`text-black rounded`} />
   ```

2. **[Styled components](#)**<br/>
   Create and style jsx elements in the same code block:
   ```js
   const Input = tw.div`text-black rounded`
   <Input />
   ```
