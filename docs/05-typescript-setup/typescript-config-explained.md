# TypeScript Config Deep Dive

This section is where your `tsconfig.json` starts to make sense.

We’ll go through each option step by step, not just explaining what it does, but why it exists, when it actually matters and more importantly, explain why we made the choice we did.

Here is the config we are working with:
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

Let us go through each one.


## `target`
```json
"target": "ES2020"
```
This tells the TypeScript compiler: **"When you convert my TypeScript into JavaScript, which version of JavaScript should the output be?"**   

JavaScript has evolved over the years. ES5 was the old, widely-compatible version. ES2020 is a modern version that supports features like async/await, optional chaining (?.), nullish coalescing (??), and more — natively, without any transformation tricks.

We set this to ES2020 because modern Node.js fully supports it. The output stays clean, readable, and close to what you originally wrote.

If you set this to ES5, TypeScript would have to transform async/await into deeply nested callback chains just to make it compatible. It would work — but the compiled output would be unreadable and harder to debug.


## `module`
```json
"module": "commonjs"
```
This tells the compiler: **"What module system should the output JavaScript use?"**

There are two module systems you will encounter:

**CommonJS** — uses require() and module.exports. This is what Node.js uses by default.  
**ESModules** — uses import and export. This is what browsers and modern bundlers use.

Even though you write `import express from 'express'` in TypeScript, after compilation it becomes `const express = require('express')` in the output — because we told TypeScript to use commonjs.

Since we are building a Node.js backend, this is the correct choice.

## `lib`
```json
"lib": ["ES2020"]
```

This tells TypeScript: **"Which built-in JavaScript APIs should you be aware of?"**

TypeScript ships with type definitions for built-in JavaScript features — things like `Promise`, `Array.prototype.map`, `Object.entries`, and so on. The `lib` field controls which set of those definitions gets included.

By setting it to ES2020, we are telling TypeScript to recognise all built-in features up to the ES2020 standard. Without this, TypeScript might throw errors on perfectly valid modern JavaScript — simply because it does not know those APIs exist.

## `outDir`
```json
"outDir": "./dist"
```
This tells the compiler: **"Put all your compiled JavaScript output in this folder."**

When TypeScript compiles your `.ts` files, it needs somewhere to write the .js files it generates. We point it to a folder called dist — short for distribution.

So `src/index.ts` becomes `dist/index.js` after compilation. Your Node.js process runs from `dist/` — never from `src/`.

> The dist folder should never be committed to GitHub. It is generated output — not source code. We will add it to .gitignore later.


## `rootDir`
```json
"rootDir": "./src"
```

This tells the compiler: **"All my TypeScript source files live here."**

`rootDir` and `outDir` work as a pair. TypeScript mirrors the folder structure of src/ inside dist/. So if you have `src/routes/user.ts`, it compiles to `dist/routes/user.js` — the structure is preserved.

## `strict`
```json
"strict": true
```

This is the most important setting in the entire file.
Setting strict to true turns on a collection of type-checking rules all at once. The most important ones are:

- `strictNullChecks` — you cannot accidentally use null or undefined where a real value is expected
- `noImplicitAny` — TypeScript will not silently assume any when a type is missing; it will force you to be explicit

Think of `strict: true` as the whole reason you are using TypeScript in the first place. Turning it off to silence errors defeats the purpose entirely.

Always keep this **true**.

## `esModuleInterop`
```json
"esModuleInterop": true
```

This fixes a very common import problem.   

Many popular Node.js packages — like express — were written using CommonJS and do not have a proper default export in the ESModule sense. Without this setting, writing:

```ts
import express from 'express';
```
would cause TypeScript to complain. With `esModuleInterop: true`, TypeScript adds a small compatibility layer that makes this import work exactly as you would expect.

Always keep this true for Node.js projects.

## `skipLibCheck`
```json
"skipLibCheck": true
```

This tells TypeScript: **"Do not type-check declaration files inside node_modules."**

Every package you install may ship with .d.ts files — type declaration files. Sometimes these files conflict with each other or contain minor errors. Without `skipLibCheck`, TypeScript would try to validate all of them and sometimes fail on errors you cannot even fix — because they are inside packages you did not write.

This keeps your build clean and fast without any real downside.

## `forceConsistentCasingInFileNames`
```json
"forceConsistentCasingInFileNames": true
```

This enforces that file name casing in imports must match exactly what is on disk.

Mac and Linux file systems are case-sensitive. Windows is not. This means a developer on Windows can import `./userRouter` while the actual file is named `UserRouter.ts` and it works fine locally — but breaks the moment the code is deployed to a Linux server.

This setting catches that class of bug at compile time, before it ever reaches production.

## `resolveJsonModule`
```json
"resolveJsonModule": true
```

This allows you to import .json files directly in TypeScript:
```ts
import config from './config.json';
console.log(config.port);
```

Without this, TypeScript would throw an error on that import. Useful when you have configuration or static data stored in JSON format.

## `declaration` and `declarationMap`
```json
"declaration": true,
"declarationMap": true
```
These two work together.

- `declaration: true` — generates a .d.ts file alongside each compiled .js file. These are type definition files. They describe the types of your code so other TypeScript projects can consume it without needing your source.
- `declarationMap: true` — generates a source map for those .d.ts files, so editors can navigate from the declaration back to your original .ts source.

For a backend service these are optional, but keeping them on is a good habit — especially if your project ever evolves into something shared or reusable.

## `sourceMap`
```json
"sourceMap": true
```

When TypeScript compiles your code, the original `.ts` files become `.js` files. If an error occurs at runtime, Node.js points to a line in the compiled `.j`s file — which is hard to trace back to the TypeScript you actually wrote.

With `sourceMap: true`, TypeScript generates .map files that link the compiled JavaScript back to the original TypeScript source. Debuggers — including the one built into VS Code — use these maps to show you the exact line in your `.ts` file where something went wrong.

Always keep this **true** during development.

## `include`

```json
"include": ["src/**/*"]
```

This tells TypeScript which files to compile. The pattern src/**/* means: everything inside the src folder, no matter how deeply nested.

## `exclude`
```json
"exclude": ["node_modules", "dist"]
```
This tells TypeScript which folders to ignore completely.

- `node_modules` — installed packages. TypeScript should never try to compile these.
- `dist` — already compiled output. No point compiling it again

---
That covers every field in our config. Nothing in there is arbitrary — every setting has a reason, and now you know what each one is.