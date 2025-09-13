## Git Basics for Beginners

Git is a free and open-source version control system that helps you track changes in your code and collaborate with others. Here are some basics to get you started:

### What is Git?
Git allows you to:
- Track changes in your files
- Revert to previous versions
- Work on different features in parallel (branches)
- Collaborate with others without overwriting each other's work

### Common Git Commands

#### 1. Initialize a Repository
```
git init
```
Creates a new Git repository in your current folder.

#### 2. Check Status
```
git status
```
Shows the status of your files (changes, staged, etc).

#### 3. Add Files to Staging
```
git add <filename>
```
or add all files:
```
git add .
```

#### 4. Commit Changes
```
git commit -m "Your commit message"
```
Saves your staged changes with a message.

#### 5. View Commit History
```
git log
```

#### 6. Connect to a Remote Repository
```
git remote add origin <repository-url>
```

#### 7. Push Changes to Remote
```
git push origin main
```


#### 9. Rebase Branches
```
git rebase <branch-name>
```
Reapplies your changes on top of another branch. This is useful for keeping your branch up to date with the latest changes from another branch (like `main`) before merging. It creates a cleaner project history compared to merging.

**Example:**
```
git checkout feature-branch
git rebase main
```
This updates `feature-branch` with the latest commits from `main`.


### More Resources
- [Official Git Documentation](https://git-scm.com/doc)
- [GitHub Learning Lab](https://lab.github.com/)
