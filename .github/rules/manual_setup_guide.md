# Manual Branch Protection Setup Guide

Since the JSON import and API methods are having compatibility issues, here's the most reliable manual setup method.

## 🎯 **Step-by-Step Manual Setup**

### Step 1: Access Branch Protection Settings

1. Go to your repository: `https://github.com/diaryfolio/aptars_data`
2. Click **Settings** tab
3. In the left sidebar, click **Branches**

### Step 2: Add Branch Protection Rule

1. Click **Add rule** or **Add branch protection rule**
2. In **Branch name pattern**, enter: `main`
3. Click **Create**

### Step 3: Configure Basic Settings

✅ **Check these boxes:**

#### Pull Request Settings:
- [ ] **Require a pull request before merging**
- [ ] **Require approvals**: Set to `1`
- [ ] **Dismiss stale PR approvals when new commits are pushed**
- [ ] **Require review from code owners**

#### Status Checks:
- [ ] **Require status checks to pass before merging**
- [ ] **Require branches to be up to date before merging**
- [ ] **Status checks that are required**: Add `Branch Protection Checks`

### Step 4: Configure Additional Settings

✅ **Check these boxes:**

#### Security Settings:
- [ ] **Require conversation resolution before merging**
- [ ] **Require signed commits**
- [ ] **Require linear history**

#### Restrict Pushes:
- [ ] **Restrict force pushes**
- [ ] **Restrict pushes that delete files**

### Step 5: Configure File Pattern Restrictions

✅ **Check this box:**
- [ ] **Restrict pushes that create files matching specified glob patterns**

**Add these patterns:**
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

### Step 6: Save the Rule

1. Scroll to the bottom
2. Click **Create** or **Save changes**

## 🔍 **Verification Steps**

### Test 1: Try Direct Push to Main
```bash
git push origin main
# Expected: Should be blocked with "remote: error: GH006: Protected branch update failed"
```

### Test 2: Create Feature Branch
```bash
git checkout -b feature/test
git push origin feature/test
# Expected: Should work normally
```

### Test 3: Create Pull Request
```bash
gh pr create --title "Test PR" --body "Testing branch protection"
# Expected: PR created successfully
```

### Test 4: Try to Add Sensitive File
```bash
echo "secret=123" > .env
git add .env
git commit -m "Add env file"
git push origin feature/test
# Expected: Should be blocked by file pattern restrictions
```

## 🚨 **Troubleshooting**

### If rules aren't working:
1. **Check permissions**: Ensure you're a repository admin
2. **Verify branch name**: Make sure it's exactly `main` (not `master`)
3. **Check rule status**: Ensure the rule is active
4. **Clear cache**: Try refreshing the page

### If you need to bypass rules:
1. Go to Settings → Branches
2. Click **Edit** on the main branch rule
3. Uncheck protection settings temporarily
4. Save changes
5. **Remember to re-enable after emergency fix**

## ✅ **Expected Results**

After setup, you should see:
- ✅ Direct pushes to main are blocked
- ✅ Pull requests require approval
- ✅ Sensitive files are blocked
- ✅ Force pushes are prevented
- ✅ Status checks are required

## 📊 **Monitoring**

Check that rules are working:
```bash
# List pull requests
gh pr list

# Check workflow runs
gh run list

# View repository status
gh repo view
```

---

**Note**: This manual method is the most reliable and gives you full control over each setting. The JSON import feature may have compatibility issues with certain rule configurations.
