# Branch Protection Ruleset Installation Guide

This guide provides step-by-step instructions for implementing the branch protection rules defined in `ruleset_01.yml` using both GitHub CLI and the GitHub web interface.

## 📋 Prerequisites

- GitHub repository with admin access
- GitHub CLI installed and authenticated (optional, for CLI method)
- Repository owner or admin permissions

## 🔧 Method 1: GitHub Web Interface (Recommended)

### Step 1: Access Repository Settings

1. Navigate to your GitHub repository: `https://github.com/diaryfolio/aptars_data`
2. Click on **Settings** tab (near the top of the repository page)
3. In the left sidebar, click **Branches**

### Step 2: Add Branch Protection Rule

1. Click **Add rule** or **Add branch protection rule**
2. In the **Branch name pattern** field, enter: `main`
3. Click **Create**

### Step 3: Configure Protection Settings

Based on `ruleset_01.yml`, configure these settings:

#### ✅ Basic Settings:
- **Require a pull request before merging** → ✅ Check this
- **Require approvals** → Set to `1`
- **Dismiss stale PR approvals when new commits are pushed** → ✅ Check this
- **Require review from code owners** → ✅ Check this (since you have CODEOWNERS)

#### ✅ Additional Settings:
- **Require status checks to pass before merging** → ✅ Check this
- **Require branches to be up to date before merging** → ✅ Check this
- **Require conversation resolution before merging** → ✅ Check this
- **Require signed commits** → ✅ Check this
- **Require linear history** → ✅ Check this

#### ✅ Restrict Pushes:
- **Restrict pushes that create files matching specified glob patterns** → ✅ Check this
- Add these patterns:
  ```
  *.key
  *.pem
  *.p12
  *.pfx
  secrets/
  keys/
  .env
  config/local/
  ```
- **Restrict pushes that delete files** → ✅ Check this
- **Restrict force pushes** → ✅ Check this

### Step 4: Configure Status Checks

In the **Status checks** section:
- **Require branches to be up to date before merging** → ✅ Check this
- **Status checks that are required** → Add: `Branch Protection Checks` (from your workflow)

### Step 5: Save the Rule

1. Scroll to the bottom
2. Click **Create** or **Save changes**

## 🖥️ Method 2: GitHub CLI (Advanced)

### Step 1: Verify GitHub CLI Installation

```bash
# Check if GitHub CLI is installed
gh --version

# Authenticate if not already done
gh auth login
```

### Step 2: Check Current Rulesets

```bash
# List existing rulesets
gh ruleset list

# View specific ruleset details
gh ruleset view <RULESET_ID>
```

### Step 3: Create Ruleset via API

```bash
# Create ruleset using the ruleset_01.yml configuration
gh api repos/:owner/:repo/rulesets \
  --method POST \
  --field name="Main Branch Protection Ruleset" \
  --field description="Protects the main branch from direct writes and enforces pull request workflow" \
  --field enforcement="active" \
  --field target="branch" \
  --field conditions='{"ref_name":{"include":["refs/heads/main"]}}' \
  --field rules='[{"name":"Require pull request reviews before merging","parameters":{"required_approving_review_count":1,"dismiss_stale_reviews_on_push":true}}]'
```

### Step 4: Verify Ruleset Creation

```bash
# List rulesets to confirm creation
gh ruleset list

# View the created ruleset
gh ruleset view <NEW_RULESET_ID>
```

## 🔍 Verification Steps

### Test 1: Direct Push to Main
```bash
# Try to push directly to main (should be blocked)
git push origin main
# Expected: Permission denied or branch protection error
```

### Test 2: Create Feature Branch
```bash
# Create and push a feature branch (should work)
git checkout -b feature/test-branch
git push origin feature/test-branch
# Expected: Success
```

### Test 3: Create Pull Request
```bash
# Create a pull request
gh pr create --title "Test PR" --body "Testing branch protection"
# Expected: PR created successfully
```

### Test 4: Try to Add Sensitive File
```bash
# Try to add a sensitive file
echo "secret_key=12345" > .env
git add .env
git commit -m "Add env file"
git push origin feature/test-branch
# Expected: Should be blocked by file pattern restrictions
```

## 🚨 Troubleshooting

### Common Issues:

#### Issue: Rules not applying
**Solution:**
- Verify you have admin permissions on the repository
- Check that the branch name pattern matches exactly (`main` vs `master`)
- Ensure the rule is saved and active
- Check repository settings for any conflicting rules

#### Issue: Can't push to main
**Expected Behavior:** This is the intended protection
**Solution:** Create a feature branch and use pull requests instead

#### Issue: PR blocked by status checks
**Solution:**
- Ensure all required status checks pass
- Check that the workflow is running correctly
- Verify the status check name matches exactly

#### Issue: File creation blocked
**Solution:**
- Check if the file matches restricted patterns
- Use alternative file names or locations
- Request pattern exceptions if needed

### Emergency Override:

Repository owners can temporarily disable rules:
1. Go to Settings → Branches
2. Click **Edit** on the main branch rule
3. Uncheck protection settings
4. Save changes
5. **Remember to re-enable after emergency fix**

## 📊 Monitoring and Maintenance

### Regular Checks:

```bash
# Check ruleset status
gh ruleset list

# View workflow runs
gh run list

# Check pull request status
gh pr list --state all
```

### Quarterly Review:

1. **Review rule effectiveness**
   - Monitor blocked pushes and PRs
   - Gather team feedback
   - Adjust approval requirements if needed

2. **Update patterns**
   - Review file restriction patterns
   - Add new sensitive file types
   - Remove outdated patterns

3. **Security audit**
   - Verify no sensitive files were committed
   - Check bypass usage
   - Review access permissions

## 🔗 Related Files

- `ruleset_01.yml` - Ruleset configuration template
- `.github/CODEOWNERS` - Automatic reviewer assignment
- `.github/workflows/branch-protection.yml` - Automated checks
- `.github/README.md` - GitHub configuration documentation

## 📞 Support

For issues with branch protection rules:
1. Check this guide first
2. Review GitHub documentation: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/defining-the-mergeability-of-pull-requests/about-protected-branches
3. Contact repository administrators

---

**Last Updated:** 2024-08-16  
**Version:** 1.0  
**Maintainer:** Repository Owner
