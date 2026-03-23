# Step 04 — Install Essential Dependencies

**Course Progress:** `04 / 08`

📅 **Published:** 17 March 2026  
🔄 **Last Updated:** 17 March 2026  
✍️ **Maintained By:** Kaustubh Gawade  

---

In the previous step, we initialized our Node.js project by running `yarn init`, which created our `package.json` file.

Think of `package.json` as the **identity card of your project** — it holds the project name, version, scripts, and most importantly, the list of all packages your project depends on.

Right now, that file exists but our project has **no packages installed yet**.

That changes in this step.

---

## What You Will Learn in This Step

In this step we will learn how to:
- understand the difference between `dependencies` and `devDependencies`
- install runtime packages like `dotenv`, `winston`, `axios`, `uuid`, and `cors`
- install development tools like `typescript`, `@types/node`, `nodemon`, `eslint`, and `prettier`
- understand what each package does and why we need it
- verify that all packages are installed correctly

By the end of this step, you will have all the **essential packages installed and ready**, so your Node.js project is one step ahead to set up for both development and production.

---

## Understanding `dependencies` vs `devDependencies`

Before we install anything, we need to understand **where** each package belongs — and why it matters.

### The Analogy: Building a House

Imagine you are **constructing a house**.

You need two kinds of things:
- **Things that become part of the finished house:** bricks, cement, wiring, plumbing, windows
- **Tools you use only during construction:** a drill machine, scaffolding, a spirit level, a measuring tape

Once the house is built and someone moves in, they don't need the drill machine. The drill was only needed **while building**. But the wiring? That stays — the house cannot function without it.

Node.js works exactly the same way.

### Mapping It to Node.js

| House analogy | Node.js equivalent |
|---|---|
| Things inside the finished house | `dependencies` |
| Construction tools | `devDependencies` |
| The house being lived in | Your app running in **production** |
| Construction phase | Your **development** environment |

- **`dependencies`** → packages your app needs to **run** in production
- **`devDependencies`** → packages you only need **while developing** — they never go to production

### A Concrete Example

Let's say your app reads a secret database password from a `.env` file using `dotenv`.

When your app is **live on a server**, it still needs to read that `.env` file.
So `dotenv` must be present in production → it belongs in **`dependencies`**.

Now think about `nodemon` — it watches your files and auto-restarts the server when you save a change.

Does your **production server** need to watch for file changes and restart itself?
Absolutely not. That is purely a **developer convenience tool** → it belongs in **`devDependencies`**.


### How yarn Knows the Difference

When you install a package, you tell yarn exactly where to put it:
```
# installs into dependencies
yarn add dotenv

# installs into devDependencies
yarn add -D nodemon
```

And your `package.json` will reflect this clearly:
```
{
  "dependencies": {
    "dotenv": "^16.4.5"
  },
  "devDependencies": {
    "nodemon": "^3.1.0"
  }
}
```

### Why Does This Actually Matter?

When you deploy your app to a server (say, an AWS EC2 instance or a Docker container),
you typically run:
```
yarn install --production
```

This tells yarn: *"install only what the app needs to run — skip the dev tools."*

The result? A **leaner, faster, and more secure** production build.

> Shipping `nodemon`, `eslint`, and `prettier` to production is like
> packing your drill machine when you move into your finished house.
> You just don't need it there.


### Quick Rule of Thumb

Ask yourself one question before installing any package:

> *"Will my app crash or misbehave in production if this package is missing?"*

- **Yes** → `dependencies` → `yarn add <package>`
- **No** → `devDependencies` → `yarn add -D <package>`

---

## Installing the Packages

Now that we understand the difference between `dependencies` and `devDependencies`, let's get everything installed.


### Runtime Dependencies

These are the packages your app needs to **run in production**.
```
yarn add dotenv winston axios uuid cors
```

### Development Dependencies

These are the tools you need **only during development**.
```
yarn add -D typescript @types/node nodemon eslint prettier
```

