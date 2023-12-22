---
layout: post
title: "The Junk Drawer monorepo, and a real-life use-case for an obscure git-merge flag"
imgpath: "/assets/img/posts/junkdrawer-monorepo"
---

How I combined a few "junk repos" into my [JunkDrawer monorepo](https://github.com/hoodlm/JunkDrawer)
with `git merge --allow-unrelated-histories`.

## Taking stock of the Junk...

I have a lot of dubiously-useful side projects and experiments that I've hoarded
over the years. Some of these I've uploaded to Github, if nothing else because I see a potentially useful reference
for my future self or for someone else out there.

So I accumulated a bunch of little junk repos, with no common theme to speak of:

* an abstract art engine (generative and artificial, but unintelligent) in Ruby
* a poorly-written math expression parser (the parser is poorly-written, not necessarily the math expressions) in Rust
* a static HTML page showing the tango color palette

Proliferation of junk repos isn't *really* a problem. Github's free tier doesn't place any limit on the number of
public repos. Things were just starting to feel cluttered, so I wanted to
consolidate these tiny projects into one place.

### The junk drawer analogy

The junk drawer is a practical organizational technique: an escape-hatch when something doesn't fit
in a neat category. When you are trying to organize
your desk or toolbox or kitchen, you can define some clear categories:
silverware, knives, cooking utensils, baking, and so on. Every item
belongs to one and only one category, and so it is placed in the corresponding drawer.

But there's an inevitable remnant: toothpicks, meat thermometer, paper
and pen, and so on. Their utility warrants presence in a kitchen drawer, but they don't fit neatly into the
Pure Kitchen Hierarchy you planned. But you can still give them a category: they belong in the Junk Drawer.

## Founding the JunkDrawer

```
mkdir $HOME/JunkDrawer && cd $HOME/JunkDrawer
git init
```

Now, I could just copy all my other projects directly...

```
cp -r $HOME/Code/* .
```

...but I'd lose the history of commits in each project if I do that.
This whole exercise is about hoarding dubiously-useful code, so I'd like to also
preserve the dubiously-useful history that goes with it!

## Combining histories

Git-merge is documented as a command to "join two or more
development histories together". In this context, a "history" is synonymous with a
branch. The most common day-to-day usage for git-merge is to join
two local branches together; for example, to fit the commits from a feature branch
into a main branch.

The git-merge documentation has ASCII diagrams to illustrate
merging a branch 'Topic' into 'Main'.

```
       A---B---C Topic
      /         \
 D---E---F---G---H Main
```

Topic and Main, above, have a common point in history - they diverged at
commit E, and have been merged back together at commit H.

What I'm trying to accomplish is a different picture.

```
E---F---G---H---I Project-1
                 \
                  A---B Junk-Drawer
                     /
    V---W---X---Y---Z Project-2
```

Project-1 and Project-2 don't have any commit in common. In git parlance,
this means they are "unrelated histories". In particular, they are also
unrelated histories with respect to Junk-Drawer, which I'm trying to merge
them into.

Let's see what happens if I try to do this merge:

```
$ pwd
/home/logan/Code/JunkDrawer

$ git remote add github/mexprander https://github.com/hoodlm/mexprander.git

$ git fetch github/mexprander
remote: Enumerating objects: 17, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 17 (delta 2), reused 17 (delta 2), pack-reused 0
Unpacking objects: 100% (17/17), 3.13 KiB | 458.00 KiB/s, done.
From https://github.com/hoodlm/mexprander
 * [new branch]      main       -> github/mexprander/main

$ git merge github/mexprander/main
fatal: refusing to merge unrelated histories
```

## Combining *unrelated* histories

There's a flag for that!

> \-\-allow-unrelated-histories

> By default, git merge command refuses to merge histories that do not share a common ancestor. This option can be used to
override this safety when merging histories of two projects that started their lives independently. As that is a very rare
occasion, no configuration variable to enable this by default exists and will not be added.

I am, in fact, experiencing the very rare occasion alluded to by the documentation! So, let's try it out:

```
$ git merge --allow-unrelated-histories -- github/mexprander/main

$ ls
Cargo.toml  README.md  src
```

So far so good! Let's try a second repo:

```
$ git remote add github/logan-rosalind-rust https://github.com/hoodlm/logan-rosalind-rust.git

$ git fetch
...
$ git merge --allow-unrelated-histories -- github/logan-rosalind-rust
logan@logan-mint-hp ~/C/FakeJunkDrawer (main)> git merge --allow-unrelated-histories -- rosalind
Auto-merging .gitignore
CONFLICT (add/add): Merge conflict in .gitignore
Auto-merging Cargo.toml
CONFLICT (add/add): Merge conflict in Cargo.toml
Auto-merging README.md
CONFLICT (add/add): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

Well, that didn't go so well. I'm trying to merge two Rust projects together. Right off the bat,
I have identical filepaths for README, .gitignore, and Cargo.toml. This blind-merge also crams
all the files in the `src` directory together, which is not what I want either.

### Preparing sub-projects for merging: directory restructuring

The core problem is that git-merge preserves the file hierarchy of both commits.
In a perfect world there would be a single-shot project-merge command that knows
to merge files into per-project subdirectories: so that they land in 'RustProject1/README'
and 'RustProject2/README'. But, it's easy enough for me to set up these subdirectories
myself before merging.

Typically I would run this in the git repo root:

```
$ PROJECT="rust/mexprander"

$ mkdir -p $PROJECT

$ mv * $PROJECT/
```

I also want to pull the .gitignore file along:

```
$ mv .gitignore $PROJECT/
```

Finally, I would do a quick test of the project at this point (for example, `cargo clean && cargo test`) to make sure that nothing
got broken or lost in the move.

Then I made a git-commit (locally, not pushed yet) with the move: [see example](https://github.com/hoodlm/JunkDrawer/commit/8f9c651afb18b46c522f7a29ecf4f8a48446bebc).

### Optional: annotate commit messages

I also wanted to do a bit of deconfliction in the git commit log.
For example, two different project histories might have a commit with the exact message
"Adding README.md". To disambiguate, I want a brief project annotation at the front of each commit message, like
"[mexprander] Adding README.md" and "[rosalind] Adding README.md".

None of my projects had a very long history (no more than 20 commits), so I annotated semi-manually with an
interactive git-rebase from the \-\-root commit. (The "committer-date-is-author-date" flag prevents
commit timestamps from getting bumped to the present.)

```
$ git rebase --committer-date-is-author-date -i --root
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

Again, I am keeping this rebased history *local* on my machine for now.

## Ready to merge

I'm ready to retry my merge. This time, I want to merge from the local
repositories, which have non-conflicting directory structures. It's
actually possible to use a local directory as a "remote" repository in git, like so:

```
$ git remote add someproject $HOME/Code/SomeProject
```

The sequence that I followed for each project was like this:

```
$ git remote add someproject ~/your/local/path/to/someproject
$ git fetch someproject --tags
$ git merge --allow-unrelated-histories -- someproject/main
$ git remote remove someproject
```

## Results - a successful merge

All of my code is now in a coherent directory structure in a single git repository. Slightly abridged tree output:

```
$ tree .
.
├── hn_offline <-- originally its own repo
│   ├── hn_offline.sh
│   ├── LICENSE.txt
│   ├── out
│   └── README.md
├── html
│   └── tango.html
├── lographiq <-- originally its own repo
│   ├── bin
│   ├── example
│   ├── lib
│   ├── LICENSE
│   └── README
├── README.md
└── rust
    ├── mexprander <-- originally its own repo
    │   ├── Cargo.toml
    │   ├── README.md
    │   └── src
    │       └── lib.rs
    └── rosalind <-- originally its own repo
        ├── Cargo.toml
        ├── LICENSE.md
        ├── README.md
        └── src
            ├── dna.rs
            └── subs.rs
```

And, after all this effort, the commit log is historically complete and shows the origin of each
project, with the right author dates on every commit.

```
$ git log --all --pretty=format:"%h %as %s" --graph
* a25d438 2023-12-17 html - tango color palette
* 6b9d9b5 2023-12-17 Repo-wide README and consolidated .gitignore
*   74f85bf 2023-12-17 Merge remote-tracking branch 'lographiq/master'
|\  
| * 8f9c651 2023-12-17 [lographiq] Move to lographiq directory for merge into JunkDrawer monorepo
| * bd947b2 2015-07-05 [lographiq] more playing around with polygon primitives
| * 619cc87 2015-07-05 [lographiq] simple invert effect
|   ...
| * 67b8e73 2015-06-12 [lographiq] adding some additional logging to examples
| * b35738a 2015-06-12 [lographiq] Initial commit - basic 'draw' engine, canvas library that can render to ppm, and two simple examples
*   dd6856b 2023-12-17 Merge remote-tracking branch 'rosalind/main'
|\  
| * 4729e71 2023-12-17 [rust-rosalind] Move to rust/rosalind directory for merge into JunkDrawer monorepo
| * 43f94be 2023-11-10 [rust-rosalind] iprb - mendel's first law probability calculator
|   ...
| * 5b0bf20 2023-10-22 [rust-rosalind] RNA
| * 14bb9c3 2023-10-22 [rust-rosalind] Initial project setup, solution to first DNA problem
*   83a14f7 2023-12-17 Merge remote-tracking branch 'mexpr/main'
|\  
| * 1e2f91f 2023-12-17 [mexprander] Move to rust/mexprander/ directory for merge into JunkDrawer monorepo
| * cf03643 2023-11-30 [mexprander] README
| * 15f5b25 2023-11-30 [mexprander] Add multiply token
| * a094f3c 2023-11-26 [mexprander] Basic expression expander for flat additive lists
* 8fe2182 2023-12-17 [hn_offline] Moving everything to hn_offline directory for merge into JunkDrawer monorepo
* 7d3223d 2023-01-29 [hn_offline] Rewrite to use wget recursive mode
* da4e444 2023-01-26 [hn_offline] Download hn.js
* ...
* fd19658 2023-01-21 [hn_offline] Clean shellcheck
* 779db2b 2023-01-21 [hn_offline] Initial implementation
```

If you look at my JunkDrawer on Github, though, the dates will look wrong. When I did my migration, I didn't know about
the \-\-committer-date-is-author-date flag on git-rebase (although I do include it in the instructions above).
So, when I annotated all the commit messages, Git also bumped the commit date to the present time. GitHub
displays Commit Time, not Author Time - so, about 8 years worth of sporadic commits appear to be
[compressed into about an hour](https://github.com/hoodlm/JunkDrawer/commits?since=2023-12-17&until=2023-12-17).

But, all in all, still a successful migration. Back to creating Junk...

