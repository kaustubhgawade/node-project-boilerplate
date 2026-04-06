# Step 01 — Creating a GitHub Repository

**Course Progress:** `01 / 08`

📅 **Published:** 15 March 2026  
🔄 **Last Updated:** 15 March 2026  
✍️ **Maintained By:** Kaustubh Gawade  

---

Before we write a single line of code, we need a **home for our project**.

That home will be a **GitHub repository**.

You can think of a repository as a **project folder on the internet** where:
- your code lives
- your project history is stored
- you can safely track changes over time
- other developers can collaborate with you

If **Git** is the **version control engine**, then **GitHub** is the **garage where your projects are stored, organized, and shared**.

---

## What You Will Learn in This Step

In this step we will learn how to:
- create a new GitHub repository
- choose a good repository name
- write a clear project description
- understand public vs private repositories
- add important files like `README`, `.gitignore`, and a `LICENSE`

By the end of this step, you will have a **clean project container ready for development**.

Now let's create our repository.

---

## 1. Go to GitHub

Go to [github.com](https://github.com) and make sure you're logged into your account.

To create a new repository, you have two options:

- In the **top-right corner**, click the **"+" icon** and select: **New repository**. It will redirect you to a new page
- Or go directly to [github.com/new](https://github.com/new)

Both will take you to the new repository form.

---

## 2. Choose an Owner and Repository Name

On the form, you'll see two fields side by side — Owner and Repository name (both required)*.

**Owner** — By default, this is set to your own GitHub account. In 99% of cases, you can leave this as-is. You'd only change it if you're creating the repo under a GitHub Organization (e.g., a company or team account) instead of your personal account.

**Repository name** — This is what you need to fill in. Keep it short, simple, and self-explanatory so anyone (including future you) can understand what the project is about at a glance.

A few good examples:  
`portfolio-website`  
`weather-app`  
`expense-tracker`  
`learn-python`  
`blog-backend`  


> 💡 Tip: Avoid vague names like project1 or test-repo — they make it harder to find and manage your repos later.  

> 💡 Tip: If your repo name has two or more words, separate them with a hyphen (-) and keep everything lowercase — for example, my-portfolio instead of myPortfolio or My_Portfolio. This is known as kebab-case and is the preferred naming convention for GitHub repositories (though not mandatory).

> 📘 **Want to learn how to choose better repository names?**  
> See: **[MORE.md → Choosing Good Repository Names](./MORE.md#choosing-good-repository-names)**

---

## 3. Add a Description

The **Description** field is optional, but it's highly recommended to fill it in.

The description shows up right below your repository name on GitHub — anyone who visits your profile or stumbles across your repo can instantly understand what the project is about without having to dig into the code or README.

What to write in the description?
Keep it to one clear sentence that answers: "What does this project do?"

A few good examples:  
`Personal portfolio showcasing my design and development work`  
`A expense tracker app built with React`  
`REST API for a blog platform built with Node.js and Express`  
`CLI tool to automate renaming files in bulk`  


So let’s make sure your description does the job it’s meant to do: **grab attention, explain the purpose, and invite people to explore more**.


Things to avoid:
- Leaving it blank
- Being too vague — e.g., "my project" or "test"
- Overly long sentences — save the details for the README

> 💡 Tip: Write your description as a plain statement, not a sentence starting with "This is a..." or "It is a..." — just get straight to the point. e.g., A weather app that fetches real-time data using OpenWeatherMap API reads better than This is a project that is a weather app...

> 📘 **Want to learn how to write better project descriptions?**  
> See: **[MORE.md → Writing Good Project Descriptions](../MORE.md#writing-good-project-descriptions)**

---

## 4. Choose Visibility — Public or Private

GitHub gives you two visibility options for your repository:

🌍 **Public** — Anyone on the internet can see your repository, but only you (or collaborators you add) can make changes to it.  
🔒 **Private** — Only you and collaborators you explicitly invite can see and access the repository.

When to keep it Private:
1. You're still learning and not comfortable sharing the code yet
2. It's a work-in-progress or experimental project
3. The project contains sensitive information (API keys, credentials, business logic)
4. It's a client or company project

When to make it Public:
1. It's a personal project you want to showcase in your portfolio
2. You're building an open-source project
3. You want others to learn from or contribute to your code
4. You're applying for jobs — recruiters and interviewers often go through your public repos


> 💡 Tip: You can always change the visibility later from the repository settings. So if you're unsure, start with Private and make it public when you're ready.


> ⚠️ Note: If you're a student or a beginner building portfolio projects, leaning towards Public is generally a good idea — your GitHub profile is essentially your coding resume.

---

## 5. Initialize with a README

GitHub will give you an option to:
**Add a README file**


A **README** is usually the **first file people see when they open your repository**.  
Think of it as the **front page of your project**.

It typically explains things like:

- what the project is
- how to install or run it
- how to use it
- how others can contribute

For now, we simply want GitHub to **create the file for us**, so make sure you **check this option**.

> ⚠️ **Note**
>
> If you skip this step, GitHub will create an **empty repository** and show you a setup page with several Git commands.
>
> This can look a bit intimidating if you're new to Git.  
> Initializing the repository with a README keeps things **simple and beginner-friendly**.

### When You Might Skip This Option

There are situations where you may want to leave this unchecked.

For example:
- you already have a project on your **local machine**
- you are planning to **push an existing repository to GitHub**

In this case, initializing with a README may create a **conflict with your local repository history**.

> 💡 **Tip**
>
> Don't worry about writing the perfect README right now.  
> When GitHub creates it, the file only contains your repository name and the description if you have provided that earlier.
>
> You can always **edit and expand it later** as the project grows.

📘 For guidance on writing a good README, see:  **[MORE.md → Writing a Good README](../MORE.md#writing-a-good-readme)**

---

## 6. Add a .gitignore
When you work on a project, your code editor, operating system, and tools like Node.js generate a bunch of extra files and folders that are needed to run the project locally — but have absolutely no business being on GitHub.

Things like:

- `node_modules/` — thousands of dependency files that can be reinstalled with a single command
- `.env` — your environment file that likely contains secret API keys and credentials
- Build output folders, logs, cache files, editor config files, etc.

This is where .gitignore comes in. It's a simple file that tells Git: "Hey, ignore these files and folders — don't track them, don't upload them." Anything listed in .gitignore stays on your machine and never makes it into your repository.

Why does this matter?
- **Security:** You never want to accidentally push your .env file with secret keys to a public repo. It happens more often than you'd think, and it's a painful mistake to fix.
- **Cleanliness:** Nobody wants to see node_modules/ with its 50,000 files cluttering your repository.
- **Size:** node_modules/ alone can be hundreds of MBs. GitHub is not the place for it — anyone who clones your project can just run npm install and get it back instantly.


How GitHub helps you here:   
When creating your repository, GitHub lets you select a .gitignore template based on your project type. Since this is a Node.js project, select Node from the dropdown.
GitHub will automatically create a .gitignore file in your repo pre-filled with all the files and folders that should be ignored in a typical Node.js project — node_modules/, npm-debug.log, .env, build directories, and more. You don't have to write it from scratch.

> 💡 Tip: Even if you're not sure what half the entries in the .gitignore file mean right now — don't worry. The Node template has you covered for the most common cases. You can always add more entries later as your project grows.

> ⚠️ Note: If you forget to add a .gitignore and accidentally push node_modules/ or .env to GitHub, don't panic — but do fix it immediately. Removing tracked files from Git history is possible but messy. It's much easier to get it right from the start.

> Curious about what the Node.js `.gitignore` template actually looks like? See an example [here](../../examples/.gitignore). Want to understand `.gitignore` in depth? [Learn more here](./MORE.md).

---

## 7. Choose a License

A license defines how others are allowed to use your code. Without one, nobody is technically allowed to copy, modify, or distribute it — even if the repo is public.

For most personal projects, learning repos, and open-source tools, MIT License is the go-to choice. It's simple and permissive — others can use, modify, and distribute your code freely, as long as they keep the original copyright notice.

### Quick License Comparison

| License | Use & Modify | Commercial Use | Forces Project to be Open Source |
|---|---|---|---|
| **MIT** | ✅ | ✅ | ❌ |
| **Apache 2.0** | ✅ | ✅ | ❌ |
| **GPL** | ✅ | ✅ | ✅ |

**MIT** → Simple and permissive. Great for most open-source projects and templates.  
**Apache 2.0** → Similar to MIT but includes additional **patent protection**.  
**GPL** → Ensures that any modified versions of the project must also remain **open source**.

> 💡 **Tip:**  If you're unsure which license to choose, **MIT is usually the safest and simplest option for most personal and open-source projects.**
>
> ⚠️ Note: If your repo is private, or contains sensitive/company code, you can skip the license entirely.

Want to understand licenses in more detail? [Learn more here](./MORE.md).

---

## 8. Create Your Repository

Once you have filled in all the options, scroll to the bottom and click: **Create repository**. GitHub will instantly create your repository and redirect you to its main page.

Congratulations 🎉

You now have your **first project container on GitHub**.

Depending on the options you selected earlier, your repository may contain some initial files.

| File | Present If... |
|-----|---------------|
| `README.md` | You selected **Add a README file** |
| `.gitignore` | You chose a **.gitignore template** |
| `LICENSE` | You selected a **license** |

---

In the next step, we will bring this repository **from GitHub to our local machine** so we can start building the project.

---

>  Next step: **[Clone Repository](../02-clone-repository/README.md)**
