# ESLint Config Deep Dive

Let's  walk through every single line of the `eslint.config.js` we set up, explain what it does, and more importantly, explain why it is there.

Here is the config we are working with:
```js
// @ts-check
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import importPlugin from 'eslint-plugin-import';
import globals from 'globals';

export default tseslint.config(
  {
    ignores: ['dist/', 'node_modules/', 'coverage/', '**/*.d.ts'],
  },
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  {
    files: ['**/*.ts'],
    languageOptions: {
      parser: tseslint.parser,
      globals: {
        ...globals.node,
      },
    },
    plugins: {
      import: importPlugin,
    },
    rules: {
      'no-console': 'warn',
      'prefer-const': 'error',
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
      '@typescript-eslint/no-explicit-any': 'warn',
      '@typescript-eslint/explicit-function-return-type': 'warn',
      'import/order': ['warn', { 'newlines-between': 'always' }],
    },
  }
);
```
Let us go through every line.

---

## Line 1 — `// @ts-check`
```js
// @ts-check
```
This is a special comment that tells VS Code to apply TypeScript type checking to this JavaScript file — even though it is a `.js` file.
Remember, `eslint.config.js` is a JavaScript file. By default, VS Code does not type-check JavaScript files. But by adding // @ts-check at the very top, you get:
- autocomplete for all ESLint config properties
- type errors if you pass wrong values to config options
- inline documentation when you hover over config properties

This does not affect how ESLint runs. It is purely a developer experience improvement — your editor becomes smarter while writing this file.

---

## Lines 3—6 — `Imports`
```js
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import importPlugin from 'eslint-plugin-import';
import globals from 'globals';
```
In the old ESLint format, plugins and configs were referenced as strings:
```json
{
  "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
  "plugins": ["import"]
}
```
In ESLint v9 flat config, everything is a proper JavaScript import. You import the actual package and use it directly. This is cleaner — you can see exactly what is being used and where it comes from.
Let us look at each import:
#### `@eslint/js`
```js
import eslint from '@eslint/js';
```
This is the official ESLint JavaScript ruleset package. It contains ESLint's built-in recommended rules for JavaScript — things like `no-unused-vars`, `no-undef`, `no-console`, and many more.
We import it so we can use `eslint.configs.recommended` later in the config.

#### `typescript-eslint`
```js
import tseslint from 'typescript-eslint';
```
This is the main typescript-eslint package — the bridge between ESLint and TypeScript.
Out of the box, ESLint only understands JavaScript. It cannot parse TypeScript syntax — things like type annotations, interfaces, generics, or decorators. This package does two things:
- provides a TypeScript parser so ESLint can read and understand .ts files
- provides a set of TypeScript-specific rules that catch issues specific to TypeScript code

Without this, ESLint would throw parse errors on every .ts file in your project.
It also provides the `tseslint.config()` helper function which we use to wrap our entire config — more on that below.

#### `eslint-plugin-import`
```js
import importPlugin from 'eslint-plugin-import';
```
This plugin adds rules around import and export statements. It catches things like:
- importing a module that does not exist
- importing the same module twice
- disorganised import order

For a backend project with many files, routes, services, and utilities, keeping imports clean and organised is important. This plugin enforces that automatically.

#### `globals`
```js
import globals from 'globals';
```
This package provides lists of global variables for different JavaScript environments.

Every environment has globals — variables that exist without you importing them. In a browser, `window`, `document`, and `localStorage` are globals. In Node.js, `process`, `__dirname`, `__filename`, and `Buffer` are globals.

Without telling ESLint which environment you are in, it does not know about these globals. 

It would flag `process.env.PORT` as an error because `process` was never declared — even though it is a perfectly valid Node.js global.

We import globals so we can tell ESLint: "*this is a Node.js project — know about Node.js globals.*"

---

## Line 8 — tseslint.config()
```js
export default tseslint.config()
```
This is the entry point of our config. `tseslint.config()` is a helper function provided by `typescript-eslint`.

You might wonder — why use `tseslint.config()` instead of just exporting a plain array?
Two reasons:
- **Type safety** — wrapping your config in `tseslint.config()` gives you full TypeScript type checking on every config object. Your editor will catch mistakes in the config itself — wrong property names, invalid values, and so on.
- **The extends property** — `tseslint.config()` adds support for an extends property on each config object, making it easier to extend shared configs for specific file patterns. This is not available in plain array syntax.

Think of it as the typed, enhanced version of a plain array export.

## The `ignores` Block
```js
{
  ignores: ['dist/', 'node_modules/', 'coverage/', '**/*.d.ts'],
},
```
This is the first config object in our array. It tells ESLint which files and folders to completely skip.

