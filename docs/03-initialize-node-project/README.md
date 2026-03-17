# Step 03 — Initialize the Node Project

**Course Progress:** `03 / 08`

📅 **Published:** 16 March 2026  
🔄 **Last Updated:** 16 March 2026  
✍️ **Maintained By:** Kaustubh Gawade  

---

> 📌 Heads up: This is going to be a longer section — but it's one of the most important ones. Before jumping to the steps, please read through the initial paragraphs. They'll give you the context you need to actually understand what you're doing and why, instead of just blindly running commands.

---

## What is package.json?
Think of `package.json` as the identity card of your Node.js project. Just like how a passport holds all the essential information about a person — name, nationality, date of birth — package.json holds all the essential information about your project: what it's called, what version it's on, who wrote it, what scripts you can run, and most importantly, what other packages and tools it depends on to function.   

Here's another way to think about it. Imagine you're writing a recipe. The recipe doesn't contain the actual ingredients — it just lists them with the right quantities so that anyone who reads it knows exactly what to buy. package.json works the same way. It doesn't contain your dependencies inside it, it just lists them so that anyone who clones your project can run a single command and get everything they need installed automatically.

---

## Why is it so important in Node.js projects?

In Node.js, almost every project relies on external packages — libraries and tools written by other developers that you pull into your project to avoid reinventing the wheel. Things like Express for building servers, dotenv for managing environment variables, or nodemon for auto-restarting your server during development.

These packages live inside your `node_modules/` folder. But as you already know from the `.gitignore` section, `node_modules/` never gets pushed to GitHub — it's too large and completely unnecessary to share. So the question is: when someone clones your project, how do they know which packages to install?

The answer is `package.json`. It keeps a record of every package your project needs, along with the version ranges that are known to work. Anyone who clones your repo just runs `yarn install` (or `npm install`) and the entire `node_modules/` folder gets recreated on their machine automatically — no manual tracking needed.
Beyond dependencies, `package.json` also lets you define scripts — shortcuts for common commands. Instead of typing out a long command every time you want to start your server, you can define it once in `package.json` and just run yarn start from that point on.

---

## How do you create a `package.json`?   

You don't write it by hand — you initialize it using a package manager. A package manager is a tool that handles installing, updating, and removing packages in your project, and it uses package.json as its source of truth.

The most popular package managers for Node.js are npm and Yarn. npm comes bundled with Node.js by default, so it's available the moment you install Node. Yarn is a separate tool built by Meta as an alternative to npm, and pnpm is another modern option that's gaining popularity for being extremely fast and efficient with disk space. For most projects, any of these will get the job done — the core concepts are the same across all of them.

For this course and examples we will use **Yarn** package manager. You can translate commands to `npm` or `pnpm` later if needed.

---

## Checking if Yarn is installed

Before initializing anything, let's make sure Yarn is available on your system.  
Open your terminal and run:
```
yarn --version
```

If you see a version number printed out (e.g. 1.22.19), you're good to go. If you get an error saying the command is not found, you'll need to install Yarn first.

> ⚠️ If Yarn isn't installed yet, refer to this first: [How to install Yarn](./Install_Yarn.md)

---

## A few essential Yarn commands to know

Before we jump into initializing your project, here's a quick reference for the Yarn commands you'll be using regularly. You don't need to memorize these right now — just read through them once so they feel familiar when you encounter them.

- `yarn -v` checks which version of Yarn is installed on your system (short for `yarn --version`).
- `yarn init` creates a new package.json file in your project by walking you through a few simple questions about your project.
- `yarn add` <package-name> installs a package and adds it to your package.json as a dependency — meaning it's required for your project to run in production.
- `yarn add` <package-name> -D installs a package as a dev dependency, meaning it's only needed during development and not in production. Tools like nodemon or testing libraries are good examples of dev dependencies.
- `yarn remove` <package-name> uninstalls a package and removes it from your package.json.
- `yarn install` reads your package.json and installs all the listed dependencies. This is the command anyone would run after cloning your project.

> 💡 Tip: The -D flag is short for --dev. Keeping your dev dependencies separate from your regular dependencies is a good habit — it keeps your production environment lean and clean

---

## Initializing Your Project with yarn init

To create a `package.json` file, you use the `yarn init` command. When you run it, Yarn doesn't just silently generate a file — it starts an interactive session right in your terminal, asking you a series of simple questions about your project: things like the project name, version, description, entry point, and author. Once you answer them all, it takes your inputs and generates a `package.json` file tailored to your project.

Now, if you're in a hurry or just want to get something up quickly without answering each question one by one, there's a shortcut:
```
yarn init -y
```
The -y flag stands for "yes" — it tells Yarn to skip all the questions and accept the default value for everything, instantly producing a `package.json` with sensible defaults filled in.

Both approaches produce the same file in the end — the difference is just how much control you want over the initial values. And honestly, neither choice is permanent. You can always open `package.json` and edit any field by hand after it's been created.

> 💡 Tip: It's worth trying both commands at least once to see how they differ. Run `yarn init` in one project folder to go through the interactive flow, then try `yarn init` -y in another to see how it fills in the defaults automatically. Comparing the two outputs side by side is a great way to understand what each field in `package.json` actually means.

*With that context in place, let's walk through `yarn init` step by step so you know exactly what each prompt is asking and what you should enter.*

---

## Steps — Walking Through `yarn init`

Open your terminal, navigate to your project folder (the one you cloned from GitHub), and run:
```
yarn init
```

Yarn will now walk you through a series of prompts, one by one. Let's go through each of them using our example project — a website time tracker extension — so you know exactly what to enter and why.

