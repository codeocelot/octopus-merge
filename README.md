# Octopus Merges

Unlike vanilla merges, octopus merges involve merging 2 or more branches onto the current branch using a single commit.  Pretty awesome right?  

Let's see how it works.  In this repo I have 3 branches, with the following branches.  add-b and add-c are each branched from master and make individual commits to add b.txt and c.txt, respectively.  

master:  
- a.txt

add-b:  
- b.txt

add-c:  
- c.txt

This can occur, for example, while merging individual teammembers work.  .

Let's go ahead to merge `add-b` & `add-c` with a single commit.

`git merge add-b add-c`

Throughout this little demonstration, we can inspect our git history using `git log --oneline --decorate --graph --all` (omyzsh `glog --all`). 

    *   a2efb02 (HEAD -> master) Merge branches 'add-b' and 'add-c'
    |\
    | * 63a1dd3 (origin/add-c, add-c) added c
    * | bd28eba (origin/add-b, add-b) added b
    |/
    * 0155554 (origin/master) initial commit

Huh? Didn't we have two branches from a shared master parent branch?  Why does it look like add-b was committed on master and add-c later merged ontop of it?  A: Vanilla git merges are prone to *fast forward* commits while merging.  If you pop off the two commits we just made with `git reset --hard HEAD~2` to get back to the initial commit on master and visualize the whole git tree using our git log command above you'll see the following tree:

    * 63a1dd3 (origin/add-c, add-c) added c
    | * bd28eba (origin/add-b, add-b) added b
    |/
    * 0155554 (HEAD -> master, origin/master) initial commit

Now it's clear what happened.  In this case, add-b and add-c are both branched off from master.  git advanced HEAD to add-b before doing a one-sided merge of add-c.  In general, this can happen when merging child commits onto a parent ref.  

As a side note, git merge seems to prefer fast forwarding the first octopus branch.  For exampe `git merge add-c add-b` would instead fast forward master onto add-c.  

This isn't exactly what we wanted since it ignored the distinction that we did not, infact, mean to commit add-b directly on master.  Strictly speaking however, it did merge both branches with a single commit.  

Now, let's see if we can merge these two branches so that master's history will appear as if we merged two separate lineages onto master with a single commit.  We do this by telling git not to fast forward our master branch using the `--no-ff` flag.  

After running `git merge add-b add-c --no-ff` we can visualize our graph and see that git treated each branch as a separate lineage.

    *-.   d01aeba (HEAD -> master) Merge branches 'add-b' and 'add-c'
    |\ \
    | | * 63a1dd3 (origin/add-c, add-c) added c
    | |/
    |/|
    | * bd28eba (origin/add-b, add-b) added b
    |/
    * 0155554 (origin/master) initial commit


**Why does git have this issue?**

Fundamentally, git is pretty dumb about what it thinks a branch is.  Branches don't contain any meta data or information about their origin.  When merging a parent with commits from it's child, git will try fast forwarding HEAD to save you from having to necessarily commit.  Fast forwarding as a concept is extremely useful since it can directly cut down on necessary commits when merging lineages who diverged from the same history, such as in a team environment.

As always when using git, it pays to know the flags.

Git out there and build something awesome!

jrg.
