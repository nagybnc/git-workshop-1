---
theme: default
class: "text-center font-mono"
background: ""
highlighter: shiki
colorSchema: "light"
lineNumbers: true
info: |
    ## #1 Git workshop
    A hands-on workshop to learn the basics of Git
drawings:
    persist: false
css: unocss
---

# #1 Git Workshop

A hands-on workshop to learn the basics of Git

<div class="abs-br m-6 text-gray-600 flex items-center">
<p class="font-mono">NAGYBNC</p>
  <a href="https://github.com/nagybnc" target="_blank" alt="GitHub"
    class="text-xl icon-btn !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---

# Requirements

-   Terminal
-   Git version > 2.0 (check with git --version)
-   GitHub.com account

<br />
<img src="/images/other/gui-vs-cli.png" class="w-96 mx-auto"/>

<!--
Why use The Command line?
There are lots of graphical tool that shows you pretty visuals. Drag and Drop commits
You really never going to fully understand what's going under the hood
-->

---

# #1 workshop

-   What is git?
-   Git Workflow
-   Data Storage
-   Git Objects
    -   Git Blobs and Trees
    -   Git Commits
-   Working Area, Staging Area, Repository
-   Staging Area Deep Dive
-   References, Commits, Branches
-   Stashing

---

# #2 Workshop

-   Git Workflow
    -   Semantic Versioning
-   Cherry pick
-   Merge
-   Rebase
    -   Squash
-   Merge Conflict
-   Alias
-   Amend
-   Revert vs Reset
-   Hooks
-   Log
-   **Your idea...**

---

# What is Git?

<div class="flex justify-between gap-2">
<p class="font-bold">Distributed Version Control System</p>
<p class="font-bold">Content-addressable storage System</p>
<p class="font-bold">System for managing your files</p>
</div>

<img src="images/other/git-branches-merge.png" class="w-3/4 mx-auto my-4"/>

<!--
  The idea behind git is that it allows you to **track changes** to your project overtime and that is useful for things like **collaborating with others**
  design to be distributed. Git is decentralized
  - each user has an independent copy of the repository, including history
  - operations works against the local repository, including commit
  - users publish their changes independently of committing them

  Content-addressable storage System because you can use the content to generate the key
  Git is a database of references
  Git is a one giant graph
  Commits are references their parent

  maybe she sends it to me in a patch over email because that's a thing gets supported
-->

---

# What is Git?

<div class="flex h-full justify-center pb-4">
  <img src="/images/other/git-introduction-meme.jpeg"/>
</div>

---

# Git internals

####

<p class="text-xs leading-4">

**add**, am, annotate, **apply**, archive, bisect, blame, branch, bundle, cat-file, checkout, checkout-index, cherry, cherry-pick, citool, clean, clone, **commit**, commit-tree, config, credential, count-objects, daemon, describe, **diff**, diff-files, diff-index, difftool, diff-tree, fast-export, fast-import, **fetch**, fetch-pack, filter-branch, for-each-ref, format-patch, fsck, gc, get-tar-commit-id, gitk, gitweb, grep, gui, hash-object, help, http-backend, index-pack, init, instaweb, log, ls-files, ls-remote, ls-tree, merge, merge-base, merge-file, merge-index, mergetool, merge-tree, mktag, mktree, mv, name-rev, notes, pack-objects, pack-redundant, pack-refs, prune, prune-packed, **pull**, **push**, read-tree, rebase, reflog, relink, remote, repack, replace, rerere, **reset**, restore, revert, rev-list, rev-parse...

</p>

<div class="flex flex-col items-center">
<img src="/images/other/porcelain-plumbing.png" class="w-96"/>
<a href="https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain" class="text-xs">Git Internals - Plumbing and Porcelain</a>
</div>
<!--
 Front is the commands. Git branch, Git Add, Git commit
 Back is the how Git works under the hood

Plumbing commands: users don't usually need to use directly
Porcelain commands: more user friendly high-level commands
-->

---

# Git Workflow

<div grid="~ cols-2 gap-4">
  <div class="flex items-center">

