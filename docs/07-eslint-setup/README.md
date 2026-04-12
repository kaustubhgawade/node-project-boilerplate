# Step 07 — Setup ESLint

**Course Progress:** `07 / 08` 

📅 **Published:** 08 April 2026  
🔄 **Last Updated**: 11 April 2026  
✍️ **Maintained By**: Kaustubh Gawade

---

In the previous step, we set up Prettier to enforce consistent formatting across the project. Every file now looks the same regardless of who wrote it.

But formatting is only half the story.

Formatting tells you how your code looks. It does not tell you whether your code works correctly. You can have perfectly formatted code that has unused variables, missing return types, unsafe type usage, and broken imports — and Prettier will not catch any of it.

That is where ESLint comes in.

---

## What You Will Learn in This Step

In this step we will learn how to:
- understand what ESLint is and what problem it solves
- understand the difference between ESLint and Prettier
- understand ESLint v9 and the flat config system
- create and configure `eslint.config.js` for a Node.js TypeScript backend
- understand what each rule and field in the config does
- add ESLint as a script in `package.json`
- set up the ESLint VS Code extension
- understand how ESLint fits into your CI pipeline

By the end of this step, your project will have a linting setup that catches real code quality issues before they ever reach production.

---

## What is ESLint

ESLint is a linter — a tool that statically analyses your code and flags patterns that are problematic, unsafe, or simply bad practice.

Where Prettier looks at your code and asks "*does this look right?*", ESLint looks at your code and asks "*does this work correctly?*"

Some examples of what ESLint catches:
- a variable that is declared but never used
- a function that sometimes returns a value and sometimes returns nothing
- usage of `any` type where a proper type should be defined
- an import that points to a module that does not exist
- a `let` declaration that is never reassigned and should be `const`

None of these are formatting issues. Prettier would never catch them. They are code quality issues — and in a production backend, they are the kind of thing that causes bugs, crashes, and incidents at 2am.

---

## ESLint and Prettier — A Clear Boundary

Since we just set up Prettier in the previous step, it is worth being clear about where one ends and the other begins.

**Prettier** — handles how your code looks. Indentation, quotes, semicolons, line length, trailing commas. Pure formatting.

**ESLint** — handles how your code works. Unused variables, type safety, import correctness, logic issues. Code quality.

They are complementary tools. In a production project you need both — and they are designed to work alongside each other without conflict.

---

## ESLint v9 and the Flat Config System

If you have worked with ESLint before, you may be familiar with the old `.eslintrc.json` or `.eslintrc.js` config file format. That format is now deprecated in ESLint v9 and will be removed in a future version.

ESLint v9 introduced a new system called **flat config**. The config file is now named `eslint.config.js` and it works differently from the old format in a few important ways:

- instead of a JSON file with string-based extends and plugins, it is a JavaScript file where everything is imported directly as a module
- instead of a single object with nested properties, it exports an array of config objects — each one applying to specific files or scenarios
- there is no more implicit config merging or cascading — what you see in the file is exactly what ESLint uses

This is cleaner, more explicit, and much easier to reason about. We will be using flat config throughout this step.

#### Why `eslint.config.js` and not `eslint.config.ts`

Since your entire project is written in TypeScript, a natural question is — should this file be `.ts` as well?

The answer is no — and here is why.

`eslint.config.js` is not part of your application code. It is a tooling config file that ESLint reads directly at runtime using Node.js — before any compilation happens. Node.js cannot run `.ts` files natively. If you named this file `eslint.config.ts`, ESLint would need something to compile it before it could even read it — *a chicken and egg problem*.

ESLint v9 does support TypeScript config files but requires an additional dependency (`jiti`) to make it work. That adds unnecessary complexity for a file that does not need it.

The pattern to follow is simple:
- Application code → lives in `src/` → TypeScript → compiled by `tsc`
- Tooling config files → live at the project root → plain JavaScript or JSON → read directly by the tools

Keep them separate. `eslint.config.js` at the root as a `.js` file is the correct, standard approach.

And since it is a `.js` file living outside `src/`, it does not need to be in your `tsconfig.json` either. TypeScript will not touch it.

---

## Setting Up ESLint

Create eslint.config.js in the root of your project:
```bash
touch eslint.config.js
```
Then open it and paste the following config:
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
Save the file.

> If you want to understand what each section and rule in this config does and why we chose it, we have a dedicated deep dive for it. Head over to [ESLint Config Deep Dive →](./eslint-config-explained.md)

---

## Adding ESLint Scripts to `package.json`

Just like we added a format script for Prettier, add these scripts to your `package.json`:
```json
"scripts": {
  "lint": "eslint .",
  "lint:fix": "eslint . --fix"
}
```
Now anyone on the team can run:
```bash
yarn lint
```
to check the entire project for linting issues, or:
```bash
yarn lint:fix
```
to automatically fix everything ESLint is able to fix on its own. Note that not all rules are auto-fixable — some issues require a developer to make a conscious decision about how to fix them.

#### Verifying the Setup
Run the linter to make sure everything is working:
```bash
yarn lint
```
If there are no issues, you will see no output — silence means success.

If there are issues, ESLint will list every file and every line where something needs attention, along with the rule that was violated. Read each one carefully — they are not noise, they are real issues in your code.

#### ESLint and Prettier — Making Sure They Do Not Conflict
ESLint has some rules that overlap with what Prettier does — things like enforcing semicolons or quote style. If both tools are active and configured differently, they will fight each other and you will get errors that are impossible to satisfy.

The fix is to tell ESLint to back off on formatting rules and let Prettier own that territory entirely. Install this package:

```bash
yarn add -D eslint-config-prettier
```
Then add it to your `eslint.config.js`:
```js
import prettierConfig from 'eslint-config-prettier';

export default tseslint.config(
  // ... existing config
  prettierConfig, // add this at the end
);
```

Placing it at the end is important — it disables any ESLint rules that would conflict with Prettier, so Prettier always wins on formatting.

---

## The ESLint VS Code Extension
Just like Prettier, ESLint has a VS Code extension that brings linting directly into your editor — underlining problems as you type, without needing to run a terminal command.
Install it from the VS Code marketplace:  
👉 [ESLint - VS Code Extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 

Once installed, add these settings to your `.vscode/settings.json`:
```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "eslint.validate": ["javascript", "typescript"]
}
```

`source.fixAll.eslint` — automatically applies all auto-fixable ESLint fixes every time you save a file  
`eslint.validate` — tells the extension which file types to lint

These settings should be committed to GitHub alongside your `.vscode/settings.json` so every developer on the team gets the same editor behaviour automatically.

---

## Taking It Further — Git Hooks and GitHub Actions
Right now ESLint runs when someone manually calls `yarn lint`. In a production project, you want linting to be automatic and enforced.

**Git hooks** with `husky` and `lint-staged` — ESLint runs automatically before every commit. If there are linting errors, the commit does not go through. We will set this up in a later step.

**GitHub Actions** — ESLint runs as part of your CI pipeline on every pull request using `yarn lint`. If linting fails, the PR cannot be merged. This is the safety net that ensures nothing slips through regardless of local setup.

---

Congratulations 🎉

Your project now has both formatting and linting fully in place. Prettier keeps your code consistent. ESLint keeps your code correct. Together they form the foundation of a production-grade codebase.

---

> Back Step: **[Prettier Setup](../06-prettier-setup/README.md)**

> Next Step: **[Husky Setup](../08-husky-setup/README.md)**