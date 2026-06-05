# Git Guide & Cheatsheet

A practical guide to Git – based on the
*Software Carpentry – Version Control with Git* course.

---

## 1. Setup (one-time)

Before anything else, check that the tools are installed:

```bash
git --version
gh --version                      # GitHub CLI
```

Tell Git who you are. This info gets attached to every commit you make,
so do it once per machine:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@mail.com"
git config --global core.editor "vim"   # editor for commit messages
git config --list                 # show all current settings
```

`--global` means it applies to all your repos. Without it, the setting
would only count for the current repo.

### Logging in to GitHub

```bash
gh auth login
```

This opens an interactive dialog. Choose:
1. **GitHub.com**
2. **HTTPS** (the protocol for Git operations)
3. Authenticate Git with your credentials → **Yes**
4. **Login with a web browser** (easiest) – it shows a one-time code,
   press Enter, the browser opens, paste the code → done.

You only do this once; `gh` remembers you afterwards. Success shows
`Logged in as yourname`.

### Alternative: log in with a token

Instead of the browser, you can paste a **Personal Access Token**. Create
one at https://github.com/settings/tokens → **Generate new token (classic)**:

1. Give it a name and an expiration date
2. Tick these scopes: **`repo`**, **`read:org`**, **`workflow`**
3. **Generate token** and copy it (shown only once!)

Then during `gh auth login`, choose **Paste an authentication token** and
paste it in. Note: the token stays invisible while pasting in PowerShell –
that's normal, just press Enter.

### The vim editor (quick survival kit)

When you run `vim file.md`:
- press `i` to start typing (insert mode)
- press `Esc` to stop typing
- type `:wq` to **save and quit**, or `:q!` to **quit without saving**

### A note on the CRLF warning

On Windows you'll often see:

> warning: in the working copy of '...', LF will be replaced by CRLF...

This is **just a warning, not an error**. It's about line endings
(Windows vs. Unix). Everything still commits correctly – you can ignore it.

---

## 2. Create a Repository

```bash
mkdir recipes && cd recipes       # create folder + enter it
git init                          # turn it into a Git repo
git status                        # check the state
```

`git init` creates a hidden `.git` folder where Git stores the entire
history. See it with `ls -a`. If you delete `.git`, you lose the history.

> **Common mistake:** never run `git init` inside a folder that's already
> a repo. One `.git` per project is enough – subfolders are tracked
> automatically. Nested repos only cause confusion.

Create the GitHub repo and push it – all in one command:

```bash
gh repo create recipes --public --source=. --remote=origin --push
```

- `--public` → public repo (use `--private` for private)
- `--source=.` → use the current folder as the source
- `--remote=origin` → connect it under the name `origin`
- `--push` → push the existing commits right away

If the name already exists on your account, you'll get
`Name already exists` – just pick a different name.

### Alternative: without GitHub CLI (works on any OS)

If `gh` isn't installed or doesn't work (Mac, Linux, Windows), create the
repo on github.com in the browser first, then connect manually:

```bash
git remote add origin https://github.com/USER/recipes.git
git branch -M main
git push -u origin main           # -u remembers the connection
```

This is the method that **always works**, no `gh` needed.

To install `gh` on a Mac instead: `brew install gh`, then `gh auth login`.
Prefer clicking over the terminal? Use **GitHub Desktop**
(desktop.github.com) – a graphical app to create, commit, and push.

### Managing remotes (switching repo)

```bash
git remote -v                     # show connected remotes
git remote add origin URL         # connect a remote named "origin"
git remote remove origin          # disconnect it
git remote set-url origin NEW_URL # point "origin" to a different repo
```

Use `set-url` when you want to push an existing local repo to a
*different* GitHub repository than before.

---

## 3. Add & Commit

The core cycle. Files move through three stages:
**working directory → staging area → repository**

```bash
vim guacamole.md                  # 1. create/edit a file
git status                        # 2. shows it as "untracked" or "modified"
git add guacamole.md              # 3. stage it (or: git add . for everything)
git commit -m "added recipe"      # 4. save the snapshot
git push                          # 5. upload to GitHub
```

- **`git add`** = "I want this change in the next snapshot" (staging)
- **`git commit`** = "make the snapshot now" (with a message describing it)
- **`git push`** = "send my snapshots to GitHub"

Write one commit per logical change – that keeps history easy to read
and lets you undo single things later.

---

## 4. Viewing History (Historical Changes)

```bash
git status                        # what changed vs. the last commit
git log                           # full commit history
git log --oneline                 # compact, one line per commit
git ls-files                      # all files Git is tracking
```

`git status` only shows what is *different* from the last commit. If it
says "nothing to commit, working tree clean", every file matches Git
exactly – so they're all safely committed.

In `git log --oneline` the markers tell you where things stand:

```
0bf0d89 (HEAD -> main, origin/main) added groceries   ← your position + GitHub
0968ab8 restored original recipe
50425fa added salt
e97d609 added new guacamole recipe                     ← the first commit
```

- **`HEAD -> main`** = your current local position
- **`origin/main`** = the state on GitHub
- When both sit on the same line → local and GitHub are in sync.

### Referring to older commits

Two ways to point at a past commit – by position or by hash:

```bash
HEAD                              # the latest commit
HEAD~1                            # one commit before that
HEAD~2                            # two commits before
e97d609                          # ...or use the commit hash directly
```

`HEAD~2` and its matching hash refer to the exact same commit – just two
ways to write it.

### Seeing what changed

```bash
git diff                          # unstaged changes (working dir vs. staged)
git diff --staged                 # staged changes (vs. last commit)
git diff HEAD                     # working files vs. last commit
git diff HEAD~2                   # working files vs. 2 commits ago
git diff e97d609                  # working files vs. a specific commit
git show HEAD~2                   # what changed *inside* that one commit
```

How to read a diff:
- `-` (red) = line removed
- `+` (green) = line added
- lines with no sign = unchanged (just context)

**Examples of how diffs add up.** The further back you compare, the more
changes accumulate:

```bash
git diff HEAD       # only your newest, uncommitted edits
git diff HEAD~1     # those + everything from the last commit
git diff HEAD~2     # those + the commit before that... and so on
```

> **`show` vs `diff`:**
> `git show COMMIT` = what changed *in* that single commit (commit vs. its
> parent). `git diff COMMIT` = everything that changed *since* that commit
> up to now. Think: *show = one snapshot*, *diff = the stretch from there
> to here*.

### Tip: getting out of the pager

Long output (`git log`, `git diff`) opens in a pager and shows a `:` at
the bottom. You're *inside* the viewer, not stuck:
- **Space** → next page
- **q** → quit back to the terminal

---

## 5. Restore (Undo Changes)

```bash
git restore file.md               # discard changes → back to last commit (HEAD)
git restore --staged file.md      # undo a git add (keeps your edits)
git restore --source=HASH file.md # restore file to an older commit
git revert HASH                   # reverse a commit (creates a new commit)
```

`--source` can be shortened to `-s`:

```bash
git restore -s e97d609 file.md    # same as --source=e97d609
```

### Going back and forth

```bash
git restore --source=e97d609 file.md   # pull in an OLD version
git restore file.md                     # back to the LATEST committed version
```

Without `--source`, `restore` always returns the file to `HEAD` (your
latest commit). `restore --source` only changes the file in your working
directory – it **never touches the commit history**. Your `git log` stays
exactly the same; you're not deleting commits, you're just bringing old
content forward.

> As long as you haven't **committed**, `restore` is your undo button.
> After a push, prefer `revert` over `reset --hard` – it doesn't rewrite
> history and won't cause trouble on the next push.

To keep a restored version, just commit it like any other change:

```bash
git add file.md
git commit -m "restored original recipe"
git push
```

---

## 6. Daily Workflow

Once set up, the cycle is always the same:

```bash
vim file.md                       # 1. change something
git status                        # 2. what's different?
git diff                          #    see the details
git add .                         # 3. stage
git commit -m "description"       # 4. commit
git push                          # 5. upload
```

---

## 7. Renaming & Deleting Files

```bash
git mv oldname.md newname.md      # rename a tracked file (stages it too)
git rm file.md                    # delete a tracked file (stages the deletion)
git rm --cached file.md           # stop tracking, but keep the file on disk
```

If you rename or delete a file *outside* Git (e.g. with `mv` in PowerShell),
Git sees it as a deletion + a new file. You then stage both with:

```bash
git add -A                        # stage everything, including deletions
```

> Tip: a typo in a filename is easy to fix with `git mv`. Renaming with the
> OS works too, but then you must `git add` both the old (deleted) and the
> new name so Git records the rename.

---

## 8. Ignoring Files

Create a `.gitignore` file with patterns Git should never track:

```
*.jpg
*.tmp
__pycache__/
.venv/
pictures/
```

Key points:
- **Commit the `.gitignore` itself** – so everyone cloning the repo gets the
  same rules. The file is tracked; the things *listed inside* it are ignored.
- `.gitignore` only affects **untracked** files. If a file was already
  committed, the rule won't remove it – use `git rm --cached file` to stop
  tracking it.
- Watch the filename: it must be exactly `.gitignore` (a common typo is
  `.gitingore`, which Git won't recognize as an ignore file).

Quick test: create a `test.jpg`, run `git status` – it should **not** show
up, proving the rule works.

---

*Materials based on the Carpentries course (CC-BY 4.0).*
