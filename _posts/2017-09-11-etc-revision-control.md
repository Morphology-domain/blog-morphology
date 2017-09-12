---
layout: post
title: Putting /etc under version control
tags: git localizing
---


<div class="message">
  My notes on localizing a system
</div>

This is enticing if you roll out servers, and rely on a stable of post-install
scripts. There are many solutions for this. But this relies on nothing heavier
than git.

## Single machine setup

As early as possible when I begat a new machine:

```
mkdir /root/repos # 1) see below
git init --bare !$/etc-stuff

cd /etc && git clone !$ is-version-controlled
mv is-version-controlled/.git .
git checkout -b $HOSTNAME-live
git add .gitignore lvm/ # add whatever files you think mattered at install time
git commit -a
git push origin $HOSTNAME-live
```

So, at this point, you have the beginnings of a backup of /etc in /root/repos/$HOSTNAME-etc.
You may be content with that; changes can be easily reviewed and rolled back
to your install state. New files will appear and you can add them and commit,
making comments like "Checkpoint Charlie". Make sure you do that in the branch
called $HOSTNAME-live.

## Management

You'll regularly run, in /etc as root:
```
git commit -a -m "Automatic config" && git push
```

After big system changes, maybe package updates:
```
git add . && git commit -a -m "Automatic config" && git push
```

Less regularly, you'll accept and store changes to /etc in your own clone.
This won't necessarily be as root, but some privileged user.

#### Single Machine Initialization

At first, you won't have a branch master. I think you have to clone into the
branch you know exists:

```
git clone -b $HOSTNAME-live /root/repos/etc-stuff
cd etc-stuff

git checkout -b master
# Make some change to begin the branch master
cat >> .gitignore <<-EOF
	*.cache
	*.lock

	group-
	gshadow-
	passwd-
	shadow-
	subgid-
	subuid-
EOF
git add .gitignore
git commit -a -m "Began version control" && git push
```

This will create branch master. See Note #2 below.

#### The workflow:

1. Things change in /etc
1. root commits from /etc, with either "Automatic config" or something more
   descriptive
1. You `git cherry-pick` the commits in $HOSTNAME-live, usually ignoring
   "Automatic" anything and cherry-pick the rest.

## Multiple Machine Setup

I do this on servers as well as laptops.

In the example above, we created a 'bare' repo and also had to create a rump
branch master. If you're migrating settings from one machine to another, maybe
using a remote repo, then master will already exist. Notes 1 and 2 above
indicate lines that aren't relevant. 

```
cd /etc && git clone remote-repo is-version-controlled
mv is-version-controlled/.git .
git checkout -b $HOSTNAME-live
git add .gitignore lvm/ # add whatever files you think mattered at install time
git commit -a
git push origin $HOSTNAME-live
```

Now, you have a branch master, you don't have to messily form one out of thin
air. The Management section 'Single machines' above does not apply.

#### Management

As root, in /etc, you can merge your changes curated in branch master:

```
cd /etc
git fetch --all
git merge master
...
git commit && git push
```

This is all operating in branch $HOSTNAME-live. Across multiple servers, you
can `git cherry-pick` from master.

Working changes back into master, on a machine-by-machine basis, is the same
as above.
