# Configuring Git for GitHub

After installing Git, you need to configure it so that **your commits are properly identified** and you can **interact with GitHub repositories**.

This setup is usually done **once per machine**.

---

## 1️⃣ Set Your Git Username

Your Git username is attached to every commit you make.

Run the following command in your terminal:
```
git config --global user.name "Your Name"
```

Example:
```
git config --global user.name "Kaustubh Gawade"
```

---

## 2️⃣ Set Your Git Email

Your email should match the email used in your GitHub account.
```
git config --global user.email "your-email@example.com"
```

Example:
```
git config --global user.email "kaustubh@example.com"
```

---

## 3️⃣ Verify Your Configuration

You can check your configuration using:
```
git config --list
```
This will show your configured username, email, and other Git settings.

---

## 4️⃣ Authenticate with GitHub

When you push or pull from GitHub for the first time, GitHub will ask for authentication.

GitHub no longer allows password authentication, so you must use one of the following:

- Personal Access Token (recommended)
- SSH keys

## Creating an SSH Key for GitHub

To securely authenticate with GitHub using SSH, you need to create an SSH key on your system and add it to your GitHub account.

You can follow the official [GitHub documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)