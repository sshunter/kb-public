# An Example of Adding a New Function to the String class in Typescript

```typescript
declare global {
  interface String {
    replaceAll(search: string, replacement: string): string;
  }
}

String.prototype.replaceAll = function(this: string, search: string, replacement: string): string {
  return StringUtils.replaceAll(this, search, replacement);
};
```

Documentation on `declare global` and similar ideas can be found at
https://www.typescriptlang.org/docs/handbook/declaration-merging.html#global-namespace-and-global-modules


