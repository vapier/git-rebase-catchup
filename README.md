# Git Rebase Catchup

Helpful when you have a branch tracking an old commit, and a lot of conflicting
changes have landed in the latest branch, but you still want to update.

A single rebase to the latest commit will require addressing all the different
changes at once which can be difficult, overwhelming, and error-prone.  Instead,
if you rebased onto each intermediate conflicting point, you'd break up the work
into smaller pieces, and be able to run tests to make sure things were still OK.

## Requirements

You'll need at least:
* [Git](https://git-scm.com/) 2.22+
* [Python](https://python.org/) 3.7+

## Install

Copy `git-rebase-catchup` into your `$PATH` (e.g. `~/.bin/` or `~/.local/bin/`),
then run `git rebase-catchup` or `git-rebase-catchup`.

## Example

Let's say you have a branch that is currently 357 commits behind.  When you try
rebasing onto the latest, it hits a lot of conflicts.  The tool will bisect down
to find the most recent commit it can cleanly rebase onto.

```sh
$ git rebase-catchup
Local branch resolved to "s-logs"
Tracking branch resolved to "origin/master"
Branch is 2 commits ahead and 357 commits behind
Trying to rebase onto latest origin/master ... failed; falling back to bisect
Rebasing onto origin/master~178 ... failed
Rebasing onto origin/master~267 ... failed
Rebasing onto origin/master~312 ... failed
Rebasing onto origin/master~334 ... failed
Rebasing onto origin/master~345 ... OK
Rebasing onto origin/master~339 ... OK
Rebasing onto origin/master~336 ... failed
Rebasing onto origin/master~337 ... OK
Rebasing onto origin/master~336 ... failed
Found first failure origin/master~336
```

Now you know the first conflicting change is `origin/master~336`.  Rebase onto
that directly and address all the problems (and run tests/etc...).  Then restart
the process.

```sh
$ git rebase origin/master~336
... address all the conflicts ...
$ git rebase --continue
$ git rebase-catchup
Local branch resolved to "s-logs"
Tracking branch resolved to "origin/master"
Branch is 2 commits ahead and 335 commits behind
Trying to rebase onto latest origin/master ... failed; falling back to bisect
Rebasing onto origin/master~167 ... OK
Rebasing onto origin/master~83 ... OK
Rebasing onto origin/master~41 ... failed
Rebasing onto origin/master~62 ... OK
Rebasing onto origin/master~51 ... OK
Rebasing onto origin/master~46 ... OK
Rebasing onto origin/master~43 ... failed
Rebasing onto origin/master~44 ... failed
Rebasing onto origin/master~45 ... OK
Rebasing onto origin/master~44 ... failed
Found first failure origin/master~44
```

Now you're only 44 commits behind.  Keep doing this until you catchup!
