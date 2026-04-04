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

## target
```json
"target": "ES2024"
```
This tells the TypeScript compiler: **"When you convert my TypeScript into JavaScript, which version of JavaScript should the output be?"**  

JavaScript has evolved over the years. Each new version — ES2018, ES2019, ES2020, ES2024 — introduced new features and syntax. When TypeScript compiles your code, it needs to know which version to compile down to.

**How to Decide Which Version to Use**  
This is where a lot of developers just pick a number without thinking. Here is how to make a smart choice.

Your target should match what your Node.js version actually supports.
Every version of Node.js supports a specific set of JavaScript features. If your Node.js version supports ES2024 natively, there is no reason to compile down to ES2020 or lower — you would just be making TypeScript do unnecessary transformation work.

The way to check this is simple. Visit [node.green](https://node.green/) — it is a compatibility table that shows exactly which JavaScript features each Node.js version supports. Find your Node.js version, see how far up the ES standard it goes, and set your target accordingly.
For example, if you are running Node.js 20 or above, ES2024 is fully supported — so we set target to ES2024.

The rule of thumb: Set target to the highest ES version your Node.js runtime supports. There is no benefit in compiling down further than you need to.

**What Happens if You Set It Too Low**  
If you set target to something older than what your runtime supports — say ES5 — TypeScript has to transform modern syntax into older equivalent code. The only reason to target a lower version is when you must support older environments (e.g., older Node versions or browsers). Since you control your Docker/K8s environment and are using Node 26, you can safely set the target to the highest ES version that your Node version fully implements.   

For example, a simple async/await function would get compiled into a deeply nested chain of callbacks and state machines just to make it ES5 compatible. The code would still work — but the compiled output becomes unreadable, bloated, and much harder to debug.
Set it to match your runtime. Keep it clean.


## `module`
```json
"module": "commonjs"
```
This tells the compiler: **"What module system should the output JavaScript use?"**

To understand why this setting exists, we need to take a small step back.

#### The Problem It Solves
JavaScript was not originally designed with modules in mind. In the early days, there was no built-in way to split code across multiple files and import them into each other. As JavaScript grew, different environments solved this problem in different ways — and that created two competing systems that exist to this day.

#### The Two Module Systems
CommonJS was created for Node.js. It uses `require()` to bring in other files and `module.exports` to expose things from a file:
```js
const express = require('express');
module.exports = myFunction;
```
ESModules (ESM) is the modern standard built into the JavaScript language itself. It uses `import` and `export`:
```js
import express from 'express';
export default myFunction;
```
ESModules is what browsers natively understand today, and it is also what TypeScript syntax looks like when you write it.

#### So What Does This Setting Actually Do
Here is where it gets interesting.   

You write your TypeScript using `import` and `export` — the modern ESModule syntax. But your Node.js backend expects CommonJS. These two need to be reconciled.  
The `module` setting tells TypeScript: *"When you compile my code, convert the module syntax into this format."*  

So when you write:
```ts
import express from 'express';
```
TypeScript compiles it to:
```js
const express = require('express');
```
Your source code stays clean and modern. The output is what Node.js actually needs.

**Why `commonjs` for a Node.js Backend**  
Node.js has been adding ESModule support over recent versions, but CommonJS remains the default and the most battle-tested option for backend projects. Most packages on npm are still written in CommonJS, tooling support is more mature, and you are far less likely to run into edge cases.  
As the ecosystem matures, ESModules on Node.js will become the standard. But for a production backend today, `commonjs` is the safe, reliable choice.


## `lib`
```json
"lib": ["ES2024"]
```
Think of TypeScript as having two separate jobs.  

The first job is reading your code and understanding the syntax — things like arrow functions, async/await, destructuring, and so on. The target field handles this.  

The second job is knowing about the built-in APIs that JavaScript provides — things like Promise, Array.prototype.flatMap, Object.fromEntries, structuredClone, and so on. These are not syntax. They are actual objects and methods that live inside the JavaScript runtime.  
The lib field handles this second job.

#### Why This Matters
When you write something like:   
```ts
const result = await Promise.allSettled([fetch(url1), fetch(url2)]);
```
TypeScript needs to know what Promise.allSettled is. It needs to know what arguments it accepts, what it returns, and how to type-check your usage of it.

This knowledge does not come from your code. It comes from TypeScript's built-in type definition files — and lib controls which ones get loaded.  

If you set lib too low, TypeScript simply does not know about APIs that were introduced in later versions. It will throw an error on perfectly valid code — not because your code is wrong, but because you did not tell TypeScript those APIs exist.

#### How It Relates to target
A good rule of thumb: keep `lib` and `target` in sync.   

If you set `target` to ES2024, set `lib` to ["ES2024"] as well. They are describing the same thing from two different angles — `target` says *"compile the syntax to this version"*, and `lib` says *"recognise the built-in APIs of this version"*.    

Mismatching them leads to confusing situations where your code compiles fine syntactically but TypeScript complains about built-in methods it does not recognise.

#### It Accepts Multiple Values
Notice that `lib` takes an array — and that is intentional. You can pass multiple values when your project runs across different environments that each have their own set of built-in APIs.   
For example, if you were building a project that runs in both Node.js and the browser, you might write:
```json
"lib": ["ES2024", "DOM", "DOM.Iterable"]
```

ES2024 — gives TypeScript knowledge of modern JavaScript built-ins
DOM — gives TypeScript knowledge of browser APIs like document, window, fetch, localStorage
DOM.Iterable — adds iterable support for DOM collections like NodeList

For our project, we are building a pure Node.js backend — there is no browser involved. So a single "ES2024" is all we need. But it is good to know the door is open when your use case grows.

## `outDir`
```json
"outDir": "./dist"
```

This tells the compiler: **"Put all your compiled JavaScript output in this folder."**

#### Why Does This Folder Even Need to Exist

Remember — Node.js cannot run TypeScript. It only runs JavaScript.

So when you write your code in `.ts` files inside `src/`, TypeScript needs to compile them into `.js` files before Node.js can execute anything. But it cannot just overwrite your original `.ts` files — that would destroy your source code.

So it needs a separate place to put the compiled output. You can technically name this folder anything you want — `build/`, `out/`, `compiled/`, `output/` — TypeScript does not care. But by convention, the vast majority of Node.js and TypeScript projects use `dist/`, short for distribution. Stick with it unless you have a specific reason not to — it is what other developers will expect to see when they open your project.

#### What Happens if You Do Not Set `outDir`

If you do not specify `outDir`, TypeScript does not create a separate output folder. Instead, it drops the compiled `.js` files **right next to your `.ts` files** — inside `src/` itself.

So your `src/` folder starts looking like this:
```
src/
  index.ts
  index.js        ← TypeScript put this here
  routes/
    user.ts
    user.js       ← and this
  utils/
    logger.ts
    logger.js     ← and this
```

Your source folder becomes a mess of `.ts` and `.js` files mixed together. It gets confusing fast — you cannot tell at a glance which files are source and which are generated. Tooling gets confused. Git picks up files you never intended to track.

Always set `outDir`. Keep your source and your output cleanly separated.

#### What Actually Happens During Compilation

When you run `yarn tsc`, TypeScript reads every `.ts` file inside `src/` and produces a corresponding `.js` file inside `dist/`. The folder structure is mirrored exactly.

So:
```
src/index.ts          →    dist/index.js
src/routes/user.ts    →    dist/routes/user.js
src/utils/logger.ts   →    dist/utils/logger.js
```
Your Node.js process then runs from `dist/` — never from ``src/``. The `src/` folder is for you, the developer. The `dist/` folder is for the runtime.

**The Two Worlds of Your Project**  
It helps to think of your project as having two worlds:

`src/` — your world. This is where you write, read, and think. TypeScript, types, and all the good stuff lives here.  
`dist/` — the machine's world. This is plain JavaScript that Node.js can actually execute. You rarely need to look in here.

You work in `src/`. The server runs from `dist/`. TypeScript is the bridge between the two.

#### Never Commit `dist/` to GitHub
The `dist/` folder is generated output — it is not source code. It gets created fresh every time you run the compiler.   
Committing it to GitHub would be like committing the compiled binary of a C program alongside the source. It is redundant, it bloats your repository, and it causes unnecessary merge conflicts when multiple developers are compiling on their own machines.

If you have already followed **Step 01** of this tutorial where we created the GitHub repository, `dist/` is already taken care of. When you selected the .gitignore template for Node.js during repo setup, Git automatically included `dist/` in the ignore list for you.


## `rootDir`
json
```
"rootDir": "./src"
```

This tells the compiler: **"My TypeScript source files live inside this folder. Do not look anywhere else."**

#### What It Actually Does

When TypeScript compiles your project, it needs to know where the *root* of your source code is. `rootDir` establishes that anchor point.

But it does more than just tell TypeScript where to look. It also controls how the output is structured inside `dist/`.

TypeScript mirrors the folder structure of `rootDir` inside `outDir`. So if your source looks like this:
```
src/
  index.ts
  routes/
    user.ts
  utils/
    logger.ts
```

After compilation, `dist/` will look exactly the same — just with `.js` files instead of `.ts`:
```
dist/
  index.js
  routes/
    user.js
  utils/
    logger.js
```

The structure is preserved perfectly. This matters when your code imports other files — the relative paths in the compiled output stay correct.

#### What Happens if You Do Not Set It

If you do not set `rootDir`, TypeScript does not just give up — it tries to figure out the root on its own. It looks at every file it is asked to compile, finds the deepest folder that all of them share in common, and treats that as the root. This is called the common ancestor of your source files.

Most of the time this works fine. But sometimes TypeScript picks a root higher up than you expect — like the project root itself instead of `src/`. When that happens, your `dist/` folder ends up with an unexpected nested structure:
```
dist/
  src/           ← TypeScript added this extra layer
    index.js
    routes/
      user.js
```
That extra `src/` folder inside `dist/` breaks your paths and causes confusing errors. Setting rootDir explicitly removes any ambiguity — TypeScript knows exactly where your source starts.

**How It Differs from include**  
This is a question that trips up a lot of developers, so it is worth being clear about.


- `include` is a top-level field in tsconfig.json — not inside compilerOptions. It tells TypeScript which files to compile. Think of it as the selection step — you are pointing TypeScript at the files it should even bother looking at.
- `rootDir` on the other hand does not select files. It tells TypeScript where the root of your source tree is — so that when it mirrors your folder structure into dist/, it knows where to start from.

They do two different jobs:

`include` says — "compile these files"   
`rootDir` says — "when you do, treat this folder as the root of the tree"

A simple way to think about it: include is the what, rootDir is the from where.

#### The Name is Just a Convention
You can technically name this folder anything — `source/`, `app/`, `ts/`, whatever you like. TypeScript does not care what the folder is called.  

But `src/` is the overwhelmingly common convention across the Node.js and TypeScript ecosystem. Every developer who opens your project will immediately know where to look. Stick with `src/` unless you have a specific reason to deviate.


## `strict`
```json
"strict": true
```

If there is one setting in this entire file that you should understand deeply — it is this one.

#### What It Does
Setting strict to true does not turn on a single rule. It is a shorthand that enables a collection of strict type-checking rules all at once. Instead of turning each one on individually, one flag covers all of them.  

The most important ones it enables are:  
`strictNullChecks`  
Without this, `null` and `undefined` can be assigned to any variable without TypeScript complaining. This is one of the most common sources of runtime crashes in JavaScript — the infamous *Cannot read properties of undefined* error.
```ts
// Without `strictNullChecks` — TypeScript is fine with this
let user: User = null; // no error
console.log(user.name); // crashes at runtime

// With `strictNullChecks` — TypeScript catches it immediately
let user: User = null; // Error: Type 'null' is not assignable to type 'User'
```


`noImplicitAny`   
When TypeScript cannot figure out the type of something and you have not told it, it has two choices — throw an error or silently assume `any`. `any` essentially turns off type checking for that value entirely.   
Without this rule, TypeScript silently assumes `any` and lets everything through. With it, TypeScript forces you to be explicit.
```ts
// Without noImplicitAny — TypeScript silently assumes `any`
function process(data) { // TypeScript assumes data: any — no error
  return data.value;
}

// With noImplicitAny — TypeScript forces you to declare the type
function process(data: RequestData) { // you must be explicit
  return data.value;
}
```

`strictFunctionTypes`  
Makes function type checking more thorough — ensures that when you pass a function as an argument, its parameter types and return types are checked correctly in both directions.

`strictPropertyInitialization`   
Ensures that every property declared in a class is either given a default value or assigned in the constructor. Prevents the case where a class property exists in the type definition but is undefined at runtime because it was never actually set.

#### Why Turning It Off is a Mistake   
When developers are new to TypeScript,` strict: true` can feel overwhelming. Suddenly there are errors everywhere. The temptation is to set `strict: false` to make the red underlines go away and move on.   

This is the wrong approach.  

Those errors are not TypeScript being difficult. They are TypeScript pointing at real problems in your code — bugs that would have silently made it to production in plain JavaScript. Turning off strict does not fix the bugs. It just hides them.


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