Placing `ignores` as its own object at the top without a `files` property makes it a `global` ignore — it applies to the entire config, not just one block.

As per your project structure, you can add or remove entries from this array, but this gives you a very good starting point.

Let us look at each entry:

- `dist/` — your compiled JavaScript output. This is generated by TypeScript. Linting it is pointless — it is not code you write, it changes every time you compile, and fixing lint errors in generated files does nothing useful.
- `node_modules/` — installed packages. Never lint these. They are not your code and linting them would take forever and produce thousands of irrelevant errors.
`coverage/` — test coverage reports generated by testing tools like Jest or Vitest. Auto-generated files that should never be touched.
`**/*.d.ts` — TypeScript declaration files. These are also generated — either by the TypeScript compiler or by packages. They should not be linted.

---

## `eslint.configs.recommended`
```js
eslint.configs.recommended,
```
This enables ESLint's core recommended ruleset — a carefully chosen set of rules that catch common JavaScript mistakes and bad practices.

Some examples of what this enables:
- `no-unused-vars` — flags variables that are declared but never used
- `no-undef` — flags variables that are used but never declared
- `no-unreachable` — flags code that can never be executed
- `no-duplicate-case` — flags duplicate cases in switch statements

These are baseline rules that every JavaScript project should have. We get all of them in one line.

---

## `...tseslint.configs.recommended`
```js
...tseslint.configs.recommended,
```
Notice the spread operator (...) here. This is because `tseslint.configs.recommended` is an array of config objects — not a single object. We spread it so each config object gets added to our array individually.
This enables typescript-eslint's recommended ruleset — TypeScript-specific rules on top of the ESLint base rules.

Some examples of what this enables:
- `@typescript-eslint/no-explicit-any` — warns when any type is used
- `@typescript-eslint/no-unused-vars` — a TypeScript-aware version of no-unused-vars
- `@typescript-eslint/no-require-imports` — disallows require() in TypeScript files where import should be used instead

These rules understand TypeScript deeply — they work with type information, not just syntax.

## The Main Config Object
```js
{
  files: ['**/*.ts'],
  languageOptions: { ... },
  plugins: { ... },
  rules: { ... },
}
```

This is the heart of our config. It is a config object that applies specifically to TypeScript files — notice `files: ['**/*.ts']`.
Let us go through each section inside it.

## `files`
```js
files: ['**/*.ts'],
```
This tells ESLint: "*Apply everything in this config object only to `.ts` files.*"
The pattern `**/*.ts` means: any file ending in `.ts`, anywhere in the project, at any depth.   
This is one of the most powerful features of flat config — you can apply different rules to different file types. Your TypeScript files get TypeScript-specific rules. If you had test files, you could give them different rules. Everything is explicit and scoped.

## `languageOptions`
```js
languageOptions: {
  parser: tseslint.parser,
  globals: {
    ...globals.node,
  },
},
```
This section configures how ESLint reads and understands your code.

`parser: tseslint.parser`

By default, ESLint uses its own JavaScript parser called Espree. Espree does not understand TypeScript syntax. When it encounters a type annotation like `user: User` or a generic like `Array<string>`, it throws a parse error.

Setting the parser to `tseslint.parser` replaces Espree with the TypeScript parser. ESLint can now read and understand all TypeScript syntax correctly.

`globals: { ...globals.node }`

This spreads all Node.js global variables into ESLint's awareness.   
Without this, ESLint would not know about process, Buffer, __dirname, __filename, setTimeout, setInterval, and other Node.js globals. It would flag every usage of these as no-undef errors — even though they are perfectly valid in a Node.js environment.

`...globals.node` gives ESLint the full list of everything that Node.js makes available globally.

## `plugins`
```js
plugins: {
  import: importPlugin,
},
```
This registers the eslint-plugin-import plugin under the namespace import.

In ESLint v9, plugins are registered as objects — not strings. The key (import) becomes the namespace used in rule names. That is why the import rules below are written as import/order — the import prefix comes from this registration.
Without registering the plugin here, any import/ rule in the rules section would throw an error saying the plugin is not found.

## `rules`
This is where we define the specific behaviour we want ESLint to enforce. Each rule can be set to one of three values:
- `"off"` — rule is disabled completely
- `"warn"` — rule triggers a warning. Code still runs, CI can be configured to pass or fail on warnings.
- `"error"` — rule triggers an error. This will fail your CI pipeline and block commits if Husky is set up.

Let us go through each rule:

### `no-console`
```js
'no-console': 'warn',
```
This warns whenever console.log, console.error, console.warn, or any other console method is used.
In a production backend, console.log is not the right way to log. It has no log levels, no timestamps, no structure, and no way to control output in different environments. You should be using a proper logger like `winston` or `pino` — which we already installed in `Step 04`.

