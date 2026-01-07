# Git Commands Guide

A comprehensive guide to essential Git commands and their use cases.

## Table of Contents
- [Configuration](#configuration)
- [Repository Initialization](#repository-initialization)
- [Basic Commands](#basic-commands)
- [Branching](#branching)
- [Merging](#merging)
- [Remote Repository](#remote-repository)
- [Stashing](#stashing)
- [History and Logs](#history-and-logs)
- [Undoing Changes](#undoing-changes)
- [Tags](#tags)
- [Advanced Commands](#advanced-commands)

---

## Configuration

**Why:** Configure Git with identity and preferences. This information is attached to commits and helps collaborators identify who made changes.

### Set User Information
```bash
# Set name
git config --global user.name "GitHub user name"

# Set email
git config --global user.email "email@example.com"
```

### View Configuration
```bash
# List all configurations
git config --list

# Check specific configuration
git config user.name
git config user.email
```

### Configure Default Editor
```bash
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim
```

---

## Repository Initialization

**Why:** Start version control for a new project or bring an existing project under Git management.

### Initialize a New Repository
**Purpose:** Creates a new Git repository from scratch in the current directory.
```bash
# Initialize git in current directory
git init

# Initialize with specific branch name
git init -b main
```

### Clone an Existing Repository
**Purpose:** Download a complete copy of a remote repository to local machine, including all history and branches.
```bash
# Clone a repository
git clone <repository-url>

# Clone to a specific directory
git clone <repository-url> <directory-name>

# Clone specific branch
git clone -b <branch-name> <repository-url>
```

---

## Basic Commands

**Why:** These are the fundamental commands used daily to track changes, prepare commits, and manage the working directory.

### Check Status
**Purpose:** See which files have been modified, staged, or are untracked. Essential for understanding the current state before committing.
```bash
# Show the working tree status
git status

# Short status
git status -s
```

### Add Files to Staging Area
**Purpose:** Mark changes to include in the next commit. Staging allows preparing a logical set of changes before committing.
```bash
# Add specific file
git add <file-name>

# Add all changes
git add .

# Add all files with specific extension
git add *.js

# Add files interactively
git add -p
```

### Commit Changes
**Purpose:** Save staged changes to the repository history with a descriptive message. Creates a permanent snapshot that can be returned to later.
```bash
# Commit with message
git commit -m "Commit message"

# Commit and add all tracked files
git commit -am "Commit message"

# Amend last commit
git commit --amend -m "Updated commit message"

# Commit without running hooks
git commit --no-verify -m "Message"
```

### Remove Files
**Purpose:** Delete files from the repository while keeping the change tracked in Git history.
```bash
# Remove file from working directory and staging area
git rm <file-name>

# Remove file only from staging area
git rm --cached <file-name>

# Remove directory
git rm -r <directory-name>
```

### Move/Rename Files
**Purpose:** Rename or move files while preserving their history in Git. Git tracks the file as moved/renamed rather than deleted and recreated.
```bash
# Rename file
git mv <old-filename> <new-filename>
```

---

## Branching

**Why:** Branches allow developing features, fixing bugs, or experimenting in isolation without affecting the main codebase. Essential for collaborative development and maintaining a stable main branch.

### List Branches
**Purpose:** View all available branches to understand the repository structure and see which branch is currently active.
```bash
# List local branches
git branch

# List all branches (local and remote)
git branch -a

# List remote branches
git branch -r

# List branches with last commit
git branch -v
```

### Create Branch
**Purpose:** Start a new line of development for a feature or bug fix without disturbing the main branch.
```bash
# Create new branch
git branch <branch-name>

# Create and switch to new branch
git checkout -b <branch-name>

# Create branch from specific commit
git branch <branch-name> <commit-hash>
```

### Switch Branches
**Purpose:** Move between different branches to work on different features or view different versions of the code.
```bash
# Switch to existing branch
git checkout <branch-name>

# Switch to branch (modern syntax)
git switch <branch-name>

# Create and switch to new branch (modern syntax)
git switch -c <branch-name>

# Switch to previous branch
git checkout -
```

### Delete Branch
**Purpose:** Clean up branches that are no longer needed after merging or abandoning a feature.
```bash
# Delete local branch
git branch -d <branch-name>

# Force delete local branch
git branch -D <branch-name>

# Delete remote branch
git push origin --delete <branch-name>
```

### Rename Branch
**Purpose:** Change a branch name to be more descriptive or fix naming mistakes.
```bash
# Rename current branch
git branch -m <new-branch-name>

# Rename specific branch
git branch -m <old-branch-name> <new-branch-name>
```

---

## Merging

**Why:** Combine changes from different branches to integrate completed features or bug fixes into the main codebase.

### Merge Branches
**Purpose:** Integrate changes from one branch into another, typically merging feature branches into main/master.
```bash
# Merge branch into current branch
git merge <branch-name>

# Merge with no fast-forward
git merge --no-ff <branch-name>

# Abort merge in case of conflicts
git merge --abort

# Merge and squash commits
git merge --squash <branch-name>
```

### Rebase
**Purpose:** Rewrite commit history by moving a branch to start from a different point. Creates a cleaner, linear history compared to merging.
```bash
# Rebase current branch onto another
git rebase <branch-name>

# Interactive rebase
git rebase -i HEAD~3  # Last 3 commits

# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort

# Skip current commit during rebase
git rebase --skip
```

---

## Remote Repository

**Why:** Collaborate with others by syncing the local repository with remote servers (GitHub, GitLab, etc.). Share work and get updates from team members.

### Add Remote
**Purpose:** Connect the local repository to a remote server where code is shared and backed up.
```bash
# Add remote repository
git remote add origin <repository-url>

# Add another remote
git remote add upstream <repository-url>
```

### View Remotes
**Purpose:** See which remote repositories the local repo is connected to and their URLs.
```bash
# List remotes
git remote

# List remotes with URLs
git remote -v

# Show remote details
git remote show origin
```

### Fetch Changes
**Purpose:** Download new commits and branches from remote without merging them. Allows reviewing changes before integrating.
```bash
# Fetch from remote
git fetch origin

# Fetch all remotes
git fetch --all

# Fetch and prune deleted branches
git fetch -p
```

### Pull Changes
**Purpose:** Download and automatically merge remote changes into the current branch. Keeps the local copy up to date with team changes.
```bash
# Pull from remote branch
git pull origin <branch-name>

# Pull with rebase
git pull --rebase origin <branch-name>

# Pull current branch
git pull
```

### Push Changes
**Purpose:** Upload local commits to a remote repository so others can access the changes and to back up work.
```bash
# Push to remote branch
git push origin <branch-name>

# Push and set upstream
git push -u origin <branch-name>

# Push all branches
git push --all

# Push tags
git push --tags

# Force push (use with caution)
git push --force

# Force push safely
git push --force-with-lease
```

### Remove Remote
**Purpose:** Disconnect from a remote repository that's no longer needed or has changed location.
```bash
# Remove remote
git remote remove <remote-name>

# Rename remote
git remote rename <old-name> <new-name>
```

---

## Stashing

**Why:** Temporarily save work-in-progress changes without committing, allowing switching contexts (branches) cleanly and returning to work later.

### Save Changes
**Purpose:** Store uncommitted changes temporarily when a clean working directory is needed, such as when switching branches urgently.
```bash
# Stash current changes
git stash

# Stash with message
git stash save "Stash message"

# Stash including untracked files
git stash -u

# Stash including ignored files
git stash -a
```

### List Stashes
**Purpose:** View all saved stashes to see what work has been temporarily set aside.
```bash
# List all stashes
git stash list

# Show stash content
git stash show

# Show stash diff
git stash show -p
```

### Apply Stashes
**Purpose:** Restore previously stashed changes to the working directory to continue working on them.
```bash
# Apply most recent stash
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Apply and remove stash
git stash pop

# Apply specific stash and remove
git stash pop stash@{2}
```

### Delete Stashes
**Purpose:** Remove stashes that are no longer needed to keep the stash list clean.
```bash
# Drop specific stash
git stash drop stash@{2}

# Clear all stashes
git stash clear
```

---

## History and Logs

**Why:** Understand the evolution of the project, track who made what changes, find when bugs were introduced, and review the history before making decisions.

### View Commit History
**Purpose:** See the chronological list of commits to understand project evolution and find specific changes.
```bash
# Show commit history
git log

# Show compact log
git log --oneline

# Show last n commits
git log -n 5

# Show commits with diff
git log -p

# Show commits with stats
git log --stat

# Show graph
git log --graph --oneline --all

# Show commits by author
git log --author="Author Name"

# Show commits in date range
git log --since="2 weeks ago"
git log --after="2024-01-01" --before="2024-12-31"

# Show commits for specific file
git log -- <file-name>
```

### View Changes
**Purpose:** Compare different versions of files to see exactly what changed between commits, branches, or the working directory.
```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged

# Compare branches
git diff <branch1>..<branch2>

# Compare commits
git diff <commit1> <commit2>

# Show changes for specific file
git diff <file-name>
```

### Show Commit Details
**Purpose:** View the complete details of a specific commit including the changes made and metadata.
```bash
# Show commit details
git show <commit-hash>

# Show specific file from commit
git show <commit-hash>:<file-path>
```

### Blame
**Purpose:** Identify who last modified each line of a file and when. Useful for understanding code history and finding the author of specific changes.
```bash
# Show who changed each line
git blame <file-name>

# Blame specific lines
git blame -L 10,20 <file-name>
```

---

## Undoing Changes

**Why:** Mistakes happen. These commands allow undoing changes, reverting commits, or restoring previous versions without losing important history.

### Discard Changes
**Purpose:** Throw away uncommitted changes in the working directory to start fresh.
```bash
# Discard changes in working directory
git checkout -- <file-name>

# Discard all changes (modern syntax)
git restore <file-name>

# Discard all changes
git restore .

# Unstage file
git restore --staged <file-name>
```

### Reset
**Purpose:** Move the current branch pointer to a different commit, optionally changing the staging area and working directory. Use to undo commits or unstage changes.
```bash
# Unstage all files
git reset

# Reset to specific commit (keep changes)
git reset --soft <commit-hash>

# Reset to specific commit (discard staged changes)
git reset --mixed <commit-hash>

# Reset to specific commit (discard all changes)
git reset --hard <commit-hash>

# Reset to last commit
git reset --hard HEAD

# Reset to remote state
git reset --hard origin/<branch-name>
```

### Revert
**Purpose:** Undo a commit by creating a new commit that reverses its changes. Safe for shared branches because it doesn't rewrite history.
```bash
# Revert specific commit (creates new commit)
git revert <commit-hash>

# Revert multiple commits
git revert <commit1> <commit2>

# Revert without committing
git revert -n <commit-hash>
```

### Clean
**Purpose:** Remove untracked files from working directory to maintain a clean workspace.
```bash
# Show what will be removed
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd

# Remove ignored files too
git clean -fdx
```

---

## Tags

**Why:** Mark specific points in history as important, typically for releases (v1.0, v2.0). Tags are immutable references to specific commits.

### Create Tags
**Purpose:** Label significant commits, usually for version releases, to easily reference them later.
```bash
# Create lightweight tag
git tag <tag-name>

# Create annotated tag
git tag -a <tag-name> -m "Tag message"

# Tag specific commit
git tag <tag-name> <commit-hash>
```

### List Tags
**Purpose:** View all version tags in repository to see release history.
```bash
# List all tags
git tag

# List tags matching pattern
git tag -l "v1.*"

# Show tag details
git show <tag-name>
```

### Push Tags
**Purpose:** Upload tags to remote repository so others can access version markers. Tags aren't pushed automatically with commits.
```bash
# Push specific tag
git push origin <tag-name>

# Push all tags
git push --tags
```

### Delete Tags
**Purpose:** Remove incorrect or unnecessary tags from local and remote repositories.
```bash
# Delete local tag
git tag -d <tag-name>

# Delete remote tag
git push origin --delete <tag-name>
```

---

## Advanced Commands

**Why:** Handle complex scenarios like applying specific commits across branches, debugging with binary search, or managing subprojects.

### Cherry-Pick
**Purpose:** Copy a specific commit from one branch to another without merging the entire branch. Useful for applying bug fixes to multiple branches.
```bash
# Apply specific commit to current branch
git cherry-pick <commit-hash>

# Cherry-pick multiple commits
git cherry-pick <commit1> <commit2>

# Cherry-pick without committing
git cherry-pick -n <commit-hash>
```

### Reflog
**Purpose:** View the history of HEAD movements to recover lost commits or branches. A safety net for undoing mistakes.
```bash
# Show reflog
git reflog

# Recover lost commit
git reflog
git checkout <commit-hash>
```

### Submodules
**Purpose:** Include other Git repositories within repository, useful for managing dependencies or shared libraries.
```bash
# Add submodule
git submodule add <repository-url> <path>

# Initialize submodules
git submodule init

# Update submodules
git submodule update

# Clone with submodules
git clone --recursive <repository-url>
```

### Worktree
**Purpose:** Work on multiple branches simultaneously by checking out different branches in separate directories.
```bash
# Create new worktree
git worktree add <path> <branch-name>

# List worktrees
git worktree list

# Remove worktree
git worktree remove <path>
```

### Bisect (Find bugs)
**Purpose:** Use binary search to quickly find which commit introduced a bug by marking commits as good or bad.
```bash
# Start bisect
git bisect start

# Mark current commit as bad
git bisect bad

# Mark commit as good
git bisect good <commit-hash>

# Bisect will checkout middle commit - test and mark
git bisect good  # or git bisect bad

# Reset after finding bug
git bisect reset
```

### Archive
**Purpose:** Export a snapshot of repository as a zip or tar file, useful for creating releases without Git metadata.
```bash
# Create archive of repository
git archive --format=zip HEAD > archive.zip

# Archive specific branch
git archive --format=tar <branch-name> > archive.tar
```

### Grep (Search in repository)
**Purpose:** Search through tracked files in repository, much faster than regular grep for large codebases.
```bash
# Search in tracked files
git grep "search-term"

# Search with line numbers
git grep -n "search-term"

# Search in specific commit
git grep "search-term" <commit-hash>
```

---

## Useful Aliases

Add these to the `.gitconfig` file or set them using `git config --global alias.<alias-name> '<command>'`

```bash
# Status shorthand
git config --global alias.st status

# Checkout shorthand
git config --global alias.co checkout

# Branch shorthand
git config --global alias.br branch

# Commit shorthand
git config --global alias.ci commit

# Pretty log
git config --global alias.lg "log --graph --oneline --decorate --all"

# Last commit
git config --global alias.last "log -1 HEAD --stat"

# Undo last commit
git config --global alias.undo "reset HEAD~1 --mixed"

# Amend commit
git config --global alias.amend "commit --amend --no-edit"
```

---

## Common Workflows

### Feature Branch Workflow
```bash
# Create and switch to feature branch
git checkout -b feature/new-feature

# Make changes and commit
git add .
git commit -m "Add new feature"

# Push to remote
git push -u origin feature/new-feature

# Merge into main
git checkout main
git merge feature/new-feature
git push origin main

# Delete feature branch
git branch -d feature/new-feature
git push origin --delete feature/new-feature
```

### Fix Conflicts
```bash
# Pull changes
git pull origin main

# If conflicts occur, Git will mark them
# Open conflicted files and resolve

# After resolving
git add .
git commit -m "Resolve merge conflicts"
git push
```

### Update Fork
```bash
# Add upstream remote
git remote add upstream <original-repo-url>

# Fetch upstream changes
git fetch upstream

# Merge upstream changes
git checkout main
git merge upstream/main

# Push to fork
git push origin main
```

---

## Best Practices

1. **Commit Often**: Make small, logical commits
2. **Write Clear Messages**: Use descriptive commit messages
3. **Pull Before Push**: Always pull latest changes before pushing
4. **Use Branches**: Keep main branch stable, develop in feature branches
5. **Review Before Commit**: Use `git diff` and `git status` before committing
6. **Don't Commit Secrets**: Never commit passwords, API keys, or sensitive data
7. **Use .gitignore**: Ignore build artifacts, dependencies, and local configs
8. **Sync Regularly**: Keep the local repository in sync with remote

---

## Emergency Commands

### Made a mistake in last commit
```bash
# Fix commit message
git commit --amend -m "Corrected message"

# Add forgotten file to last commit
git add forgotten-file.txt
git commit --amend --no-edit
```

### Accidentally committed to wrong branch
```bash
# Note the commit hash
git log

# Switch to correct branch
git checkout correct-branch

# Cherry-pick the commit
git cherry-pick <commit-hash>

# Go back to wrong branch and remove commit
git checkout wrong-branch
git reset --hard HEAD~1
```

### Need to undo pushed commit
```bash
# Revert the commit (safe for shared branches)
git revert <commit-hash>
git push origin <branch-name>
```

### Lost work after reset
```bash
# Find lost commit in reflog
git reflog

# Restore lost commit
git checkout <commit-hash>
git branch recovery-branch
```

---

## Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)
- [Pro Git Book](https://git-scm.com/book/en/v2)

---

*Last Updated: January 2026*
