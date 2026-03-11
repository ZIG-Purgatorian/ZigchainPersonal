# Syncing zigchain-examples from zigchain-examples-private

Use this when you've finished project updates in the private repo and want to publish the latest version to the public `zigchain-examples` repo.

## One-time setup (only once)

From the **private** repo, add the public repo as a remote:

```powershell
cd "c:\Users\HOME\Desktop\Documents\Git-Hub\Projects\Zigchain\zigchain-examples-private"
git remote add public https://github.com/ZIGChain/zigchain-examples.git
```

(If you get "remote public already exists", skip this step.)

## Every time you want to publish latest to public

1. **In the private repo:** make sure you're on `main` and up to date, then push to the public remote:

   ```powershell
   cd "c:\Users\HOME\Desktop\Documents\Git-Hub\Projects\Zigchain\zigchain-examples-private"
   git checkout main
   git pull origin main
   git push public main
   ```

2. **Optional:** If the public repo has a different default branch name (e.g. `master`), use:

   ```powershell
   git push public main:main
   ```

That's it. The public repo will have the same commits and history as the private one.

## Alternative: sync from the public repo (fetch from private)

If you prefer to run the sync from inside the **public** repo:

**One-time setup:**

```powershell
cd "c:\Users\HOME\Desktop\Documents\Git-Hub\Projects\Zigchain\zigchain-examples"
git remote add private "c:\Users\HOME\Desktop\Documents\Git-Hub\Projects\Zigchain\zigchain-examples-private"
```

**Every time you want to publish:**

```powershell
cd "c:\Users\HOME\Desktop\Documents\Git-Hub\Projects\Zigchain\zigchain-examples"
git fetch private
git checkout main
git reset --hard private/main
git push origin main
```

- **Push from private (first approach):** simpler, one place to run commands, good if you always work in the private repo.
- **Fetch in public (second approach):** good if you often work in the public repo or want the "source of truth" for the sync to be the public folder.

Both approaches preserve full git history.