<ul>
<li class="mb-4">
  <strong>REMOTE REPOSITORY</strong>
  <span class="block text-xs">Hosted on a server (GitHub / GitLab) that is accessible for all team members</span>
</li>

<li>
  <strong>LOCAL REPOSITORY</strong>
  <span class="block text-xs">Local copy of the upstream repo. Stored on your computer</span>
  <li>
    <strong>WORKING AREA</strong>
    <span class="block text-xs">Files that have been modified but not committed</span>
  </li>
  <li>
    <strong>STAGING (index) AREA</strong>
    <span class="block text-xs">Modified/Added files that are marked to go into the next commit</span>
  </li>
  <li>
    <strong>STASH AREA</strong>
    <span class="block text-xs">You can temporarily store a snapshot of your changes without committing them to the repository so you can work on something else, and then come back and re-apply them later on</span>
  </li>
</li>
</ul>

  </div>
  <div class="relative flex items-center">
   <img src="/images/git-workflow/background.png" class="absolute"/>
  <v-clicks>
   <img src="/images/git-workflow/fetch-pull.png" class="absolute"/>
   <img src="/images/git-workflow/add-reset-diff.png" class="absolute"/>
   <img src="/images/git-workflow/stash-apply.png" class="absolute"/>
   <img src="/images/git-workflow/commit-diff.png" class="absolute"/>
   <img src="/images/git-workflow/push.png" class="absolute"/>
   </v-clicks>
  </div>
</div>

<!--
**Working area** - The files in your working area that are also not in the staging and not handled by git. Untracked files.
**index/Staging area** - What files are going to be part of the next commit. What will change between the current and the next commit.
**Repository** - The files git knows about. Contains all of your commits

**UNTRACKED FILES** - New files that Git has not been told to track previously

Let's talk about the **git data transport commands**

1. git fetch updates your remote-tracking branches
   git pull is shorthand for git fetch followed by git merge FETCH_HEAD

2. git add and git reset and git diff
  git diff command what is comparing? What is in the working area and what is in the staging area

3. git stash and git apply

4. git commit and git diff --staged
    git diff --staged (or --cached) when you need to compare the staging area and the repo

5. git push

-->

---

# How does Git store information?

-   Git is like a key value store

    -   The **Value** = **Data**
    -   The **Key** = **Hash of the Data**

-   You can use the key to retrieve the content.
-   SHA1 is a cryptographic hash function.
-   Given a piece of data, it produces a 40-digit hexadecimal number.
-   This value should always be the same of the given input is the same.

---

# The value - Blob

<img src="/images/git-objects/value-blob.png" class="absolute right-20 top-32"/>

-   Git stores the compressed data in a blob, along with metadata in a header:
    -   The identifier **blob**
    -   The size of the content
    -   \0 delimiter
    -   Content

<br/>

-   **git-hash-object** - Compute object ID and optionally creates a blob from a file
    -   **--stdin** - Read the object from standard input instead of from a file.
-   Asking Git for the SHA1 of contents:

```shell {0|1|2}
echo 'Hello, World!' | git hash-object --stdin
8ab686eafeb1f44702738c8b0f24f2567c36da6d
```

<!--

-->

---

# Where does Git store its Data?

```shell {2}
~/playground/sample
git init
!!! Check git init sample command; cd sample; ls -h
Initialized empty Git repository in /Users/bence/playground/sample/.git/
```

<!--
It stores them in the .git directory
Git init will initialize an empty git repository
.git directory contains data about our repository
As i make changes in my Git repository in my project those changes get logged and saved as files inside of this .git directory
-->

---

# Where are blobs stored?

-   **-w** Actually write the object into the object database.

```shell {0|1-2|4|6|7-20}
echo 'Hello, World!' | git hash-object -w --stdin
8ab686eafeb1f44702738c8b0f24f2567c36da6d

rm -rf .git/hooks

tree .git
.git
├── HEAD
├── config
├── description
├── info
│   └── exclude
├── objects
│   ├── 8a
│   │   └── b686eafeb1f44702738c8b0f24f2567c36da6d
│   ├── info
│   └── pack
└── refs
    ├── heads
    └── tags
```

