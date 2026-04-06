# Step 05 — Setup TypeScript

**Course Progress:** `05 / 08`

📅 **Published:** 22 March 2026  
🔄 **Last Updated:** 22 March 2026  
✍️ **Maintained By:** Kaustubh Gawade  

---

In the previous step, we installed all our essential dependencies — both runtime packages and development tools.

Now it's time to configure TypeScript.

TypeScript does not just work by installing it. After installation, it needs a configuration file to understand:

- how strict should it be when checking your code
- which files to compile
- where to put the compiled output
- what JavaScript version to compile down to
- and many more options

This configuration file is called **`tsconfig.json`**.

---

## What You Will Learn in This Step

In this step we will learn how to:

- exploring TypeScript — what it is, why it exists, and why it matters in production
- understand what `tsconfig.json` is and why it exists
- generate it using the `tsc --init` command
- configure it correctly for a Node.js backend project
- understand what each important field means

By the end of this step, your TypeScript will be fully configured and ready to use in your project.

---

## Exploring TypeScript
### What is TypeScript
**TypeScript is JavaScript** — with one superpower added on top: **types**.
That is the simplest way to put it. Every JavaScript file you have ever written is already valid TypeScript. TypeScript does not replace JavaScript. It sits on top of it, adds a layer of safety, and then steps aside.

The Problem with JavaScript
Let us look at a real situation.
You are building a backend. You have a function that calculates the total price of an order:
```js
function calculateTotal(price, quantity) {
  return price * quantity;
}

console.log(calculateTotal(100, 5));      // 500 ✅
console.log(calculateTotal("100", 5));    // "100100100100100" ❌
```
**Did you catch that?**  
When you pass a string "100" instead of a number 100, JavaScript does not throw an error. It just silently does something wrong. The * operator coerces the string and multiplies it — and you get garbage output.  

Now imagine this is not a toy example. Imagine this is an e-commerce backend processing real orders. That bug goes live. A customer gets charged the wrong amount. You get a 2am alert. You spend an hour tracing back through logs to find a one-character mistake.   

JavaScript never warned you. It had no way to.

**How TypeScript Solves It**  
Now look at the same function in TypeScript:

```ts
function calculateTotal(price: number, quantity: number): number {
  return price * quantity;
}

calculateTotal("100", 5);
// Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

TypeScript catches this **before your code ever runs** — right in your editor, the moment you make the mistake.

You do not need to run the server. You do not need to write a test for this. TypeScript just tells you: *"Hey, you said this would be a number. That is a string. Fix it."*

This is the core value of TypeScript — **moving errors from runtime to compile time**.


### What TypeScript Actually Is Under the Hood

Here is the mental model that matters:
```
Your TypeScript Code (.ts)
        ↓
  TypeScript Compiler (tsc)
        ↓
Plain JavaScript Code (.js)
        ↓
     Node.js runs it
```

Node.js never sees TypeScript. 
It cannot run TypeScript. The TypeScript compiler reads your `.ts` files, checks them for type errors, and outputs plain `.js` files. Node.js then runs those `.js` files — completely unaware that TypeScript was ever involved.

TypeScript is a development tool. It exists entirely to help you write better code. By the time your code reaches production, it is just JavaScript.

**Why It Matters in Production**   

As projects grow, three things happen:

- **Codebases get large.** When you have 50 files and 10,000 lines of code, you cannot hold everything in your head. Types act as documentation that never goes stale — you can look at a function signature and instantly know what it expects and what it returns.   
- **Teams get larger.** When multiple developers work on the same codebase, someone will eventually call your function with the wrong arguments. TypeScript makes that impossible to miss — the compiler catches it before the code is even committed.   
- **Refactoring becomes safe.** Imagine renaming a field that is used in 30 different places across your codebase. In JavaScript, you find out you missed one only when it breaks in production. In TypeScript, the compiler tells you every single place that needs to change — before you ship anything.   

This is why every serious production Node.js project today uses TypeScript. It is not a preference. It is the difference between code that scales and code that becomes a liability.

## What is `tsconfig.json` and Why Does It Exist
When you install TypeScript and write your first .ts file, TypeScript does not just automatically know what to do with it.  

It does not know:
- where your source files are
- where to put the compiled output
- how strict to be while checking your code
- which version of JavaScript to compile down to

It needs instructions. And those instructions live in a single file — `tsconfig.json`.

Think of it like this.
Imagine you hire a new developer and sit them down at the desk. They are skilled. They know TypeScript inside out. But before they start working, you need to brief them:

> "Our source files are in the src folder. Compiled output goes to dist. Be strict — do not let anything slide. We are targeting Node.js 20, so compile to ES2020."

`tsconfig.json` is that briefing. It is the set of instructions you give to the TypeScript compiler before it touches a single file.
Without it, the compiler either refuses to work or makes assumptions — and compiler assumptions in a production project are never a good thing.

*When you run yarn tsc --init in the next step, TypeScript will generate this file for you automatically. We will then go through each field together — and for every single one, we will understand why it is there before we set it.*

---

## Generating the tsconfig.json File
Run this command in your terminal:
```bash
yarn tsc --init
```

This command does one thing — it creates a `tsconfig.json` file in the root of your project.   
When you open it, you will see something like this:
```json
{
  // Visit https://aka.ms/tsconfig to read more about this file
  "compilerOptions": {
    // File Layout
    // "rootDir": "./src",
    // "outDir": "./dist",

    // Environment Settings
    // See also https://aka.ms/tsconfig/module
    "module": "nodenext",
    "target": "esnext",
    "types": [],
    // For nodejs:
    // "lib": ["esnext"],
    // "types": ["node"],
    // and npm install -D @types/node

    // Other Outputs
    "sourceMap": true,
    "declaration": true,
    "declarationMap": true,

    // Stricter Typechecking Options
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,

    // Style Options
    // "noImplicitReturns": true,
    // "noImplicitOverride": true,
    // "noUnusedLocals": true,
    // "noUnusedParameters": true,
    // "noFallthroughCasesInSwitch": true,
    // "noPropertyAccessFromIndexSignature": true,

    // Recommended Options
    "strict": true,
    "jsx": "react-jsx",
    "verbatimModuleSyntax": true,
    "isolatedModules": true,
    "noUncheckedSideEffectImports": true,
    "moduleDetection": "force",
    "skipLibCheck": true,
  }
}

