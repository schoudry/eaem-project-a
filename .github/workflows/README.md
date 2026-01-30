# GitHub Actions - Sync to Adobe Cloud Manager Submodule

## Overview

This workflow automatically syncs `eaem-project-a` to Adobe Cloud Manager as a submodule in the parent repository.

## How It Works

When code is pushed to the `main` branch:

1. **Checkout**: Gets the latest code from `eaem-project-a`
2. **Setup SSH**: Configures SSH authentication for Adobe Cloud Manager
3. **Clone Parent**: Clones the parent repository from Adobe Cloud Manager
4. **Update Submodule**: Updates the submodule reference to the latest commit
5. **Push**: Pushes the updated parent repository back to Adobe Cloud Manager

## Setup Instructions

### 1. Get Adobe Cloud Manager Git Credentials

1. Go to Adobe Cloud Manager
2. Navigate to your program settings → Repositories
3. Click on "Repository Info" to get Git credentials
4. Note down:
   - **Username**: Your Adobe Cloud Manager Git username
   - **Password**: Your Adobe Cloud Manager Git password or personal access token

**Reference**: https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/using-cloud-manager/managing-code/accessing-repos

### 2. Add Credentials to GitHub Secrets

1. Go to your `eaem-project-a` GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Add two secrets:

   **Secret 1:**
   - **Name**: `ADOBE_GIT_USERNAME`
   - **Value**: Your Adobe Cloud Manager Git username

   **Secret 2:**
   - **Name**: `ADOBE_GIT_PASSWORD`
   - **Value**: Your Adobe Cloud Manager Git password or access token

### 3. Test the Workflow

1. Push a commit to the `main` branch of `eaem-project-a`
2. Go to **Actions** tab in GitHub
3. Watch the workflow run
4. Verify in Adobe Cloud Manager that the submodule was updated

## Repository Structure

### GitHub (eaem-project-a)
```
eaem-project-a/                    ← Your source repository
├── .github/
│   └── workflows/
│       └── sync-to-adobe-submodule.yml
├── pom.xml
└── src/
```

### Adobe Cloud Manager (eaem-project-parent)
```
eaem-project-parent/               ← Parent aggregator
├── .gitmodules
├── pom.xml
└── eaem-project-a/                ← Submodule (synced automatically)
    ├── pom.xml
    └── src/
```

## Workflow Process

```
GitHub (eaem-project-a)
  │
  │ 1. Push to main
  │
  ▼
GitHub Actions
  │
  │ 2. Clone Adobe CM parent repo
  │ 3. Update submodule reference
  │ 4. Commit & push
  │
  ▼
Adobe Cloud Manager
  │
  │ 5. Parent repo updated
  │ 6. Cloud Manager builds with new submodule
  │
  ▼
AEM Deployment
```

## Important Notes

### Security
- ⚠️ **Never commit credentials** to the repository
- ✅ Always use GitHub Secrets for sensitive data
- ✅ Use access tokens with minimal permissions instead of passwords when possible
- ⚠️ Credentials are passed via environment variables and not exposed in logs

### Submodule Behavior
- The parent repository stores a **reference** to a specific commit in `eaem-project-a`
- This workflow updates that reference to the latest commit
- Adobe Cloud Manager will checkout the exact commit referenced

### Troubleshooting

**Workflow fails with "Authentication failed"**
- Verify credentials are correct in Adobe Cloud Manager
- Verify both `ADOBE_GIT_USERNAME` and `ADOBE_GIT_PASSWORD` are set in GitHub Secrets
- Ensure the password/token has write access to the repository
- Check if the password needs to be URL-encoded if it contains special characters

**Submodule not updating**
- Check that `.gitmodules` in parent repo has correct URL
- Verify submodule path is `eaem-project-a` (not `../eaem-project-a`)
- Ensure the submodule repository exists in Adobe Cloud Manager

**Build fails in Cloud Manager**
- Check that `pom.xml` in parent references the module correctly
- Verify the submodule has a valid `pom.xml`
- Review Cloud Manager build logs

## Alternative: Direct Push (Not Recommended for Submodules)

If you want to push directly without submodules, you would sync the entire content to a subfolder. However, **this defeats the purpose of Git submodules** and is not recommended when using Adobe Cloud Manager's submodule feature.

## References

- [Adobe Cloud Manager Git Submodules](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-manager/content/managing-code/git-submodules)
- [Git Submodules Documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
