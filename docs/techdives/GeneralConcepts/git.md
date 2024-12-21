# **Git**

Version control is the cornerstone of modern software development, and Git stands as the most widely used version control system in the world. Whether you're a beginner or an experienced if you're a developer understanding Git is crucial for collaborative and individual projects alike. In this article, we'll take a deep dive into Git, covering everything from its basics to its advanced features, to equip you with the knowledge to master it and with a [**cheat sheet**](#git-cheat-sheet) at the end.

## **What is Git ?**

Git is a distributed version control system that tracks changes in files, enabling multiple developers to collaborate on a project effectively. Created in 2005 by Linus Torvalds, Git was initially designed for managing the Linux kernel's development. Today, it powers everything from small personal projects to massive enterprise software systems.

!!! note "Key features"
    - **Distributed:** Each user has a complete copy of the project history, ensuring no single point of failure.
    - **Fast:** Optimized to handle large projects with efficiency.
    - **Flexible Branching and Merging:** Enables seamless collaboration and experimentation.
    - **Data Integrity:** Uses SHA-1 cryptographic hashes to ensure data accuracy.
    - **Open Source:** Freely available and widely supported.


## **Installing Git**

Getting started with Git begins with installing it on your system. Here's how you can set it up based on your operating system:

=== "Mac"
    ```bash title="Use Homebrew to install Git"
    brew install git
    ```

