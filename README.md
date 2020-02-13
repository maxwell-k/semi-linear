# Enforce a semi-linear git history using bors-ng and GitHub actions

## Explanation

Start with a pull request to merge a particular branch, say `feature` into
`master`. If you post a comment `bors merge` then:

1. [bors-ng] will create a new merge commit and force-push it to the `staging`
   branch
2. the CI system will run on the `staging` branch
3. if the CI run passes, "bors-ng" will push that commit to `master`

The idea is that the CI run should fail if merging the `feature` branch and
master does not give a semi-linear history.

For the merge commit created in step 1:

1. The first parent, `HEAD^1`, is the tip of `master`
2. The second parent, `HEAD^2`, is the tip of the `feature` branch

For an explanation of ^1 and ^2 see `git help revisions`

So to check if a branch needs to be rebased to give a semi-linear history, run:

```sh
test "$(git rev-parse master)" = "$(git merge-base master HEAD^2)"
```

This repository includes an example implementation using GitHub actions.

[bors-ng]: https://github.com/bors-ng/bors-ng

## Disadvantages

One disadvantage of a semi-linear history, [observed by the D-Bus project], is
that it can require additional CI time running on recently rebased branches.
This advantage can be mitigated by using bors-ng to only run CI on `staging` or
`trying` branches.

[observed by the d-bus project]:
  https://gitlab.freedesktop.org/dbus/dbus/issues/231

## The Future

This approach only adds a gate, each re-base must be conducted manually. A
better automated solution within [bors-ng] has [been discussed].

[been discussed]: https://forum.bors.tech/t/rebase-a-pull-request/290/3

## Background material

- Microsoft's [description] of a semi-linear merge
- GitLab's documentation for [semi-linear history]
- [GitHub community post] acknowledging feature request
- an explanation of the advantages of a semi-linear merge:
  <https://www.bitsnbites.eu/a-tidy-linear-git-history/>
- [Not rocket science]

[semi-linear history]:
  https://docs.gitlab.com/ee/user/project/merge_requests/reviewing_and_managing_merge_requests.html#semi-linear-history-merge-requests
[description]: https://devblogs.microsoft.com/devops/pull-requests-with-rebase/
[not rocket science]: https://graydon.livejournal.com/186550.html
[github community post]: https://gitlab.freedesktop.org/dbus/dbus/issues/231

---

[![Bors enabled](https://bors.tech/images/badge_small.svg)](https://app.bors.tech/repositories/23202)
[![REUSE status](https://api.reuse.software/badge/github.com/maxwell-k/semi-linear)](https://api.reuse.software/info/github.com/maxwell-k/semi-linear)
