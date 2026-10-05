# Upgrading from the upstream (parent) repo

This repo (`origin` → `RechieKho/RichieOpenMontage`) is a specialized fork of the
original project (`calesthio/OpenMontage`). To avoid losing track of upstream
improvements, a second remote and tracking branch are kept pointed at the
original repo:

- **`parent`** — a git remote pointing to `https://github.com/calesthio/OpenMontage.git`
- **`parent-main`** — a local branch tracking `parent/main`

**Important:** `parent` and `parent-main` are **local-only git constructs**,
set up by the agent in each local clone's `.git/config` — they are not a
GitHub-hosted remote or branch. GitHub only knows about `origin`
(`RechieKho/RichieOpenMontage`). There is no such thing as a "parent remote"
on GitHub itself; `parent` is simply this local repo's own pointer to the
upstream repo's URL, and `parent-main` is a local branch that mirrors
`parent/main` after a `git fetch`. Anyone who clones this repo fresh (or any
agent working in a new clone) will need to redo the one-time setup below,
since neither construct travels with the repo on its own.

"Upgrading" means pulling new commits from the parent repo's `main` branch and
merging them into this fork's `main`, so local customizations are preserved
while upstream fixes/features are incorporated.

## One-time setup (only if missing)

Check first:

```bash
git remote -v
git branch -a
```

If the `parent` remote does not exist:

```bash
git remote add parent https://github.com/calesthio/OpenMontage.git
```

If the `parent-main` branch does not exist:

```bash
git fetch parent
git branch parent-main parent/main
```

## Upgrade procedure

Whenever asked to "upgrade" (pull in upstream changes), do the following:

1. **Verify remotes/branches exist** — run the one-time setup above if either
   `parent` or `parent-main` is missing.

2. **Fetch latest upstream commits:**

   ```bash
   git fetch parent
   ```

3. **Update the tracking branch:**

   ```bash
   git checkout parent-main
   git merge --ff-only parent/main
   ```

   (This should always fast-forward since `parent-main` only ever tracks
   `parent/main`.)

4. **Merge into `main`:**

   ```bash
   git checkout main
   git merge parent-main
   ```

5. **Resolve any conflicts.** Since `main` has been repurposed/specialized,
   conflicts are expected in files that were customized. Resolve conflicts in
   favor of preserving the specialized behavior unless the upstream change is
   clearly a bugfix or improvement that should take precedence. When in
   doubt, surface the conflict to the user rather than guessing.

6. **Verify** the repo still works as expected (e.g., run relevant tests or
   do a smoke check) before considering the upgrade complete.

7. **Do not push automatically.** Leave the merged state for the user to
   review and push, unless explicitly asked to push.

## Notes

- Never force-push or rewrite history on `main` as part of an upgrade.
- Never delete or reset the `parent-main` branch — it exists solely to track
  upstream and should only ever fast-forward.
- If `parent/main` has diverged in a way that makes `parent-main` unable to
  fast-forward (e.g., upstream rebased), stop and ask the user how to proceed
  rather than force-updating.
