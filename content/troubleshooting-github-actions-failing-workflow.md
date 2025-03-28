---
title: Troubleshooting GitHub Actions Failing Workflow
draft: false
tags:
  - git
---

![banner](https://images.unsplash.com/photo-1591608516485-a1a53df39498?w=500&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8Nnx8Z2l0aHVifGVufDB8fDB8fHww)

If your GitHub Actions workflow fails to trigger or execute properly, follow these steps to diagnose and fix the issue.

## 1️⃣ Check If the Tag Exists on Remote

```sh
git ls-remote --tags origin
```

If the tag already exists and you need to update it:

```sh
git tag -d v1.0.1  # Delete local tag
git push origin --delete v1.0.1  # Delete remote tag
git tag v1.0.1  # Recreate the tag on latest commit
git push origin v1.0.1  # Push it again
```

---

## 2️⃣ Manually Trigger the Workflow

1. **Go to GitHub → Your Repo → Actions**
2. Find the workflow for releases
3. Click **"Run workflow"** (if available)

---

## 3️⃣ Verify GitHub Actions Settings

### ➤ Ensure Actions Are Enabled

1. **Go to:** `Settings` → `Actions`
2. Under `General`, ensure that **"Allow all actions and reusable workflows"** is selected.
3. If restrictions apply, ensure the repository or organization allows workflow execution.

### ➤ Check Repository Permissions

1. **Go to:** `Settings` → `Actions` → `General`
2. Under `Workflow permissions`, ensure **"Read and write permissions"** is enabled.

---

## 4️⃣ Generate a Personal Access Token (PAT) for Releases

If `GITHUB_TOKEN` has permission issues (403 errors), create a **Personal Access Token (PAT)**:

### ➤ Create PAT

1. **Go to:** `GitHub → Settings → Developer Settings → Personal Access Tokens`
2. Click **"Generate new token (classic)"**
3. Select **Scopes:**
   - `repo` (for repository access)
   - `workflow` (for actions access)
4. Generate the token and **copy it** (you won’t see it again!)

### ➤ Use PAT in GitHub Actions

1. **Go to:** `GitHub → Your Repo → Settings → Secrets and Variables → Actions`
2. Click **"New repository secret"**
3. Name it **`PAT_TOKEN`** and paste the generated PAT.
4. Update `release.yml` to use it:

```yaml
      - name: Create GitHub Release
        uses: softprops/action-gh-release@v1
        with:
          files: dist/*
          tag_name: ${{ github.ref_name }}
          body: "Automated release for version ${{ github.ref_name }}"
        env:
          GITHUB_TOKEN: ${{ secrets.PAT_TOKEN }}
```

Now, pushing a tag should trigger the workflow successfully!