<!--
what is the .git directory?
HEAD
(objects) - A collection of objects
(refs) - A system for naming for those objects

Our blob is stored in objects.
The directory starts with the first 2 chars of the hash.
In practice you will never never need use this
-->

---

# Git Objects

<div grid="~ cols-2 gap-4">
<ul>
<li><strong class="text-blue-400">BLOB</strong> - Contents of a file (No metadata)</li>
<li><strong class="text-purple-700">TREE</strong> - A directory listing (of blobs and trees)</li>
<li><strong class="text-orange">COMMIT</strong> - A snapshot of the working tree</li>
</ul>

<div v-click>
```shell {all}
docs
├── pic.png
├── 1.txt
test.js
````
</div>

</div>

<img v-click src="images/git-objects/git-objects-1.png" class=""/>


<!--
The blob is missing information!

-   filenames
-   directory structures

Blobs are just contents without metadata
Git stores this information in a tree.
A binary large object (BLOB or blob) is a collection of binary data stored as a single entity. Blobs are typically images, audio or other multimedia objects,

+++++

A **tree** contains pointers (using SHA1):

-   to blobs
-   to other trees

and metadata:

-   **type** of pointer (**blob** or **tree**)
-   **filename** or directory name
-   **mode**

+++++

a **commit** points to a tree
and contains metadata
- **parent commit**
- **author and committer**
- **date**
- **message**

commit has a parent

+++ OTHER +++
blob and tree will be same at everytime -> Identical content is only stored once
Gib objects are compressed
4. objects is the annotated tag
You just think about commits. I took snapshoot, snapshoot. I want to merge these two snapshoots together
-->

---

# Git Objects

<img src="images/git-objects/git-objects-2.png" class=""/>

---

# Commit

```shell {1|3|5|7}
echo 'Hello World!' > test.js

git add test.js

git commit -m "Initial commit"

tree .git/objects
```

<div v-click>
```shell {all}
.git/objects

├── 98
│ └── 0a0d5f19a64b4b30a87d4206aade58726b60e3
├── cb
│ └── 5ff15410efdb510c2f04f96b2912212109a921
├── fd
│ └── e5161c643914e3cca4d4e16d1c93844e64a0f8
├── info
└── pack

````
</div>
<!--
  -m <msg>]
-->

---

# Commit

```shell {1|2|4|5|7-11|13-21}
git cat-file -t 980a0
blob

git cat-file -p 980a0
Hello World!

git cat-file -t cb5ff
tree

git cat-file -p cb5ff
100644 blob 980a0d5f19a64b4b30a87d4206aade58726b60e3    test.js

git cat-file -t fde51
commit

git cat-file -p fde51
tree cb5ff15410efdb510c2f04f96b2912212109a921
author Bence Nagy <email 1665247381 +0200
committer Bence Nagy <email> 1665247381 +0200

initial commit
````

<!--
easier with git show <hash>?
or
git show --pretty=raw <hash>

easier with git ls-tree <hash>?

compressed files, so you can open with git cat-file
-t => print the type
-p => print the contents

100644 -> mode (executable)
blob -> type

Why can't we change commits?
If you change any data about the commit, the commit will have a new SHA1 hash.
Even if the files don't change, the created date will.
-->

---

# The Staging area

-   The staging area is how Git knows what will change between the current commit and the next commit.
-   A "clean" staging area isn't empty!
-   Consider the baseline staging area as being an exact copy of the latest commit.
<div v-click>

```shell {all}
git ls-files -s
100644 980a0d5f19a64b4b30a87d4206aade58726b60e3 0       test.js
```

</div>

<!--
  -s
  --stage
  Show staged contents' mode bits, object name and stage number in the output.
-->

---

# Moving files in & out of the staging area

-   Add a file to the next commit:
    -   **git add "fileName"**
-   delete a file in the next commit:
    -   **git rm "fileName"**
-   rename a file in the next commit:
    -   **git mv "fileName"**

---

# Git Stash

-   Save un-commited work.
-   The stash is **safe** from destructive operations

<br />

<div v-click grid="~ cols-2 gap-4">
<div class="p-4">

-   Stash changes
    -   **git stash**
-   Name stashes for easy reference
    -   **git stash save "name of the stash"**
-   List changes
    -   **git stash list**
-   Show the contents
    -   **git stash show@{0}**

</div>
<div class="p-4">

-   Apply the last stash
    -   **git stash apply**
-   Apply a specific stash
    -   **git stash apply stash@{0}**
-   Keep untracked files
    -   **git stash --include-untracked**
-   Keep all files (even ignored ones!)
    -   **git stash --all**

</div>
</div>

---

# Git Stash

-   Save un-commited work.
-   The stash is **safe** from destructive operations

<br />

<div grid="~ cols-2 gap-2">
<div class="p-4">

-   Start a new branch from a stash
    -   **git stash branch "optional branch name"**
-   Grab a single file from a stash
    -   **git checkout "stash name" -- "filename"**

</div>
<div class="p-4">

-   Remove the last stash and applying changes
    -   **git stash pop**
-   Remove the last stash
    -   **git stash drop**
-   Remove the nth stash
    -   **git stash drop stash@{n}**
-   Remove all stashes
    -   **git stash clear**

</div>
</div>

---

# References

-   **Branches**
    -   Branch is just a pointer to a particular commit.
    -   The pointer of the current branch changes as new commits are made.
-   **Head**
    -   Head is how git knows what branch you're currently on, and what the next parent will be.
    -   It's a pointer. It usually points at the name of the current branch.
    -   It moves when:
        -   You make a commit in the currently active branch
        -   When you checkout a new branch
-   **Tag**
    -   The commit that a tag points doesn't change.
    -   It's a snapshot.

<!--
A branch is just a named reference to a commit. Like a bookmark.
Head - What is the current branch?
Being on a branch just means that when you make your next commit that branch will be updated to point to that new commit you just created.
-->

---

# Head

```shell {1|2|4|5|7|8}
cat .git/HEAD
ref: refs/heads/master