Setting this to warn rather than error is intentional. During development, console.log is useful for quick debugging. A warning lets you know it is there without blocking your work. Before committing or deploying, you clean them up.

### `prefer-const`
```js
'prefer-const': 'error',
```
This throws an error whenever you use let to declare a variable that is never reassigned.
```ts
// triggers an error — value never changes, should be const
let userId = getUserId();

// correct
const userId = getUserId();
```
`const` communicates intent. When you see `const`, you immediately know this value will not change. When you see let, you expect it might. Using let everywhere removes that signal and makes code harder to reason about.

This is set to error because there is never a valid reason to use let for a variable that is never reassigned. It is always a mistake or an oversight.

### `@typescript-eslint/no-unused-vars`
```js
'@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
```
This throws an error for any variable, import, or function parameter that is declared but never used.
Unused variables are noise. They clutter the codebase, confuse other developers, and often indicate a bug — something was imported or declared that was supposed to be used but got forgotten.
The second argument { argsIgnorePattern: '^_' } is an exception — it tells ESLint to ignore function parameters that start with an underscore:
```ts
// no error — _req is intentionally unused, the underscore signals this
app.use((_req, res, next) => {
  res.setHeader('X-Request-Id', generateId());
  next();
});
```
This is a widely adopted convention. When you need to have a parameter in a function signature — because the function signature requires it — but you do not actually use that parameter, prefix it with _. ESLint will leave it alone.

### `@typescript-eslint/no-explicit-any`
```js
'@typescript-eslint/no-explicit-any': 'warn',
```
This warns whenever the any type is used explicitly in your code.
```ts
// triggers a warning
function processData(data: any) {
  return data.value;
}

// correct — be explicit about the type
function processData(data: RequestPayload) {
  return data.value;
}
```
Using any turns off TypeScript's type checking for that value entirely. It is the escape hatch that defeats the purpose of using TypeScript in the first place.

This is set to warn rather than error because there are rare legitimate cases where any is genuinely the right choice — for example when dealing with third-party libraries that have incomplete or incorrect type definitions. A warning lets you make a conscious decision rather than blocking you entirely.

### `@typescript-eslint/explicit-function-return-type`
```js
'@typescript-eslint/explicit-function-return-type': 'warn',
```
This warns whenever a function does not have an explicit return type annotation.
```ts
// triggers a warning — return type is inferred, not declared
function getUser(id: string) {
  return userRepository.findById(id);
}

// correct — return type is explicit
function getUser(id: string): Promise<User> {
  return userRepository.findById(id);
}
```
TypeScript can infer return types automatically — so why declare them explicitly?

**Two reasons**. First, explicit return types are documentation. Anyone reading the function signature immediately knows what it returns without having to trace through the implementation. Second, if you accidentally change what a function returns, TypeScript will catch the mismatch between the declared return type and the actual return value — protecting you from unintended breaking changes.

This is set to warn because in some cases — like simple utility functions or callbacks — requiring an explicit return type adds more noise than value. The warning prompts you to think about it without forcing your hand every time.

## `import/order`
```js
'import/order': ['warn', { 'newlines-between': 'always' }],
```
This warns when imports are not organised in a consistent order, and requires a blank line between different groups of imports.   

ESLint groups imports into categories:
1. Built-in Node.js modules (path, fs, os)
2. External packages (express, axios, winston)
3. Internal project files (./routes/user, ../utils/logger)

With `newlines-between: 'always'`, each group must be separated by a blank line:
```ts
// correct — groups separated by blank lines
import path from 'path';
import fs from 'fs';

import express from 'express';
import axios from 'axios';

import { UserRouter } from './routes/user';
import { logger } from './utils/logger';

// incorrect — all imports mixed together with no separation
import express from 'express';
import path from 'path';
import { UserRouter } from './routes/user';
import axios from 'axios';
```
Organised imports make it immediately clear what your file depends on — which things come from Node.js, which are third-party packages, and which are your own code. In a large backend with many files this matters a lot.

That covers every line in the config. Nothing in there is arbitrary — every decision has a reason behind it, and now you know what each one is.

---

This ESLint config is a solid starting point — but it is just that, a starting point. As your project grows, your team expands, and your requirements become more specific, there will come a time when you need to come back and tweak this file. You will be amazed by the sheer number of built-in rules ESLint offers — and you do not need to learn them all. Research them as you need them, add them when they solve a real problem you are facing. And when you reach a point where built-in rules are not enough, ESLint also allows you to write your own custom rules tailored exactly to your project's needs. The config you have today will evolve — and that is completely by design.

You now have a deep understanding of your ESLint setup. Every rule is intentional, every config option is deliberate.


Back Step: [ESLint Setup](./README.md)