=== "windows"
    Download **Git for Windows** from [git-scm.com](https://git-scm.com/download/win) and follow the installer instructions.

=== "Linux"
    ```bash title="Install Git using your distribution's package manager"
    sudo apt install git  # For Debian/Ubuntu
    sudo yum install git  # For CentOS/Red Hat
    ```

## **Verify Installation**
```bash title="To confirm Git is installed correctly, run"
git --version
```

## **Initial Configuration**
```bash title="After installation, configure Git with your name, email, and preferred editor"
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.editor "code"  # Use VSCode or any editor
```


## **Getting Started with Git**

### **Creating a New Repository**
To start tracking changes in a project, initialize a repository
```bash
git init
```

### **Clone an Existing Repository**
To work on an existing project, clone its repository
```bash
git clone <repository-url>
```

### **Tracking Changes**
**Stage Changes:** Add files to the staging area
```bash
git add <file>
```

**Commit Changes:** Save changes to the repository
```bash
git commit -m "<Write a proper commit message>"
```

### **Checking Repository Status**
View the status of your working directory and staged files
```bash
git status
```

### **Viewing Commit History**
Review the project's history with
```bash
git log
git log --oneline  # Concise view
```


## **Working with Branches**

Git's branching system is one of its most powerful features. Branches allow you to work on different features or bug fixes without affecting the main codebase.

### **Creating Switching Branches**
Create a new branch
```bash
git branch <branch-name>
```
Switch to the branch
```bash
git checkout <branch-name>
git switch <branch-name>  # New alternative
```
Creating and Switching to the branch
```bash
git checkout -b <branch-name>
```

### **Merging Branches**
To integrate changes from one branch into another
```bash
git checkout main # replace main with custom branch
git merge <branch-name>
```

### **Handling Merge Conflicts**
If Git detects conflicting changes, resolve them manually by editing the affected files. Then
```bash
git add <file>
git commit
```


## **Remote Repositories**

Remote repositories allow teams to collaborate effectively.

### **Adding a Remote**
Link your local repository to a remote
```bash
git remote add origin <repository-url>
```

### **Pushing Changes**
Send your commits to the remote repository
```bash
git push origin <branch-name>
```

### **Pulling Updates**
Fetch and integrate changes from the remote repository
```bash
git pull
```

### **Removing a Remote**
If needed, you can remove a remote
```bash
git remote remove origin
```


## **Advanced Git**

### **Stashing Changes**
Temporarily save changes without committing
```bash
git stash
```
Retrieve them later with
```bash
git stash apply
```

### **Cherry-Picking**
Apply a specific commit from another branch
```bash
git cherry-pick <commit-hash>
```

### **Rebasing**
Rebase your branch onto another
```bash
git rebase <branch-name>
```

### **Amending Commits**
Fix the last commit message or contents
```bash
git commit --amend
```


## **Understanding Git Internals**

Git operates by storing snapshots of your project at each commit, not deltas (differences). The key components of Git's internal storage include:

- **Blobs:** Store file contents.
- **Trees:** Represent directory structures.
- **Commits:** Point to a tree and reference parent commits.
- **Tags:** Reference specific commits.

All data is stored in the `.git` directory.


## **Collaboration Workflows**

Teams often adopt workflows to streamline collaboration. Popular ones include:

- Uses branches like `main`, `develop`, `feature`, and `release` for structured development.
- Developers fork the repository, make changes, and submit pull requests.
- All changes are merged directly into the `main` branch, often behind feature flags.


## **Common Issues**
- **Detached HEAD:** Fix by switching to a branch:
  ```bash
  git switch <branch-name>
  ```

## **Best Practices**
- Commit often with meaningful messages.
- Avoid committing sensitive files by using `.gitignore`.

## **Git Cheat Sheet**

Below are all the essential Git commands.

!!! tip "Git Cheat Sheet"
    ```bash
    # Git Commands Cheat Sheet

    # Configuration
    git config --global user.name "Your Name"       # Set user name
    git config --global user.email "your.email@uth.com" # Set user email
    git config --global core.editor "code"         # Set default editor
    git config --list                              # View current configuration

    # Repository Management
    git init                                       # Initialize a new repository
    git clone <repository-url>                     # Clone an existing repository
    git remote add origin <url>                    # Add remote repository
    git remote -v                                  # View remote repositories
    git remote remove <name>                       # Remove a remote

    # Staging and Committing
    git add <file>                                 # Stage specific file
    git add .                                      # Stage all files
    git status                                     # Check status of repository
    git commit -m "Message"                        # Commit with message
    git commit --amend                             # Amend the last commit

    # Branching
    git branch                                     # List branches
    git branch <branch-name>                       # Create a new branch
    git checkout <branch-name>                     # Switch to a branch
    git checkout -b <branch-name>                  # Create and Switch to a branch
    git switch <branch-name>                       # Modern way to switch branches
    git branch -d <branch-name>                    # Delete a branch
    git branch -D <branch-name>                    # Force delete a branch

    # Merging
    git merge <branch-name>                        # Merge a branch into the current branch

    # Pulling and Pushing
    git pull                                       # Fetch and merge from remote repository
    git pull origin <branch-name>                  # Pull specific branch
    git push origin <branch-name>                  # Push to remote repository
    git push --all                                 # Push all branches
    git push --tags                                # Push tags to remote

    # Logs and History
    git log                                        # View commit history
    git log --oneline                              # View concise commit history
    git log --graph                                # View graphical commit history

    # Undo Changes
    git reset HEAD <file>                          # Unstage a file
    git checkout -- <file>                         # Discard changes in working directory
    git revert <commit-hash>                       # Undo a specific commit (safe)
    git reset <commit-hash>                        # Reset to a specific commit (dangerous)

    # Stashing
    git stash                                      # Stash changes
    git stash list                                 # List stashes
    git stash apply                                # Apply the last stash
    git stash drop                                 # Remove the last stash
    git stash clear                                # Clear all stashes

    # Rebasing
    git rebase <branch-name>                       # Rebase current branch onto another
    git rebase -i <commit-hash>                    # Interactive rebase

    # Tags
    git tag <tag-name>                             # Create a tag
    git tag -a <tag-name> -m "Message"             # Create an annotated tag
    git tag -d <tag-name>                          # Delete a tag locally
    git push origin <tag-name>                     # Push a specific tag
    git push --tags                                # Push all tags

    # Collaboration
    git fetch                                      # Fetch updates from remote
    git pull                                       # Fetch and merge updates
    git pull origin <branch-name>                  # Pull specific branch
    git push                                       # Push changes to remote
    git push origin <branch-name>                  # Push specific branch

    # Ignoring Files
    echo "filename" >> .gitignore                  # Add file to .gitignore
    git rm --cached <file>                         # Stop tracking a file

    # Viewing Changes
    git diff                                       # View unstaged changes
    git diff --staged                              # View staged changes
    git diff <commit-hash1> <commit-hash2>         # Compare two commits

    # Cherry-Picking
    git cherry-pick <commit-hash>                  # Apply a specific commit to the current branch

    # Aliases
    git config --global alias.co checkout          # Alias for checkout
    git config --global alias.br branch            # Alias for branch
    git config --global alias.cm commit            # Alias for commit

    ```
---