git checkout -b feature
Switched to a new branch 'feature'

cat .git/HEAD
ref: refs/heads/feature

```

<img v-click src="images/git-objects/git-head.png" class="w-96 mx-auto"/>

<!--
What is inside in the => cat .git/refs/heads/master
= hash
-->

---

# Sample REPO - A Simple Blog

-   We're working on a simple blog.
-   **index.txt** contains links to our entries, and post titles
-   the **posts/** directory contains our blog entries.

<br />

```shell {1-3|5|7|9|11-12}
mkdir blog
cd blog
mkdir posts

echo 'This is my very first blog entry' > posts/welcome.txt

echo 'welcome.txt Welcome to my blog' > index.txt

git init

git add .
git commit -m 'Initial commit'

```

<p v-click>HEAD -> master -> 65822 </p>


---

# Current repo state

```shell {all}
git checkout -b tech_posts
```

<p v-click>HEAD -> master -> tech_posts -> 65822 </p>

```shell {all}
echo 'This is the python' > posts/python.txt
git add posts/python.txt
git commit -m 'New blog posts about python'
```

<p v-click>HEAD -> tech_posts -> 10064  </p>
<p v-click>master -> 65822 </p>

---

# Tags

-   **Lightweight Tags**
    -   Just a simple pointer to a commit.
    -   When you create a tag with no arguments, it captures the value in HEAD

```shell {all}
git tag my-first-commit
```

<br />

-   **Annotated Tags**
    -   Point to a commit, but store additional information
    -   Author, message, date

```shell {1|3|5}
git tag -a v1.0 -m 'Version 1.0 of my blog'

git tag

git show v1.0
```

---

# Working with tags

-   List all the tags in repo
    -   **git tag**
-   List all tags, and what commit they're pointing to
    -   **git show-ref --tags**
-   List all the tags pointing at a commit
    -   **git tag --points-at 'commit'**
-   Looking at the tag, or tagged contents:
    -   **git show 'tag-name'**

---

# Playground

<a href="https://git-school.github.io/visualizing-git/#free-remote" target="_blank">https://git-school.github.io/visualizing-git/#free-remote</a>

<br/>

<img src="/images/other/free-remote-example.png" class="w-[600px] mx-auto"/>

---

# Questions?
