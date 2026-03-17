# Dependencies Explained

A breakdown of every package used in this project — what it does and why we chose it.

---

## Runtime Dependencies

These packages are installed via `yarn add` and ship to production.

---

### `dotenv`

**What it does:**
Loads environment variables from a `.env` file into `process.env` at runtime.

**Why we need it:**
Your app will have secrets — database URLs, API keys, port numbers. You never hardcode these directly in your source code. Instead you store them in a `.env` file and `dotenv` makes them available anywhere in your app via `process.env.YOUR_KEY`.
```ts
import 'dotenv/config'

console.log(process.env.PORT) // 3000
```

---

### `winston`

**What it does:**
A logging library that lets you log messages at different severity levels — `info`, `warn`, `error`, `debug` — and send them to different destinations like the console or a log file.

**Why we need it:**
`console.log` is fine for quick debugging but it has no structure, no log levels, and no way to control output in production. Winston gives you proper, production-grade logging from day one.
```ts
import winston from 'winston'

const logger = winston.createLogger({
  level: 'info',
  transports: [new winston.transports.Console()],
})

logger.info('Server started')
logger.error('Something went wrong')
```

---

### `axios`

**What it does:**
A promise-based HTTP client for making requests to external APIs or services.

**Why we need it:**
Your backend will often need to talk to other services — a payment gateway, a third-party API, a microservice. Axios makes these HTTP calls clean and readable, with built-in support for headers, interceptors, and error handling.
```ts
import axios from 'axios'

const response = await axios.get('https://api.example.com/users')
console.log(response.data)
```

---

### `uuid`

**What it does:**
Generates universally unique identifiers (UUIDs) — random, collision-resistant IDs.

**Why we need it:**
Whenever you create a new record — a user, an order, a session — you need a unique ID. UUIDs are the standard way to do this without relying on a database auto-increment, which matters especially in distributed systems.
```ts
import { v4 as uuidv4 } from 'uuid'

const userId = uuidv4()
// 'f47ac10b-58cc-4372-a567-0e02b2c3d479'
```

---

### `cors`

**What it does:**
Express middleware that sets the correct HTTP headers to allow (or restrict) cross-origin requests.

**Why we need it:**
When your frontend (running on `localhost:5173`) tries to call your backend (running on `localhost:3000`), the browser blocks it by default — this is the browser's same-origin policy. `cors` tells the browser which origins are allowed to talk to your API.
```ts
import cors from 'cors'
import express from 'express'

const app = express()
app.use(cors({ origin: 'http://localhost:5173' }))
```

---

## Development Dependencies

These packages are installed via `yarn add -D` and are **never shipped to production**.

---

### `typescript`

**What it does:**
Adds static typing to JavaScript. You write `.ts` files which are compiled down to plain `.js` before running.

**Why we need it:**
TypeScript catches bugs at compile time before your code ever runs. For a backend project, it means safer function signatures, autocomplete, and far fewer runtime surprises.

---

### `@types/node`

**What it does:**
TypeScript type definitions for Node.js built-in modules like `fs`, `path`, `http`, `process`, and `os`.

**Why we need it:**
TypeScript doesn't know what `process.env` or `__dirname` is out of the box. This package tells it — so you get proper autocomplete and type safety when using Node.js core APIs.
```bash
yarn add -D @types/node
```

---

### `nodemon`

**What it does:**
Watches your project files and automatically restarts the Node.js server whenever you save a change.

**Why we need it:**
Without it, you would manually stop and restart the server every single time you edit a file during development. Nodemon eliminates that friction entirely.
```json
// package.json
"scripts": {
  "dev": "nodemon src/index.ts"
}
```

---

### `eslint`

**What it does:**
Statically analyses your code and flags problems — unused variables, missing error handling, inconsistent patterns — before you even run it.

**Why we need it:**
In a team or even solo, ESLint enforces consistent code quality. It catches real bugs (not just style issues) and can be configured with rules specific to TypeScript and Node.js projects.

---

### `prettier`

**What it does:**
An opinionated code formatter. It automatically reformats your code to a consistent style — indentation, quotes, semicolons, line length — on every save.

**Why we need it:**
Formatting debates waste time. Prettier ends them. You write the logic, Prettier handles the style. It works alongside ESLint — ESLint catches bugs, Prettier handles formatting.

---

> ← Back to [Step 04 — Install Essential Dependencies](./Step-04.md)