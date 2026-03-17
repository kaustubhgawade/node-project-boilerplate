# Step 02 — Clone the Repository

📅 **Published:** 15 March 2026  
🔄 **Last Updated:** 15 March 2026  
✍️ **Maintained By:** Kaustubh Gawade

---

In the previous step, we created a **GitHub repository**

Right now, that repository lives **on the internet**. In Git terminology, this is called a **remote repository**.

You can think of it like this: *GitHub is storing your project in a **folder somewhere on the internet**.*

But we don’t write code directly on GitHub.
We write code on our local machine (our laptop or desktop).

So the next step is simple: We need to bring a copy of that GitHub repository to our computer. This process is called **cloning**.

Cloning downloads the complete repository to your machine, including:

- project files
- commit history
- branches
- repository configuration

Think of cloning as **creating a local working copy of your project**, so you can develop it using your editor (like VS Code).

Once cloned, your computer and GitHub will stay **connected through Git**, allowing you to:

- pull latest updates from GitHub
- push your code changes back to GitHub
- collaborate with other developers

Because of this connection, later we can use commands like:

* `git push` → send our changes to GitHub
* `git pull` → download updates from GitHub

Don't worry about those commands yet — we’ll learn them later.

For now, we just want to **get the repository onto our computer**.

---

## What You Will Learn in This Step

In this step we will learn how to:

- clone a repository from GitHub to your local machine
- understand the repository URL
- use the `git clone` command
- open the project locally in **VS Code**
- verify that the repository is properly connected to GitHub

By the end of this step, you will have the **GitHub repository running locally on your computer**, ready to start development.

---

## 1. Downloading the repository in local system

### Method 1 - Clone via Git (Recommended)

On your repository page, click the green "Code" button. Make sure you're on the HTTPS tab and copy the link.

> ⚠️ Note: This method requires Git to be installed on your system and your Git identity configured. If you haven't done that yet, refer to these first:  
> 
> [How to install Git](./Install_Git.md)  
> [How to configure Git](./Configure_Git.md)

Once Git is ready, open your `git bash` terminal and run:
bash
```
git clone https://github.com/your-username/your-repo-name.git
```

This will create a folder with your repository name in whatever directory your terminal is currently in.
Go to your repository on GitHub.

### Method 2 — Download as ZIP
On your repository page, click the green "Code" button and select "Download ZIP".
Extract the ZIP file to a location of your choice on your system.

> 💡 Tip: This method works without Git installed, but it's just a snapshot of your code — you won't be able to push changes back to GitHub this way. It's fine for just browsing or referencing the code.

---

## Step 2 Opening the Project in VS Code

Once you have the folder on your system — whether cloned or extracted — open VS Code.

> ⚠️ Note: If you don't have VS Code installed yet; download & install the [VSCode](https://code.visualstudio.com/download) 

In VS Code, go to File → Open Folder and select your project folder. That's it — you're ready to start working on your project locally.

> 💡 Tip: If you're using the terminal, you can also open the folder directly in VS Code by navigating into the project folder and running:
```
code .
```

Navigate into the repository:
```
cd repository-name
```

Inside the folder you should see the files that GitHub created earlier:

```
LICENSE
README.md
.gitignore
```

There will also be another important folder:

```
.git
```

This folder is usually **hidden** because it contains Git’s internal data.

It stores things like:

* commit history
* branch information
* repository metadata

---

Congratulations 🎉

You now have a **local copy of your GitHub repository**.

From this point onward:

* You will **write code locally**
* Use Git to **track your changes**
* And later **push your work back to GitHub**

---

> Back Step: **[Create Github Repository](../01-create-github-repo/README.md)**
> Next Step: **[Initialize Node Project](../03-initialize-node-project/README.md)**
