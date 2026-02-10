# Git Rulesets Guide

## Table of Contents
- [Overview](#overview)
- [What are Git Rulesets?](#what-are-git-rulesets)
- [Types of Rulesets](#types-of-rulesets)
- [Creating Rulesets](#creating-rulesets)
- [Ruleset Configuration](#ruleset-configuration)
- [Common Rules](#common-rules)
- [Best Practices](#best-practices)
- [Use Cases](#use-cases)
- [Troubleshooting](#troubleshooting)

## Overview

Git rulesets are a powerful feature in GitHub that allow repository administrators to enforce policies and protect important branches. They provide a more flexible and maintainable alternative to branch protection rules, with support for targeting multiple branches using patterns.

## What are Git Rulesets?

Git rulesets are collections of rules that control how users can interact with branches and tags in a repository. They can:

- Enforce branch naming conventions
- Require status checks to pass before merging
- Require pull request reviews
- Restrict who can push to specific branches
- Prevent force pushes and deletions
- Require signed commits
- Block creation of matching branches

Rulesets operate at the repository or organization level and use pattern matching to target branches and tags.

## Types of Rulesets

### 1. Branch Rulesets
Target branches using glob patterns. These are the most common type of ruleset.

**Example targets:**
- `main` - Exact branch name
- `release/*` - All branches starting with "release/"
- `**` - All branches
- `feature/**` - All branches in the feature directory

### 2. Tag Rulesets
Target tags using similar glob patterns.

**Example targets:**
- `v*` - All tags starting with "v"
- `release-*` - All tags starting with "release-"

## Creating Rulesets

### Via GitHub Web Interface

1. Navigate to your repository on GitHub
2. Click on **Settings**
3. In the left sidebar, click **Rules** under "Code and automation"
4. Click **Rulesets**
5. Click **New ruleset** → **New branch ruleset** (or tag ruleset)
6. Configure your ruleset:
   - Give it a descriptive name
   - Set enforcement status (Active/Disabled/Evaluate)
   - Define target branches/tags
   - Add rules
   - Set bypass permissions (optional)
7. Click **Create**

### Via GitHub API

You can also create and manage rulesets programmatically using the GitHub REST API:

```bash
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/rulesets \
  -d '{
    "name": "Protect main branch",
    "target": "branch",
    "enforcement": "active",
    "conditions": {
      "ref_name": {
        "include": ["refs/heads/main"],
        "exclude": []
      }
    },
    "rules": [
      {
        "type": "pull_request",
        "parameters": {
          "required_approving_review_count": 2
        }
      }
    ]
  }'
```

## Ruleset Configuration

### Enforcement Levels

- **Active**: Ruleset is enforced. Users must comply with all rules.
- **Evaluate**: Ruleset is in evaluation mode. GitHub tracks compliance but doesn't block actions.
- **Disabled**: Ruleset is disabled and not evaluated.

### Target Configuration

Use the **Target branches** or **Target tags** section to specify what the ruleset applies to:

- **Include patterns**: Patterns that must match
- **Exclude patterns**: Patterns to exclude from matches

**Pattern Examples:**
```
Include: main, develop
Exclude: (none)
Result: Applies only to main and develop branches

Include: release/*
Exclude: release/archive/*
Result: Applies to all release branches except those in archive/

Include: **
Exclude: feature/experimental/*
Result: Applies to all branches except experimental features
```

### Bypass Permissions

You can allow specific actors to bypass the ruleset:
- Repository administrators
- Specific users or teams
- GitHub Apps
- Deploy keys

## Common Rules

### 1. Require Pull Request Before Merging

Forces all changes to go through pull requests.

**Configuration:**
- Required number of approvals
- Dismiss stale reviews when new commits are pushed
- Require review from code owners
- Require approval of most recent reviewable push

### 2. Require Status Checks to Pass

Ensures CI/CD pipelines pass before merging.

**Configuration:**
- Required status checks
- Require branches to be up to date before merging

### 3. Require Commit Signing

Ensures all commits are signed with GPG, SSH, or S/MIME.

**Configuration:**
- Require signed commits: Yes/No

### 4. Block Force Pushes

Prevents force pushes that could rewrite history.

**Configuration:**
- Block force pushes: Yes/No

### 5. Require Linear History

Enforces a linear commit history by preventing merge commits.

**Configuration:**
- Require linear history: Yes/No
- Must use squash merging or rebase merging

### 6. Restrict Deletions

Prevents branch or tag deletion.

**Configuration:**
- Restrict deletions: Yes/No

### 7. Restrict Creations

Prevents creation of matching branches or tags.

**Configuration:**
- Restrict creations: Yes/No

### 8. Require Deployments to Succeed

Requires specific deployments to succeed before merging.

**Configuration:**
- Required deployment environments

### 9. Metadata Restrictions

Enforces commit metadata requirements.

**Configuration:**
- Commit message pattern
- Commit author email pattern
- Committer email pattern
- Branch name pattern

## Best Practices

### 1. Start with Evaluate Mode
When implementing new rulesets, start in **Evaluate** mode to understand impact before enforcing.

```
Enforcement: Evaluate → Monitor for 1-2 weeks → Switch to Active
```

### 2. Use Descriptive Names
Give your rulesets clear, descriptive names that explain their purpose.

**Good examples:**
- "Protect production branches"
- "Require reviews for main and release branches"
- "Enforce commit signing for all branches"

**Bad examples:**
- "Ruleset 1"
- "New ruleset"
- "Branch protection"

### 3. Leverage Pattern Matching
Use patterns to apply rules consistently across multiple branches.

```
Instead of creating separate rulesets for:
- feature/auth
- feature/payments
- feature/notifications

Create one ruleset targeting: feature/*
```

### 4. Document Your Rulesets
Maintain documentation explaining:
- Why each ruleset exists
- What branches/tags it targets
- Who can bypass it and why
- How to request exceptions

### 5. Layer Rulesets Strategically
Combine multiple rulesets for comprehensive protection:

```
Ruleset 1: All branches (**) - Require signed commits
Ruleset 2: main, develop - Require 2 reviews + CI passing
Ruleset 3: release/* - Require 3 reviews + deployment success
```

### 6. Configure Appropriate Bypass Permissions
Grant bypass permissions carefully:
- Emergency hotfix scenarios
- Automated system accounts
- Senior maintainers only

### 7. Keep Rules Simple and Focused
Avoid overly complex rulesets. Split into multiple rulesets if needed.

### 8. Regular Review and Updates
- Review rulesets quarterly
- Remove obsolete rules
- Update patterns as branch structure evolves
- Gather team feedback on ruleset effectiveness

## Use Cases

### Use Case 1: Protecting Main Branch

**Scenario**: Ensure all changes to main branch are reviewed and tested.

**Ruleset Configuration:**
- Name: "Main Branch Protection"
- Target: `main`
- Enforcement: Active
- Rules:
  - Require pull request with 2 approvals
  - Require status checks: CI, Tests, Linting
  - Require branches to be up to date
  - Block force pushes
  - Restrict deletions

### Use Case 2: Release Branch Management

**Scenario**: Strict controls on release branches with deployment verification.

**Ruleset Configuration:**
- Name: "Release Branch Requirements"
- Target: `release/*`
- Enforcement: Active
- Rules:
  - Require pull request with 3 approvals
  - Require code owner review
  - Require all status checks
  - Require deployment to staging
  - Require signed commits
  - Block force pushes
  - Restrict deletions

### Use Case 3: Feature Branch Standards

**Scenario**: Enforce best practices on feature branches.

**Ruleset Configuration:**
- Name: "Feature Branch Standards"
- Target: `feature/*`
- Enforcement: Active
- Rules:
  - Require pull request with 1 approval
  - Require basic status checks
  - Allow force pushes (for rebasing)
  - Allow deletions (after merging)

### Use Case 4: Tag Protection

**Scenario**: Prevent unauthorized tag modifications.

**Ruleset Configuration:**
- Name: "Tag Protection"
- Target: `v*` (version tags)
- Enforcement: Active
- Rules:
  - Restrict creations (only maintainers)
  - Restrict updates
  - Restrict deletions
  - Require signed commits

### Use Case 5: Organization-Wide Standards

**Scenario**: Apply consistent policies across all repositories in an organization.

**Organization-Level Ruleset Configuration:**
- Name: "Org Security Standards"
- Target: `main`, `master`, `production`
- Enforcement: Active
- Apply to: All repositories
- Rules:
  - Require signed commits
  - Require pull request reviews
  - Block force pushes

### Use Case 6: Hotfix Branch Exception

**Scenario**: Allow expedited merges for critical hotfixes while maintaining oversight.

**Ruleset Configuration:**
- Name: "Hotfix Process"
- Target: `hotfix/*`
- Enforcement: Active
- Rules:
  - Require pull request with 1 approval (reduced from normal 2)
  - Require basic CI only (skip longer tests)
  - Allow specific team to bypass for emergencies

## Troubleshooting

### Common Issues

#### Issue 1: Users Can't Push to Expected Branches

**Symptoms**: Error message when pushing to a branch that should be allowed.

**Solutions:**
1. Check if multiple rulesets are targeting the same branch
2. Verify the user has required permissions
3. Check if branch name matches include patterns but not exclude patterns
4. Review bypass permissions

#### Issue 2: Ruleset Not Taking Effect

**Symptoms**: Ruleset shows as active but doesn't enforce rules.

**Solutions:**
1. Verify enforcement is set to "Active" not "Evaluate"
2. Check target patterns are correct
3. Confirm rules are properly configured
4. Wait a few minutes for cache to update

#### Issue 3: Status Checks Not Required

**Symptoms**: PRs can be merged without required status checks passing.

**Solutions:**
1. Verify status check names match exactly (case-sensitive)
2. Ensure "Require status checks to pass" rule is enabled
3. Check if user has bypass permissions
4. Confirm status checks are running on the correct branch

#### Issue 4: Pattern Matching Not Working

**Symptoms**: Ruleset doesn't apply to expected branches.

**Solutions:**
1. Test patterns:
   - `main` - exact match only
   - `release/*` - one level deep
   - `release/**` - any depth
   - `**` - all branches
2. Check for typos in patterns
3. Verify include/exclude logic
4. Use the GitHub UI's pattern testing feature

#### Issue 5: Cannot Delete Old Branches

**Symptoms**: Error when trying to delete merged feature branches.

**Solutions:**
1. Check if "Restrict deletions" rule applies to the branch
2. Review if user needs bypass permissions for cleanup
3. Consider excluding merged branches from deletion restrictions
4. Use proper branch naming to exclude from patterns

### Getting Help

If you encounter issues:

1. **Check GitHub Status**: Visit https://www.githubstatus.com/
2. **Review GitHub Docs**: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets
3. **Check Audit Log**: Settings → Security → Audit log
4. **Contact Support**: If using GitHub Enterprise

## Additional Resources

- [GitHub Rulesets Documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets)
- [GitHub REST API - Rulesets](https://docs.github.com/en/rest/repos/rules)
- [Branch Protection vs Rulesets](https://github.blog/changelog/2023-08-22-repository-rules-generally-available/)
- [Organization Rulesets](https://docs.github.com/en/organizations/managing-organization-settings/managing-rulesets-for-repositories-in-your-organization)

---

**Last Updated**: February 2026  
**Version**: 1.0
