## Git Basics for Beginners

Git is a free and open-source **version control system** that helps you track changes in your code and collaborate with others. Think of it as a powerful "save and undo" tool for your projects, keeping a detailed history of every change you make.

---

### Common Git Commands

* **1. Initialize a Repository**: `git init` - This command creates a new Git repository in your current folder, setting up the necessary files to start tracking your project's history.
* **2. Check Status**: `git status` - Use this to see the current state of your files. It tells you which files are new, modified, or staged and ready for the next commit.
* **3. Add Files to Staging**: `git add <filename>` or `git add .` (for all files) - This moves changes from your working directory to the **staging area**, preparing them for a commit.
* **4. Commit Changes**: `git commit -m "Your commit message"` - This command saves your staged changes as a new **commit**—a permanent snapshot of your project. The message describes the changes you made.
* **5. View Commit History**: `git log` - Shows a detailed history of all the commits in your repository, including the author, date, and commit message.
* **6. Connect to a Remote Repository**: `git remote add origin <repository-url>` - This command links your local repository to a remote one, like a project on GitHub. `origin` is a conventional name for the primary remote repository.
* **7. Push Changes to Remote**: `git push origin main` - This uploads your local commits to the remote repository, sharing your work with collaborators.
* **8. Rebase Branches**: `git rebase <branch-name>` - Rebasing reapplies your commits on top of another branch. This is often used to keep your branch up to date with the main branch, creating a cleaner, more linear project history than a merge.