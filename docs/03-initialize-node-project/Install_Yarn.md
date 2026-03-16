# How to Install Yarn

Yarn is a fast, reliable package manager for JavaScript projects. Before you install Yarn, you need to have Node.js and npm already installed on your system.

> [Download Node.js](https://nodejs.org/) (npm comes bundled with it)

Once Node.js and npm are ready, choose your preferred installation method:

---

## 📦 Install via npm (easiest)
Open your terminal and run:

```
npm install -g yarn
```
This installs Yarn globally so you can use it anywhere.

---

## 🖥️ Official Installer (Windows, macOS, Linux)
Head over to the [official Yarn installation page](https://classic.yarnpkg.com/en/docs/install) and download the installer for your operating system.

---

## ⚡ Using Corepack (Node.js 16.10+)
If you have a recent Node.js version, you can use Corepack, which comes built‑in:

```
corepack enable
corepack prepare yarn@stable --activate
```

## ✅ Verify Installation
Check that Yarn installed correctly:

```
yarn --version
```
You should see a version number like 1.22.22 (or newer).

🎉 That's it! You're ready to use Yarn for your projects.