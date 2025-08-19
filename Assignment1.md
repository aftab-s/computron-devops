\# Git Basics



\## Repository

A \*\*repository\*\* (often called a \*\*repo\*\*) is a folder with version control.

It stores project files along with their entire history of changes.



---



\## Pull Request

A \*\*Pull Request (PR)\*\* is a way to propose changes to a project in Git (usually on platforms like \*\*GitHub\*\*, \*\*GitLab\*\*, \*\*Bitbucket\*\*).



\- You make changes in your branch.

\- Then you open a pull request to ask the project owner/team to review and merge your changes into the main branch.

\- Others can review, comment, and approve before merging.

\- In \*\*GitLab\*\*, the term \*\*Merge Request (MR)\*\* is used instead of Pull Request.



---



\## Git Commands

Every Git command starts with \*\*`git`\*\* as the main command, followed by a \*\*subcommand\*\*.



\- `git init` : Starts or initializes a repo.

\- `git clone <url>` : Copy a remote repo to your computer.

  - \*\*Example:\*\*

    ```bash

    git clone https://github.com/KAILAS-R-PILLAI/Coffee-Gardens.git

    ```

\- `git status` : Show current state (changes, staged files, branch info).

\- `git add <file>` : Add files to staging area (prepare for commit). Like a cache.

  - \*\*Examples:\*\*

    ```bash

    git add .             # stages all modified, new, and deleted files in current directory

    git add file1.txt     # stages only file1.txt

    ```

\- `git commit -m "any message"` : Save changes with a message.

\- `git branch` : List all branches.

\- `git branch <name>` : Create a new branch.

  - \*\*Example:\*\*

    ```bash

    git branch dev   # creates a branch called "dev"

    ```

\- `git checkout -b <branch>` : Create and switch to another branch.

  - \*\*Example:\*\*

    ```bash

    git checkout -b dev   # switches to branch called "dev"

    ```

\- `git merge <branch>` : Merge a branch into the current branch.

\- `git rebase <branch>` : Reapply commits on top of another branch.

\- `git remote add <name> <url>` : Connect your local repository to a remote repository.

  - \*\*Example:\*\*

    ```bash

    git remote add origin https://github.com/KAILAS-R-PILLAI/Coffee-Gardens.git

    ```

    This adds a new remote named \*\*origin\*\* (usually the main remote) pointing to the GitHub repo.

\- `git push <remote-name> <branch-name>` : The push command is used to upload your local commits to a remote repository.

  - \*\*Example:\*\*

    ```bash

    git push origin main   # commits to the main branch

    ```

\- `git pull <remote-name> <branch-name>` : Fetch and merge changes from a remote repo to your local machine.

  - \*\*Example:\*\*

    ```bash

    git pull origin main

    ```

    To pull the changes to local machine. Whenever changes are made in remote repo make sure to pull git so that the changes are made locally also.

