# Lesson 1: Git Objects & the Data Model

**Lesson 1** · ~25 min · Hands-on

> [!NOTE] Mission tie-in
> Git isn't just a tool — it's a content-addressable filesystem. Understanding how Git stores data internally makes everything else (branches, merges, rebases) click into place.

## What You'll Learn

- Git's object model: blobs, trees, commits
- What a SHA hash actually represents
- How Git stores your data
- Explore the `.git` directory

## The Three Objects

Git stores everything in `.git/objects/` as one of three object types:

| Object | What it stores | Example |
|--------|---------------|---------|
| **Blob** | File contents | `hello world` |
| **Tree** | Directory listing (filenames + modes + hashes) | `file.txt → abc123` |
| **Commit** | Snapshot: tree + parent + author + message | `commit 1a2b3c...` |

## Explore .git

```bash
mkdir ~/git-explore && cd ~/git-explore
git init

# See what git created
ls -la .git/
ls .git/objects/   # Empty — no objects yet
ls .git/refs/      # Empty — no refs yet
```

## Create Your First Objects

```bash
# Create a blob
echo "hello git" | git hash-object --stdin -w

# Output: 8ab686eafeb1f44702738c8b0f24f2567a36ceaf
# This is the SHA hash of "hello git\n"

# Check it
ls .git/objects/8a/
```

The content `"hello git\n"` was compressed and stored as a file named by its SHA. The first two characters (`8a`) become the directory, the rest is the filename.

```bash
# Read it back
git cat-file -p 8ab686eafeb1f44702738c8b0f24f2567a36ceaf
git cat-file -t 8ab686eafeb1f44702738c8b0f24f2567a36ceaf   # Shows "blob"
```

## Staging and Committing

```bash
# Make a real commit
echo "hello world" > readme.md
git add readme.md

# What happened?
ls .git/objects/   # Now there's a blob object

# Commit it
git commit -m "Initial commit"

# See the commit
git log --oneline
git cat-file -p HEAD    # Shows tree, author, message
git cat-file -p HEAD^{tree}  # Shows the tree object
```

The commit contains:
```
tree 123abc            # Snapshot of the entire project
parent (none for first commit)
author You <you> date
committer You <you> date

Initial commit
```

## Branches Are Just Pointers

```bash
# HEAD is a file
cat .git/HEAD              # ref: refs/heads/master

# The branch is a file too
cat .git/refs/heads/master  # The commit SHA

# Creating a branch = writing a file
git branch feature
cat .git/refs/heads/feature  # Same SHA as master
```

> [!TIP] A branch is literally just a file containing 40 characters (a SHA). That's it. Creating a branch takes microseconds because it's just writing 40 bytes.

## Hands-On

```bash
# 1. Create a repo
mkdir -p ~/git-lab && cd ~/git-lab
git init

# 2. Make some commits
echo "file1" > f1.txt && git add f1.txt && git commit -m "Add f1"
echo "file2" > f2.txt && git add f2.txt && git commit -m "Add f2"

# 3. Explore objects
find .git/objects -type f
git cat-file -p HEAD
git cat-file -p HEAD^{tree}

# 4. See the branch pointer
cat .git/refs/heads/master

# 5. Cleanup
cd ~ && rm -rf ~/git-lab
```

## Self-Quiz

<details>
<summary><strong>1.</strong> What are the three types of Git objects?</summary>

Blobs (file contents), Trees (directory listings), and Commits (snapshots with metadata).
</details>

<details>
<summary><strong>2.</strong> What is a branch in Git's internal model?</summary>

A file in `.git/refs/heads/` containing a single commit SHA. Nothing more.
</details>

<details>
<summary><strong>3.</strong> Why does Git use SHA hashes for everything?</summary>

Content-addressable storage: the hash is determined by the content. If two files have the same hash, they have identical content. This gives integrity checking and deduplication for free.
</details>

## Next Steps

- Try `git hash-object` with different strings to see how hashes work
- Create a repo and inspect `.git/objects` after each action
- Move to Lesson 2: Branching & Merging Internals (coming next)

> [!NOTE] Ask followup questions!
> Confused about trees vs blobs? Want to see what happens during a merge at the object level? Ask your teacher.

---

*Reference: [Pro Git — Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)*
