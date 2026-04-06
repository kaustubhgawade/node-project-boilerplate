Understanding the Config
printWidth
json"printWidth": 120
This tells Prettier: "If a line exceeds this many characters, wrap it."
The default in Prettier is 80 — a value inherited from the days of small terminal screens. For modern development with wide monitors and large editor windows, 80 is too conservative. Lines wrap too aggressively and code that could comfortably sit on one line gets broken into three.
We use 120 because it strikes the right balance — long enough to keep related code together on one line, short enough that you are not scrolling horizontally.

Note: printWidth is a soft limit, not a hard one. Prettier uses it as a guide. If breaking a line would make the code less readable, Prettier may leave it longer than printWidth.


tabWidth
json"tabWidth": 2
This tells Prettier: "Use this many spaces for each level of indentation."
Two spaces is the standard across the JavaScript and TypeScript ecosystem. It keeps code compact without sacrificing readability. Four spaces is common in other languages but feels heavy in JavaScript — especially when you have deeply nested callbacks or chained promises.

useTabs
json"useTabs": false
This tells Prettier: "Use spaces for indentation, not tab characters."
Tabs render differently depending on the editor and its settings — one developer sees four spaces, another sees eight. Spaces render identically everywhere. Setting this to false ensures your code looks the same in every editor, every terminal, and every code review tool.

semi
json"semi": true
This tells Prettier: "Add semicolons at the end of statements."
JavaScript does not require semicolons — it has a feature called Automatic Semicolon Insertion (ASI) that adds them for you in most cases. But ASI has edge cases that can cause subtle, hard-to-find bugs.
Being explicit with semicolons removes any ambiguity. Your code means exactly what it says, and there are no surprises from ASI kicking in unexpectedly.

singleQuote
json"singleQuote": true
This tells Prettier: "Use single quotes for strings instead of double quotes."
This is the dominant convention in the JavaScript and TypeScript ecosystem. Single quotes are slightly less visually noisy than double quotes, and they do not conflict with HTML attributes (which use double quotes) when the two are mixed.

quoteProps
json"quoteProps": "as-needed"
This tells Prettier: "Only add quotes around object keys when they are actually required."
In JavaScript, object keys do not need quotes unless they contain special characters or reserved words. With as-needed, Prettier keeps your objects clean:
ts// as-needed — clean
const config = {
  port: 3000,
  dbHost: "localhost",
  "content-type": "application/json", // quotes required here
};

// always — unnecessary noise
const config = {
  "port": 3000,
  "dbHost": "localhost",
  "content-type": "application/json",
};

trailingComma
json"trailingComma": "all"
This tells Prettier: "Add a trailing comma after the last item in multi-line structures."
ts// with trailingComma: "all"
const options = {
  host: "localhost",
  port: 3000,
  debug: true, // ← trailing comma
};
This might look odd at first, but it is one of the most practically useful settings in the entire config. When you add a new item to the end of a list, only one line changes — the new line. Without trailing commas, two lines change — the previous last line gets a comma added, and the new line is added. This creates noisier diffs in code reviews and messier merge conflicts.
"all" applies trailing commas to function parameters and arguments as well, not just objects and arrays.

bracketSpacing
json"bracketSpacing": true
This tells Prettier: "Add spaces inside object literal curly braces."
ts// bracketSpacing: true
const user = { name: "Kaustubh", age: 25 };

// bracketSpacing: false
const user = {name: "Kaustubh", age: 25};
The spaced version is significantly easier to read. This is on by default in Prettier and there is rarely a reason to turn it off.

arrowParens
json"arrowParens": "always"
This tells Prettier: "Always include parentheses around arrow function parameters, even when there is only one."
ts// arrowParens: "always"
const double = (n) => n * 2;

// arrowParens: "avoid"
const double = n => n * 2;
The always option is more consistent. When you add a second parameter, nothing about the structure changes — the parentheses are already there. It also plays better with TypeScript, where you often need to annotate the parameter type:
tsconst double = (n: number): number => n * 2;
Without parentheses, adding a type annotation would require restructuring the function. With always, it is already in the right shape.

endOfLine
json"endOfLine": "lf"
This tells Prettier: "Use Unix-style line endings in every file."
There are two line ending styles:

LF (\n) — Unix, Mac, Linux
CRLF (\r\n) — Windows

When developers on different operating systems work on the same codebase, Git starts flagging every single line as changed — not because the code changed, but because the line endings changed. This makes diffs unreadable and creates unnecessary merge conflicts.
Setting this to lf enforces Unix line endings for everyone. This is non-negotiable for a backend project — your production server is Linux, your line endings should match.

proseWrap
json"proseWrap": "always"
This tells Prettier: "Wrap markdown prose at the printWidth limit."
Your project has markdown files — READMEs, documentation, changelogs. Without this setting, Prettier leaves long lines in markdown files untouched. With always, it wraps them at 120 characters just like your code, keeping your documentation files as clean and consistent as your source code.

embeddedLanguageFormatting
json"embeddedLanguageFormatting": "auto"
This tells Prettier: "Automatically format code that is embedded inside other files."
In a backend project, you will often have SQL queries, JSON strings, or template literals containing structured content written inside your TypeScript files. With auto, Prettier detects these embedded languages and formats them correctly — so your SQL stays readable, your JSON stays clean, and your template literals stay consistent.