```
A large file, most of it commented out. TypeScript generates this as a template — it shows you every possible option that exists, with the most commonly used ones already enabled.

> Note: This is just a starting point. This file is not ready to use as-is. Every project has different needs — different folder structures, different targets, different strictness requirements. We will modify this file to match exactly what our Node.js backend project needs.

> 💡Tip: You do not have to run yarn tsc --init at all. You can simply create a tsconfig.json file manually in the root of your project and paste your config directly into it. The command is just a convenience — the file itself is what TypeScript actually reads. 


### Our Final tsconfig.json
Replace the entire contents of the generated file with this:
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
Save the file.
> 🎯 These are the most commonly used fields you will need when starting out with TypeScript in a Node.js project. This config is a solid foundation — it covers the essentials and will get you up and running without any friction. As your project grows and your requirements change, you can always come back and tweak it.

---

### 🚀 A Quick Word Before We Move On

Hey — seriously, take a breath. 😌

You just set up your **`tsconfig.json`**. That might not feel like a big deal yet, but it is. You didn’t just copy-paste something blindly — you **created it**, you **made intentional choices**, and you **saved it**. That’s already a solid step forward. 💪

Now, let’s be real for a second —
We haven’t walked through what each field in that file actually means *yet*. That’s coming next. 👇

So if you looked at your config and thought: *“I have no idea what half of this is…”* That is **completely expected**. In fact, that’s *exactly* where you’re supposed to be right now. 🎯

### 🤝 A Truth Most Seniors Won’t Say Out Loud

Here’s something honest: **No one has `tsconfig.json` memorised.** Not even senior developers. If you ask an experienced TypeScript developer what something like *`declarationMap`* does on the spot, chances are:

* They’ll pause 🤔
* Maybe second-guess
* Probably open the docs 📖

And that’s *not* a weakness — that’s just how real-world development works.

💡 **You don’t need to know everything.**
You just need to know **where to find the answers**.

### 🔍 Where to Look When You're Stuck

Your first stop should always be:
👉 **The official [TypeScript documentation](https://www.typescriptlang.org/docs/)**

It’s honestly one of the best-written pieces of documentation out there — clear, detailed, and packed with practical examples. 📖✨

More importantly, it’s the source of truth — almost everything you’ll come across elsewhere (blogs, videos, tutorials) is ultimately derived from or inspired by this documentation.
But hey — if the docs don’t click immediately, that’s totally fine too.

Try other paths:

* 🎥 Watch a YouTube explanation
* 📝 Read a blog post
* 🤖 Ask an LLM (like this one!)

Because learning isn’t linear. Sometimes a concept only clicks after hearing it **3 different ways** — and that’s completely normal.

🌱 The Only Thing That Matters, at the end of the day: **Just keep going.** That’s it. That’s the whole game.

----

Refer this [tsconfig explaination](./typescript-config-explained.md) to know more about what we have configured in the project

---

Congratulations 🎉

That's it for the TypeScript config deep dive. You now know exactly what every field in your `tsconfig.json` does and why it is there.

---

> Back Step: **[Install Dependencies](../04-install-dependencies/README.md)**

> Next Step: **[Prettier Setup](../06-prettier-setup/README.md)**