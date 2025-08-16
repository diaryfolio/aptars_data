# GitHub Configuration

This directory contains GitHub-specific configuration files for the Aptars data repository.

## Directory Structure

```
.github/
├── README.md                    # This file
├── CODEOWNERS                   # Automatic reviewer assignment
├── workflows/
│   └── branch-protection.yml    # Automated checks workflow
└── rules/
    └── manual_setup_guide.md    # Branch protection setup guide
```

## Branch Protection Rules

### manual_setup_guide.md

This guide provides step-by-step instructions for implementing comprehensive branch protection for the main branch with the following features:

#### 🔒 **Core Protection Rules:**

1. **Mandatory Pull Requests**: All changes must go through pull requests
2. **Required Reviews**: At least 1 approving review required before merging
3. **Branch Locking**: Prevents direct pushes to main branch
4. **Linear History**: Enforces clean, linear commit history
5. **Signed Commits**: Requires GPG-signed commits for security

#### 🛡️ **Security Features:**

- **File Creation Restrictions**: Prevents creation of sensitive files (`.env`, `*.key`, `secrets/`, etc.)
- **Force Push Prevention**: Blocks force pushes to main branch
- **File Deletion Protection**: Restricts deletion of files without proper review
- **Conversation Resolution**: Requires all conversations to be resolved before merging

#### 👥 **Bypass Settings:**

- Only repository owners can bypass these rules
- Maintains flexibility for emergency fixes while ensuring security

## Implementation Instructions

### 1. Apply Branch Protection via GitHub UI:

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Branches**
3. Click **Add rule** or **Add branch protection rule**
4. Configure the following settings:

#### Basic Settings:
- **Branch name pattern**: `main` (or `master`)
- ✅ **Require a pull request before merging**
- ✅ **Require approvals**: Set to 1
- ✅ **Dismiss stale PR approvals when new commits are pushed**
- ✅ **Require review from code owners** (if you have CODEOWNERS file)

#### Additional Settings:
- ✅ **Require status checks to pass before merging**
- ✅ **Require branches to be up to date before merging**
- ✅ **Require conversation resolution before merging**
- ✅ **Require signed commits**
- ✅ **Require linear history**
- ✅ **Restrict pushes that create files matching specified glob patterns**
- ✅ **Restrict pushes that delete files**
- ✅ **Restrict force pushes**

### 2. Configure File Pattern Restrictions:

Add these patterns to restrict sensitive file creation:
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

### 3. Set Up CODEOWNERS:

The CODEOWNERS file automatically requests reviews from specific team members:

```
# Global owners
* @repository-owner

# Data-specific owners
data/ @data-team
scripts/ @dev-team
docs/ @tech-writers
```

## GitHub Actions Workflow

### branch-protection.yml

This workflow runs automated checks on pull requests and pushes to main:

#### **Security Checks:**
- Detects sensitive files (`.env`, `*.key`, etc.)
- Checks for large files (>50MB)
- Validates JSON syntax
- Prevents merge commits in PRs

#### **Data Validation:**
- Ensures required directory structure exists
- Validates README files are present
- Checks documentation quality

#### **Documentation Quality:**
- Validates markdown syntax
- Checks for broken links (placeholder)

## Benefits

- **Security**: Prevents accidental commits of sensitive data
- **Quality**: Ensures code review before merging
- **Collaboration**: Enforces proper workflow for team contributions
- **Audit Trail**: Maintains clear history of all changes
- **Compliance**: Meets enterprise security requirements

## Troubleshooting

### Common Issues:

1. **Can't push to main**: This is expected behavior - create a feature branch instead
2. **PR blocked**: Ensure all required checks pass and reviews are approved
3. **File creation blocked**: Check if the file matches restricted patterns

### Emergency Override:

Repository owners can temporarily disable rules for emergency fixes, but should re-enable them immediately after.

## Maintenance

- Review and update rules quarterly
- Monitor rule effectiveness and team feedback
- Adjust approval requirements based on team size and project needs
