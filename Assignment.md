# Git & GitHub Notes

## What is Git?
- **Git** is a version control system.

## What is GitHub?
- **GitHub** is a platform to host repositories online.

## Repository
- A **repo** is considered as a folder that stores project files and history.

---

## Common Git Commands

### Clone a Branch
```bash
git clone -b branchname <repo-url>
```
Clones a specific branch from a repository.

---

### Ignore Files
```bash
.gitignore
```
Used to specify files/folders to ignore from version control.

---

### Add Files
```bash
git add .
```
Stages all changes (stores them in the local cache).

---

### Commit Changes
```bash
git commit -m "enter message"
```
Saves staged changes with a message.

---

### Push to Remote
```bash
git push -u origin main
```
Pushes changes to the `main` branch on GitHub.

---

### Rename Branch
```bash
git branch -M branch-name
```
Renames the current branch to a new branch name.

---

### Switch to Branch
```bash
git checkout -b main
```
Switches to the `main` branch (or creates it if it does not exist).

---

### Check Current Branch
```bash
git branch
```
Lists all branches and shows the one you're currently on.

---

### Pull Updates
```bash
git pull origin main
```
Fetches and merges updates from the `main` branch on GitHub.

---
