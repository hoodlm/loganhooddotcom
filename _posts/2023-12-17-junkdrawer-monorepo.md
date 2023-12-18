---
layout: post
title: "Hoarding experimental code and misfit toys in the Junk Drawer"
imgpath: "/assets/img/posts/junkdrawer-monorepo"
---

How I moved all my Github "junk repos" into a [JunkDrawer monorepo](https://github.com/hoodlm/JunkDrawer) without
losing history.

## TL;DR

If you need to merge a lot of git repos into a monorepo:

* Create a new git repo for your monorepo ("Junk Drawer", "Code Garden", ...)
* On the same computer, check out every sub-project, and for each one:
    1. Make a commit in the sub-project that moves all the contents (including .gitignore) into an inner directory.
    1. Optionally, prepend a [subproject] tag on every historical commit message.
    1. Finally, from your new monorepo, some git-fu merge in the sub-project:

```
git remote add someproject ~/your/local/path/to/someproject
git fetch someproject --tags
git merge --allow-unrelated-histories someproject/main
git remote remove someproject
```

## Taking stock of the Junk...

Like any software developer, I have some useless side projects and experiments.
Some of these I've shamelessly uploaded to Github, "because you never know",
so I accumulated repositories for assorted experiments like:

* an abstract art engine (generative and artificial, but unintelligent)
* [Rosalind](https://rosalind.info/problems/list-view/)-flavored leetcode grinding
* a poorly-written math expression parser (the parser is poorly-written, not necessarily the math expressions)
* a static HTML page showing the tango color palette

Now, on that last bullet point - seems a bit self-important to have a github repository
for 60 lines of HTML, including its own LICENSE and README and a .gitignore, just in case?

As I've accumulated more of these one-off experiments with their own repositories, it's
started to feel messy. What do you do with stuff that doesn't fit anywhere? You shove
it in the Junk Drawer!

## Founding the JunkDrawer

```
mkdir $HOME/Code/JunkDrawer && cd $HOME/Code/JunkDrawer
git init
```

Now, I could just copy all my other projects directly...

```
cp -r $HOME/Code/GoofyJunkProject .
```

...but I'd lose the git history of each project if I do that.
This whole exercise is about hoarding dubiously-useful code, so I'd like to also
preserve the dubiously-useful history that goes with it!

## Learning some new git-fu

The git-merge command has a few normal use-cases.
It's normal to merge a feature branch into main, or to merge origin/main with other people's commits into
my local checkout that's a little stale.

Thanks to this [Stack Overflow answer](https://stackoverflow.com/a/10548919), I learned a less normal way to use git merge.
Git-merge has a flag "\-\-allow-unrelated-histories" that lets you merge one git repository, including its
history, into another completely different git repository. You could, say, merge
[the Windows Driver Frameworks](https://github.com/Microsoft/Windows-driver-frameworks) into
[rust-lang/cargo](https://github.com/rust-lang/cargo), if you really wanted to see what happened.
In this case, merging a bunch of unrelated git repositories together is exactly what I'm trying to accomplish.

The answer also specifies a **local** filepath as the "remote" repository. (Most git commands let you specify local
filepaths - you can `git clone`, `git pull`, etc. from one directory to another on the same computer.)

The full sequence for one of my projects was like this:

```
git remote add mexpr $HOME/Code/RustRaw/mexprander/
git fetch mexpr --tags
git merge --allow-unrelated-histories mexpr/main
git remote remove mexpr
```

But, before I started The Great Merge, I took a few preparatory steps in each repo:

## Preparing subprojects for merging

### Per-project subdirectories

Git-merge preserves the local file hierarchy. If the two repos have two files
like 'src/main.rs' and 'src/main.java', they'll end up in the same src directory together.
Worse, if I'm merging two Rust packages that both have 'src/main.rs' they'll cause a merge conflict.

What we'd want in a perfect world is a merge tool that "knows" to merge into
per-project subdirectories: so that they land in 'SomeRustProject/src/main.rs'
and 'SomeJavaProject/src/main.java'. This capability doesn't exist (that I know of) but
it's easy enough for us to do this ourselves before merging.

Typically I would run this in the git repo root:

```
mkdir -p $PROJECT
mv * $PROJECT/
```

Or if I want to put projects in a group; for example, to put all my Rust projects
together in a 'rust' directory:

```
mkdir -p rust/rosalind
mv * rust/rosalind
```

I also want to pull the .gitignore file along:

```
mv .gitignore rust/rosalind
```

Finally, I did a quick test of the projects at this point (for example, `cargo clean && cargo test`) to make sure that nothing
got broken or lost in the move.

Then I made a git-commit (locally, not pushed yet) with the move: [see example](https://github.com/hoodlm/JunkDrawer/commit/8f9c651afb18b46c522f7a29ecf4f8a48446bebc).

### Rewrite commit messages

I want the commit history to clearly indicate which project each commit originated in. None of my
projects had a very long history (no more than 20 commits) so I did this semi-manually with an
interactive git-rebase from the \-\-root commit. (The "committer-date-is-author-date" flag prevents
commit timestamps from getting bumped to the present.)

```
git rebase --committer-date-is-author-date -i --root
```

Specify that every commit needs to be reworded:

```
%s/pick/reword
```

Then, I modified each message to have a little tag prepended to it, for example:

```
- Add multiply token
+ [mexprander] Add multiply token
```

I didn't type "[mexprander]" over-and-over at least - I recorded the
tag in a [vim register](https://vimhelp.org/repeat.txt.html#q) so I
could repeat it for each file with two keystrokes.

### Ready to merge

At this point, the packages can be cleanly merged together. The directory
structures were non-overlapping, so the histories got neatly zipped up together.

## Post-merge

### One gitignore

I want to have a single .gitignore file for the whole monorepo.
I was hoping to be clever and automate it:

```
# fish script
for GITIGNORE in (find . -name "\.gitignore")
  set PROJECT (dirname $GITIGNORE)
  echo "# from $PROJECT/.gitignore" | tee -a .gitignore
  sed 's#^#'"$PROJECT/"'#' $GITIGNORE | tee -a .gitignore
  echo | tee -a .gitignore
  rm -i $GITIGNORE
end
```

When gave me something like:

```
# from ./hn_offline/.gitignore
./hn_offline/*swp
./hn_offline/tmp/*
./hn_offline/!tmp/.gitkeep
./hn_offline/out/*
./hn_offline/!out/.gitkeep

# from ./rust/mexprander/.gitignore
./rust/mexprander//target
./rust/mexprander//Cargo.lock
./rust/mexprander/*swp

# from ./rust/rosalind/.gitignore
./rust/rosalind//target
./rust/rosalind/*swp
...
```

Which needed some clean-up and deduplication, done by hand:

```
*.swp

rust/*/target
rust/*/Cargo.lock

hn_offline/tmp/*
!hn_offline/tmp/.gitkeep
hn_offline/out/*
!hn_offline/out/.gitkeep
...
```

# So, did I preserve history?

Not exactly. My git log shows the right commits in the right order, but with the wrong commit timestamps!

When I did my migration, I didn't know about the \-\-committer-date-is-author-date flag on git-rebase.
So, when I rewrote all the commit messages, Git also bumped the commit date to the present time. GitHub
displays Commit Time, not Author Time - so, about 8 years worth of sporadic commits are
[compressed into about an hour](https://github.com/hoodlm/JunkDrawer/commits?since=2023-12-17&until=2023-12-17).

All in all, still a successful migration. Now, back to creating more Junk...