Once both commands finish, you will see a `node_modules/` folder in your project and both `dependencies` and `devDependencies` populated inside your `package.json`.

---

> 💡 Want to know what each package does and why we chose it?
> → [dependencies-explained.md](./dependencies-explained.md)

---

## A Quick Note Before You Continue

> 📦 **Alternatives Exist for Every Package**
>
> The packages listed above are some of the most widely used and battle-tested choices in the Node.js ecosystem. However, the npm registry has alternatives for almost every one of them — and some alternatives may suit your specific use case better.
>
> If you find a package that fits your needs better, swapping it out is straightforward:
>
> ```
> # remove the existing package
> yarn remove <package-name>
>
> # add your preferred alternative
> yarn add <new-package-name>
>
> # for dev dependencies
> yarn remove <package-name>
> yarn add -D <new-package-name>
> ```
>
> For example, if you prefer `pino` over `winston` for logging:
>
> ```
> yarn remove winston
> yarn add pino
> ```
>
> The choice of package is yours — what matters is that you understand **what role it plays** in your project.


> 🗂️ **We Have Intentionally Skipped Some Packages**
>
> There are many other packages you will commonly see in Node.js projects — for example:
>
> - `express` or `fastify` — web frameworks for building HTTP servers
> - `mongoose` or `prisma` — for connecting to databases
> - `jsonwebtoken` — for JWT-based authentication
> - `zod` or `joi` — for request validation
> - `socket.io` — for real-time communication
>
> We have not included these here because **not every project needs all of them** — and the right choice often depends on the scale and purpose of what you are building.
>
> For example:
> - Building a simple app? `express` is perfectly fine.
> - Building a large, production-grade or enterprise system? `nestjs` gives you structure, modularity, and conventions that scale far better.
>
> These packages will be introduced **when we actually need them** in the course — so you understand not just *how* to install them, but *why* we are bringing them in at that specific point.


> **🧹 Clean Up Before You Ship**
> 
> Once your app is fully built, take a moment to look at your `package.json` and ask yourself — *am I actually using everything installed here?*
> 
> It is very common to install a package early in development, experiment with it, and then never actually use it in the final code. Those unused packages still get installed on your production server, increasing build size and introducing unnecessary surface area for potential vulnerabilities.
> 
> Before you deploy, audit your dependencies and remove anything you are not using:
> ```
> # remove an unused dependency / dev dependency
> yarn remove <package-name>
> ```
> 
> A leaner `package.json` means:
>
> - **faster installs** on your CI/CD pipeline and production server
> - **smaller `node_modules`** footprint
> - **fewer packages to maintain** and keep updated
> - **reduced security risk** — every package you ship is a package that could have a vulnerability
> 
> **A good rule of thumb** — if you cannot point to a file in your project that imports a package, it probably does not belong in your `package.json`.

---

## Verifying the Installation

Once both commands have finished running, let's make sure everything installed correctly.

Open your `package.json` — you should see both sections populated:
```json
{
  "dependencies": {
    "axios": "latest",
    "cors": "latest",
    "dotenv": "latest",
    "uuid": "latest",
    "winston": "latest"
  },
  "devDependencies": {
    "@types/node": "latest",
    "eslint": "latest",
    "nodemon": "latest",
    "prettier": "latest",
    "typescript": "latest"
  }
}
```

> 📌 The version numbers shown in your `package.json` will reflect whatever the latest stable version of each package is at the time you run the install command.

You should also see a `node_modules/` folder in your project root — this is where yarn physically downloads and stores all the installed packages.

> 🔍 **Don't see `node_modules`?** Run `yarn install` — this reads your `package.json` and installs everything listed in it.

---

Congratulations 🎉

That's it for this step. Your project now has all the essential packages in place and is ready for the next stage.

---

> Back Step: **[Initialize Node Project](../03-initialize-node-project/README.md)**

> Next Step: **[TypeScript Setup](../05-typescript-setup/README.md)**