--- 

### Step 1 — Question: name
```
question name (your-folder-name):
```
This is the name of your project. If you cloned your repository from GitHub first, Yarn will pre-fill this with your repository name — which in most cases is exactly what you want, so you can just press **Enter** to accept it. 

If you started fresh locally without cloning, Yarn may fall back to your folder name as the suggestion or you have to fill the name as manually for the app and you can accept it. Either way, the same rules apply — keep it lowercase, short, and use kebab-case if it has multiple words.

For our example, Yarn would have already suggested `website-time-tracker-extension`, so just press **Enter**:

For our example, we'd enter:
```
website-time-tracker-extension
```

---

### Step 2 — Question: version
```
question version (1.0.0):
```

This is the version number of your project. Yarn defaults to `1.0.0` and in almost all cases, you should just press **Enter** and accept it. Version numbers follow a system called **Semantic Versioning** (SemVer) — `major.minor.patch` — and you'll increment them as your project evolves. For now, `1.0.0` is the right starting point.

For our example, just press **Enter** to accept the default:
```
1.0.0
```

---

### Step 3 — Question: description
```
question description:
```

This is the same idea as the GitHub repository description — one clean sentence that explains what your project does. It shows up in `package.json` and also if you ever publish your package to the npm registry. You can copy paste the description which you had given when creating github repo.

Don't overthink this too much — you can always come back and edit the description directly in package.json manually as your project evolves and you have a clearer picture of what it does.

For our example:
```
A browser extension that tracks the time you spend on each website
```

---

### Step 4 — Question: entry point
```
question entry point (index.js):
```

This tells Node.js which file to run first when your project is started — essentially the "main" file of your application. Yarn defaults to `index.js`, and for most Node.js projects this is the standard convention you should stick with.

As your project grows and evolves though, this may change. For example, if you move to TypeScript you might rename it to `index.ts`, or as your project structure gets more organised you might restructure it to something like `src/main.ts`. When that happens, you don't need to re-run `yarn init` — just open your `package.json` and update the `"main"` field directly to reflect the new entry point.

For our example, press **Enter** to accept the default:
```
index.js
```

> 💡 Tip: Even if you haven't created index.js yet, that's completely fine — you're just declaring the intent here. You'll create the actual file when you start writing your code.

---

### Step 5 — Question: repository url
```
question repository url:
```

If you cloned your repository from GitHub before running `yarn init`, Yarn is smart enough to detect the remote URL from your local Git configuration and pre-fill it here automatically — so you can just press **Enter** to accept it.

If however you started your project locally without cloning from GitHub first, this field will be empty and you'll need to paste your GitHub repository URL manually. It's optional, but filling it in is good practice — it links your `package.json` directly to the source code on GitHub, which becomes especially useful if you ever share or publish the project.

For our example, since we cloned first, Yarn would have already filled this in:
```
https://github.com/your-username/website-time-tracker-extension
```

---

### Step 6 — Question: author
```
question author:
```

This is simply your name or your GitHub username. It records who built the project. Keep this default for now, it can be changed later manually in package.json

For our example:
```
your-name
```

---

### Step 7 — Question: license
```
question license (MIT):
```

This corresponds to the license you chose when setting up your GitHub repository. Yarn defaults to `MIT`, which as we discussed earlier is the right choice for most personal and open-source projects. Just press **Enter** to accept it.


For our example, press **Enter** to accept the default:
```
MIT
```

### Step 8 — Question: private
```
question private:
```

This prompt asks whether your project should be marked as private in `package.json`. Just press **Enter** or type `true` to leave it as private for most personal projects.

> **⚠️ Note:** This has nothing to do with the **Public / Private visibility** you set on GitHub earlier. That setting controls who can *see your repository on GitHub*. This `private` field in `package.json` is specifically about the **npm registry** — setting it to `true` prevents the package from being accidentally published to npm if you or someone on your team ever runs `npm publish` or `yarn publish`. For most personal and learning projects where you're not publishing a package, you can mark it as `true` or just press **Enter** to skip it.

For our example, enter true explicitly and just press **Enter**:
```
true
```

---

### Step 8 — Confirm and generate

Once you've answered all the prompts, Yarn immediately saves the file and prints:

```
success Saved package.json
```
Now if you see in your project folder and you'll find your freshly created package.json sitting there. For our website time tracker example it would look something like this:

```
{
  "name": "website-time-tracker-extension",
  "version": "1.0.0",
  "description": "A browser extension that tracks the time you spend on each website",
  "main": "index.js",
  "repository": "https://github.com/your-username/website-time-tracker-extension",
  "author": "your-name",
  "license": "MIT"
}
```
That's your package.json — clean, minimal, and ready to grow as your project evolves. From here, every package you install with yarn add will automatically get recorded in this file under the dependencies or devDependencies field.

---

Congratulations 🎉

Your repository is now officially a **Node.js project**. You've gone from an empty GitHub repository all the way to a properly initialized local project with a `package.json` that describes exactly what your project is and who built it. Every professional Node.js project you'll ever come across starts with this exact foundation — and now yours does too.

From here, as you start installing packages with `yarn add`, your `package.json` will grow and evolve alongside your project, quietly keeping track of everything your project needs to run.

If you're curious about what `yarn init -y` would have produced instead — with all the defaults accepted in one go — [see the example here](../../examples/package.json) and compare it to what we just built step by step.

---

> Back Step: **[Clone Repository](../02-clone-repository/README.md)**
> Next Step: **[Initialize Node Project](../04-install-dependencies/README.md)**
