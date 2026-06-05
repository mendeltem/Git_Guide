# Git Guide & Cheatsheet

A practical guide to Git – based on the
*Software Carpentry – Version Control with Git* course.

---

## 1. Setup (one-time)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@mail.com"
git config --global core.editor "vim"
gh auth login                     # log in to GitHub
```

## 2. Create a Repository

```bash
mkdir recipes && cd recipes       # create folder + enter it
git init                          # turn it into a Git repo
git status                        # check the state
```

Create the GitHub repo and push it (one command):

```bash
gh repo create recipes --public --source=. --remote=origin --push
```

## 3. Add & Commit

```bash
vim guacamole.md                  # create/edit a file
git add guacamole.md              # stage it (or: git add .)
git commit -m "added recipe"      # save the snapshot
git push                          # upload to GitHub
```

## 4. Viewing History (Historical Changes)

```bash
git log                           # all commits
git log --oneline                 # compact, one line per commit
git diff                          # unstaged changes
git diff --staged                 # staged changes
git ls-files                      # all tracked files
```

In the log, `HEAD -> main` marks your local position and
`origin/main` marks the state on GitHub.

## 5. Restore (Undo Changes)

```bash
git restore file.md               # discard changes → back to last commit
git restore --staged file.md      # undo a git add
git restore --source=HASH file.md # restore file to an older commit
git revert HASH                   # reverse a commit (creates a new commit)
```

> As long as you haven't **committed**, `restore` is your undo button.
> After a push, prefer `revert` over `reset --hard` – it doesn't
> rewrite history.

## 6. Daily Workflow

```bash
vim file.md                       # change something
git status                        # what's different?
git add .                         # stage
git commit -m "description"       # commit
git push                          # upload
```

## 7. Ignoring Files

Create a `.gitignore` with patterns:

```
*.tmp
__pycache__/
.venv/
```

---

*Materials based on the Carpentries course (CC-BY 4.0).*
