# Panduan Praktikal & Diagram - SCM Implementation untuk PesenMak

## 6. DIAGRAM VISUAL - BRANCHING STRATEGY

### 6.1 Git Flow Model - Diagram Komprehensif

```
                                    PRODUCTION RELEASE
                                    ═════════════════════
                                           ↓
                    ┌──────────────────────────────────────────┐
                    │          MAIN BRANCH (v1.x)              │
                    │   Always Production-Ready & Stable       │
                    │  Hanya receive dari release & hotfix      │
                    └──────────────────────────────────────────┘
                      ↑                                      ↑
                      │                                      │
                 merge v1.0                            merge hotfix
                      │                                      │
         ┌────────────┴────────────┐              ┌─────────┴──────────┐
         │                         │              │                    │
    ┌────┴────┐             ┌──────┴─────┐   ┌───┴────┐        ┌──────┴──┐
    │ release │             │   hotfix   │   │ hotfix │        │ hotfix  │
    │ v1.0.0  │             │ v1.0.1     │   │v1.0.2  │        │ v1.0.3  │
    │(testing)│             │(critical)  │   │(patch) │        │(patch)  │
    └────┬────┘             └──────┬─────┘   └───┬────┘        └──────┬──┘
         │                         │             │                    │
         │                    merge back        merge back         merge back
         │                         │             │                    │
         └─────────┬──────────────┬┴─────────────┴────────────────────┘
                   │              │
                   ↓              ↓
    ┌──────────────────────────────────────────────────────┐
    │        DEVELOP BRANCH (Integration Point)            │
    │     Staging, Testing, Feature Integration            │
    │  Multiple features dapat di-develop parallel         │
    └──────────────────────────────────────────────────────┘
         ↑          ↑         ↑          ↑          ↑
         │          │         │          │          │
    merge PR    merge PR  merge PR   merge PR   merge PR
         │          │         │          │          │
    ┌────┴───┐ ┌────┴───┐ ┌──┴────┐ ┌───┴────┐ ┌───┴────┐
    │feature │ │feature │ │bugfix │ │chore   │ │feature │
    │ /cart  │ │/payment│ │/timeout│ │/docs   │ │/wishlist
    │        │ │        │ │       │ │        │ │        │
    │ WIP    │ │ WIP    │ │ READY │ │ DONE   │ │ WIP    │
    └────────┘ └────────┘ └───────┘ └────────┘ └────────┘
    
Legend:
   ═════  Production release
   ─────  Integration/Staging
   ─ ─ ─  Feature/Support branches
   ┌──┐   Active branch
   │  │   Completed feature
   └──┘
```

### 6.2 Feature Branch Lifecycle

```
┌─────────────────────────────────────────────────────────────────┐
│ LIFECYCLE OF FEATURE/SHOPPING-CART BRANCH                       │
└─────────────────────────────────────────────────────────────────┘

DAY 1: Branch Creation
─────────────────────────────────────────────────────────────────

develop (HEAD: abc1234)
  │
  ├─ Create feature/shopping-cart
  │
  └─ feature/shopping-cart
      HEAD: abc1234

Command:
$ git checkout -b feature/shopping-cart develop

┌─────────────────────────────────────────────────────────────────┐

DAY 2-3: Active Development

develop (HEAD: abc1234)
          Master branch tetap unchanged

feature/shopping-cart
    │
    ├─ def5678 feat(cart): create ShoppingCart class
    │  Author: Developer A
    │  Time: Day 2 09:00
    │
    ├─ ghi9012 feat(cart): add addItem method
    │  Author: Developer A
    │  Time: Day 2 14:30
    │
    └─ jkl3456 feat(cart): implement removeItem method
       Author: Developer A
       Time: Day 3 10:15

Meanwhile on develop (other developers):

develop (HEAD: xyz7890)
    │
    ├─ abc1234 [ORIGINAL]
    │
    ├─ mno4567 feat(wishlist): initial setup
    │  Author: Developer B
    │  Time: Day 2 11:00
    │
    └─ pqr8901 feat(wishlist): add UI components
       Author: Developer B
       Time: Day 3 15:45

DIVERGED STATE! Both branches have different commits.

┌─────────────────────────────────────────────────────────────────┐

DAY 4: Sync with develop (Before PR)

feature/shopping-cart developer runs:
$ git fetch origin
$ git rebase origin/develop

Rebase process:
  1. Take original commits from feature/shopping-cart
  2. Put them on top of develop's latest
  3. Result: Linear history

BEFORE REBASE:
develop: abc → mno → pqr
          ↘
feature:  def → ghi → jkl

AFTER REBASE:
develop: abc → mno → pqr (unchanged)
                      ↘
feature:             def' → ghi' → jkl'
                     (rebased commits)

Now feature branch is up-to-date with develop!

┌─────────────────────────────────────────────────────────────────┐

DAY 4: Create Pull Request

feature/shopping-cart developer creates PR:

GitHub UI shows:
───────────────────────────────────────────────────────
Comparing: develop ← feature/shopping-cart
Commits: 3 (def5678, ghi9012, jkl3456)
Files changed: 3 files
───────────────────────────────────────────────────────

✓ GitHub Actions triggered:
  ├─ Build: ✓ Success
  ├─ Unit Tests: ✓ 15/15 passed
  ├─ Coverage: ✓ 85% (target: 80%)
  ├─ Security: ✓ No vulnerabilities
  └─ Code Quality: ✓ Grade A

PR Status: READY FOR REVIEW ✓

┌─────────────────────────────────────────────────────────────────┐

DAY 4: Code Review & Feedback

Reviewer (Tech Lead) checks:
├─ ✓ Logic is correct
├─ ✓ Code quality good
├─ ✗ Edge case missing: what if cart is empty?
└─ REQUEST CHANGES

Developer responds by adding:
$ git commit -m "fix(cart): add validation for empty cart"
$ git push origin feature/shopping-cart

GitHub auto-updates PR with new commit:
├─ ✓ All checks still pass
└─ New commit: stu2345 fix(cart): add validation

Reviewer approves: ✓ APPROVED

Second reviewer also approves (requirement: 2 approvals)

┌─────────────────────────────────────────────────────────────────┐

DAY 5: Merge to develop

All conditions met:
✓ 2 approvals obtained
✓ All status checks passing
✓ No conflicts
✓ Conversations resolved

GitHub "Squash and merge" button clicked:

BEFORE MERGE:
develop: abc → mno → pqr
                      ↘
feature:             def → ghi → jkl → stu

MERGE ACTION (Squash):
All commits (def, ghi, jkl, stu) → 1 commit

AFTER MERGE:
develop: abc → mno → pqr → vwx (merged commit)
                             │
                     (contains all changes from feature)

GitHub auto-generates merge commit message:
"Merge pull request #42 from feature/shopping-cart

feat(cart): implement shopping cart functionality

- Create ShoppingCart class
- Add item management methods
- Add empty cart validation
- 85% test coverage"

Closes #10 (Issue auto-closed)

┌─────────────────────────────────────────────────────────────────┐

DAY 5: Cleanup

GitHub auto-deletes remote branch:
feature/shopping-cart → DELETED from origin/

Developer cleans up locally:
$ git checkout develop
$ git pull origin develop
$ git branch -D feature/shopping-cart

FINAL STATE:
develop: abc → mno → pqr → vwx (has merged feature)
                             ↓
                        Production-ready
                        for next release

feature/shopping-cart: [DELETED]
```

### 6.3 Release Cycle Visualization

```
┌─────────────────────────────────────────────────────────────────┐
│ RELEASE CYCLE: v1.0.0 RELEASE                                   │
└─────────────────────────────────────────────────────────────────┘

WEEK 1-2: Development Sprint
──────────────────────────

develop:  f0 → f1 → f2 → f3 → f4
           ↓    ↓    ↓    ↓    ↓
           Feature branches being developed and merged

main:     v0.9.9 (stable, no changes)


WEEK 3: Release Preparation
──────────────────────────

Step 1: Create release branch from develop

$ git checkout -b release/v1.0.0 develop

develop:  f0 → f1 → f2 → f3 → f4
                                ↓
release:                      r0 (version bump)


Step 2: Finalize release branch (testing, minor fixes)

release:  f0 → f1 → f2 → f3 → f4 → r0 → r1 → r2
          └─────────────────────────────────────┘
                    Version 1.0.0
                   (No new features)
                   (Only bugs & refinement)


Step 3: Merge to main with tag

$ git checkout main
$ git merge --no-ff release/v1.0.0
$ git tag -a v1.0.0 -m "Release v1.0.0"

main:     v0.9.9 → v1.0.0 (tagged)
                   └─ Ready for production deploy

release:  (continues for patch releases if needed)


Step 4: Merge back to develop

$ git checkout develop
$ git merge --no-ff release/v1.0.0

develop:  f0 → f1 → f2 → f3 → f4 → r0 → r1 → r2
                                   ↓
                            sync with release


Step 5: Delete release branch

$ git branch -d release/v1.0.0

Final state:
main:     v0.9.9 → v1.0.0 [PRODUCTION]
develop:  f0 → f1 → f2 → f3 → f4 → r0 → r1 → r2 [STAGING]


WEEK 4: Production & Hotfix
──────────────────────────

Production deployed (v1.0.0)

If critical bug found → hotfix branch

main:     v0.9.9 → v1.0.0 → h0 → h1 (v1.0.1 hotfix)
                    ↓
                [BUG FOUND]
                    │
                    ├─ Create hotfix/v1.0.1
                    │
                    └─ Fix critical issue
                        $ git checkout -b hotfix/v1.0.1 main
                        
Hotfix applied:
main:     v0.9.9 → v1.0.0 → h0 (hotfix merged) → v1.0.1 tagged

develop:  ... → r0 → r1 → r2 → h0 (sync back from hotfix)

The hotfix is also merged to develop to keep in sync!
```

### 6.4 Conflict Resolution Visualization

```
┌─────────────────────────────────────────────────────────────────┐
│ MERGE CONFLICT SCENARIO: CartService.java                       │
└─────────────────────────────────────────────────────────────────┘

PARALLEL DEVELOPMENT
────────────────────

feature/discount:
├─ Modify CartService.java
├─ Add applyDiscount() method
└─ calculateTotal() with discount logic

feature/tax:
├─ Modify CartService.java
├─ Add calculateTax() method
└─ calculateTotal() with tax logic

BOTH MODIFY SAME FILE - calculateTotal() method


ATTEMPT TO MERGE feature/discount TO develop
─────────────────────────────────────────────

$ git checkout develop
$ git merge feature/discount

Result: ✓ SUCCESS (no conflict)

develop:  abc → def → ghi (feature/discount merged)


ATTEMPT TO MERGE feature/tax TO develop
────────────────────────────────────────

$ git checkout develop
$ git merge feature/tax

Result: ✗ CONFLICT in CartService.java!

Git cannot auto-merge because:
- Both branches modified calculateTotal()
- Git doesn't know which version to keep


CONFLICT IN FILE
─────────────────

CartService.java shows:

public class CartService {
    
<<<<<<< HEAD (develop branch - has discount)
    public double calculateTotal() {
        double subtotal = getSubtotal();
        double discounted = subtotal * 0.9;  // 10% discount
        return discounted;
    }
=======
    public double calculateTotal() {
        double subtotal = getSubtotal();
        double tax = subtotal * 0.05;  // 5% tax
        return subtotal + tax;
    }
>>>>>>> feature/tax (incoming changes - has tax)
    
}


RESOLUTION STRATEGY 1: COMBINE BOTH
────────────────────────────────────

Manual edit to:

public class CartService {
    
    public double calculateTotal() {
        double subtotal = getSubtotal();
        
        // Apply discount first
        double discounted = subtotal * 0.9;  // 10% discount
        
        // Then apply tax on discounted amount
        double tax = discounted * 0.05;  // 5% tax
        
        return discounted + tax;
    }
    
}

$ git add CartService.java
$ git commit -m "merge: combine discount and tax in calculateTotal

- Discount applied first (10%)
- Tax calculated on discounted amount (5%)
- Order: Subtotal → Discount → Tax"


RESOLUTION STRATEGY 2: USE THEIRS
──────────────────────────────────

$ git checkout --theirs CartService.java
# Takes tax version, discard discount

$ git add CartService.java
$ git commit


RESOLUTION STRATEGY 3: USE OURS
────────────────────────���───────

$ git checkout --ours CartService.java
# Keeps discount version, discard tax

$ git add CartService.java
$ git commit


VISUAL: Conflict Resolution in Git History
───────────────────────────────────────────

BEFORE MERGE:
develop:   abc → def → ghi (discount merged)
feature/tax: abc → jkl → mno (tax changes)
              ↑
              Common ancestor

MERGE CONFLICT at ghi vs mno

AFTER RESOLUTION (Strategy 1: Combine):
           ╱─ ghi (discount)
abc → def ─╲─ mno (tax)
           └─ pqr (merged - both combined)
                ↓
            develop (resolved)

AFTER RESOLUTION (Strategy 2: Use THEIRS):
           ╱─ ghi (discount) - IGNORED
abc → def ─╲─ mno (tax) - USED
           └─ pqr (merged - only tax)
                ↓
            develop (resolved)
```

---

## 7. CHECKLIST IMPLEMENTASI SCM UNTUK TIM

### 7.1 Pre-Development Checklist

```
□ Repository Setup
  □ Repository created on GitHub
  □ Repository settings configured
  □ Default branch set to "main"
  □ README.md exists
  □ .gitignore configured
  □ LICENSE added
  □ CONTRIBUTING.md added

□ Branch Protection Setup
  □ Branch protection rules for "main"
    □ Require pull request before merging: YES
    □ Require status checks to pass: YES
    □ Require 2 approvals: YES
    □ Dismiss stale PR approvals: YES
    □ Require conversation resolution: YES
    □ Include administrators: YES
  □ Branch protection rules for "develop"
    □ Require pull request before merging: YES
    □ Require status checks to pass: YES
    □ Require 1 approval: YES
    □ Include administrators: NO (allow force merge for critical)

□ Code Review Configuration
  □ CODEOWNERS file created
  □ Code review policy documented
  □ Review checklist template created
  □ PR template created (.github/pull_request_template.md)

□ CI/CD Setup
  □ GitHub Actions workflow configured
  □ Build pipeline working
  □ Automated tests running
  □ Code coverage reports generated
  □ Security scanning enabled
  □ Code quality checks enabled
  □ Deployment pipeline ready

□ Team Communication
  □ Team members added as collaborators
  □ Roles assigned (Developer, Reviewer, Admin)
  □ Permissions configured
  □ Communication channels setup (Slack, Teams, etc.)
  □ Code review SLA defined (e.g., 24h turnaround)
```

### 7.2 Developer Onboarding Checklist

```
□ Git Configuration (on first day)
  □ Set git user.name
  □ Set git user.email
  □ Configure git editor (if needed)
  □ Setup SSH keys for GitHub
  □ Test git clone works

□ Local Development Setup
  □ Clone repository: git clone ...
  □ Install dependencies (npm, maven, gradle, etc.)
  □ Setup database (if needed)
  □ Configure environment variables
  □ Run local tests successfully
  □ Setup IDE/Editor with project

□ Branching Practice
  □ Understand branch naming conventions
  □ Know what each branch type is for
  □ Practice creating feature branch
  □ Practice syncing with main/develop
  □ Practice resolving basic conflicts

□ Commit Practice
  □ Understand Conventional Commits format
  □ Practice writing good commit messages
  □ Know what goes in commit message body
  □ Practice committing small, focused changes

□ PR Process
  □ Create first feature branch
  □ Make small changes and push
  □ Create Pull Request
  □ Fill out PR template properly
  □ Understand PR review process
  □ Practice addressing feedback
  □ Merge and cleanup branch

□ Knowledge Transfer
  □ Read CONTRIBUTING.md
  □ Read SCM-STRATEGY.md (this document)
  □ Review existing PRs as examples
  □ Pair program with senior developer
  □ Ask questions!
```

### 7.3 Code Review Checklist

```
Before Starting Review:
□ Set aside focused time (no interruptions)
□ Have coffee/water nearby
□ Close other browser tabs
□ Skim PR title and description first

Functional Review:
□ Does the code solve the stated problem?
□ Does it handle edge cases?
□ Are there any obvious bugs?
□ Does it handle errors gracefully?
□ Are there any performance issues?
□ Is the solution optimal or over-engineered?

Code Quality Review:
□ Is code readable and understandable?
□ Are variable names clear and descriptive?
□ Are functions/methods appropriately sized?
□ Is there code duplication?
□ Are there magic numbers that need constants?
□ Is the code consistent with existing codebase?

Testing:
□ Is test coverage adequate (80%+)?
□ Do tests cover happy path?
□ Do tests cover edge cases?
□ Do tests cover error scenarios?
□ Are test names descriptive?
□ Are there any flaky tests?

Security:
□ Are there any hardcoded secrets/credentials?
□ Is input validated properly?
□ Is output encoded (for XSS prevention)?
□ Are there any SQL injection risks?
□ Is authentication/authorization proper?
□ Are sensitive operations logged?

Documentation:
□ Is code well-commented (especially complex logic)?
□ Are public APIs documented?
□ Is README updated if needed?
□ Are breaking changes documented?
□ Is migration guide provided (if applicable)?

Before Approving:
□ All checks above completed
□ All automated tests passing
□ Code review checklist satisfied
□ Take another look at critical sections
□ Approve with confidence
```

### 7.4 Release Checklist

```
PRE-RELEASE (1 week before)
─────────────────────────────
□ Ensure develop branch is stable
□ Run full test suite
□ Check code coverage is acceptable
□ Review security scan reports
□ Get buy-in from team

RELEASE BRANCH CREATION
─────────────────────────
□ Create release branch: release/vX.Y.Z
□ Update version numbers in code
□ Update CHANGELOG.md
□ Update documentation
□ Commit with message: "chore(release): v X.Y.Z"

TESTING & FIXING
─────────────────────────
□ Deploy to staging environment
□ Run full regression tests
□ Conduct QA testing
□ Fix bugs found (on release branch)
□ Get sign-off from QA

MERGE TO MAIN
─────────────────────────
□ Ensure all tests passing
□ Merge release branch to main
□ Create git tag: vX.Y.Z
□ Push tag to GitHub
□ Merge main back to develop (sync)
□ Delete release branch

DEPLOYMENT
─────────────────────────
□ Build final artifact
□ Deploy to production
□ Run smoke tests in production
□ Monitor application health
□ Get confirmation from ops team

POST-RELEASE
─────────────────────────
□ Create release notes on GitHub
□ Notify stakeholders
□ Document any issues found
□ Plan patch releases if needed
□ Post-mortem if any issues
□ Close related issues/milestones
```

---

## 8. COMMON PROBLEMS & SOLUTIONS

### 8.1 Masalah: "I accidentally committed to main"

**Solusi:**

```bash
# Undo last commit but keep changes
git reset --soft HEAD~1

# Verify changes are staged
git status

# Create proper branch
git checkout -b feature/new-feature

# Commit properly
git commit -m "feat: proper commit message"

# Push
git push -u origin feature/new-feature
```

### 8.2 Masalah: "I pushed to the wrong branch"

**Solusi:**

```bash
# Get the commit hash
git log --oneline | head -5

# Revert on wrong branch
git revert <commit-hash>
git push origin wrong-branch

# Or reset if not yet pulled by others
git reset --hard HEAD~1
git push -f origin wrong-branch  # ONLY if no one else is using

# Cherry-pick to correct branch
git checkout correct-branch
git cherry-pick <commit-hash>
git push origin correct-branch
```

### 8.3 Masalah: "I have conflicts in my PR"

**Solusi:**

```bash
# Fetch latest from remote
git fetch origin

# Rebase on target branch
git rebase origin/develop

# If conflicts:
# 1. Edit files to resolve
# 2. Mark as resolved
git add <resolved-files>

# Continue rebase
git rebase --continue

# Push (force push because rebased)
git push -f origin feature-branch
```

### 8.4 Masalah: "I deleted my local branch but need it back"

**Solusi:**

```bash
# Check reflog to find deleted branch
git reflog

# Example output:
# c1a2b3d (HEAD -> develop) commit: merge feature
# f4e5d6c HEAD@{1}: checkout: moving from feature/X to develop
# a7b8c9d HEAD@{2}: commit: last commit in feature/X

# Recover deleted branch
git checkout -b feature/X a7b8c9d

# Or if already on remote
git checkout --track origin/feature/X
```

### 8.5 Masalah: "My commit is in the wrong place in history"

**Solusi:**

```bash
# Using git rebase -i (interactive rebase)
git rebase -i HEAD~5  # Shows last 5 commits

# Interactive menu appears:
# pick a1b2c3d feat: feature 1
# pick f4e5d6c feat: feature 2
# pick g7h8i9j fix: bug fix
# pick k0l1m2n chore: update deps
# pick o3p4q5r feat: feature 3

# Reorder by cutting/pasting lines, then save

# Or squash commits into one
# pick a1b2c3d feat: feature 1
# squash f4e5d6c feat: sub-feature
# squash g7h8i9j fix: bug in feature 1
# pick k0l1m2n chore: update deps
# pick o3p4q5r feat: feature 3
```

### 8.6 Masalah: "I need to undo a merged PR"

**Solusi:**

```bash
# Option 1: Revert the merge commit
git revert -m 1 <merge-commit-hash>
git push origin develop

# Option 2: Reset to before merge (if recent)
git reset --hard <commit-before-merge>
git push -f origin develop  # DANGEROUS - only if no one else is using

# Option 3: Cherry-pick individual commits back
# If the merged feature had 5 commits and only 2 are bad
git revert <bad-commit-1>
git revert <bad-commit-2>
git push origin develop
```

---

## 9. GIT COMMANDS QUICK REFERENCE

### 9.1 Essential Commands Grouped by Task

```bash
# ═══════════════════════════════════════════════════════════════
# SETUP
# ═══════════════════════════════════════════════════════════════
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git clone <repository-url>
cd <repository>

# ═══════════════════════════════════════════════════════════════
# BRANCHING
# ═══════════════════════════════════════════════════════════════
git branch                          # List local branches
git branch -a                       # List all branches (local + remote)
git branch <branch-name>            # Create branch
git checkout <branch-name>          # Switch to branch
git checkout -b <branch-name>       # Create and switch
git push -u origin <branch-name>    # Push new branch to remote
git branch -d <branch-name>         # Delete local branch
git push origin --delete <branch-name>  # Delete remote branch

# ═══════════════════════════════════════════════════════════════
# COMMITTING
# ═══════════════════════════════════════════════════════════════
git status                          # See changes
git add <file>                      # Stage specific file
git add .                           # Stage all changes
git commit -m "message"             # Commit with message
git commit --amend                  # Modify last commit
git commit -m "feat: message" --no-verify  # Skip pre-commit hooks (if needed)

# ═══════════════════════════════════════════════════════════════
# PUSHING & PULLING
# ═══════════════════════════════════════════════════════════════
git push origin <branch-name>       # Push branch
git push -u origin <branch-name>    # Push and set upstream
git push origin --all               # Push all branches
git pull origin <branch-name>       # Fetch and merge
git pull origin <branch-name> --rebase  # Fetch and rebase
git fetch origin                    # Just fetch, don't merge

# ═══════════════════════════════════════════════════════════════
# MERGING & REBASING
# ═══════════════════════════════════════════════════════════════
git merge <branch-name>             # Merge branch
git merge --no-ff <branch-name>     # Merge with merge commit
git merge --squash <branch-name>    # Squash merge
git rebase <branch-name>            # Rebase current on branch
git rebase -i HEAD~3                # Interactive rebase

# ═══════════════════════════════════════════════════════════════
# VIEWING HISTORY
# ═══════════════════════════════════════════════════════════════
git log                             # Show commit history
git log --oneline                   # Compact view
git log --oneline --graph --all     # Tree visualization
git log -p                          # Show diffs
git log --author="Name"             # Filter by author
git show <commit-hash>              # Show specific commit
git diff                            # Show unstaged changes
git diff --staged                   # Show staged changes
git diff <branch1> <branch2>        # Compare branches

# ═══════════════════════════════════════════════════════════════
# FIXING MISTAKES
# ═══════════════════════════════════════════════════════════════
git reset HEAD <file>               # Unstage file
git reset HEAD~1                    # Undo last commit (keep changes)
git reset --hard HEAD~1             # Undo last commit (discard changes)
git revert <commit-hash>            # Create new commit that undoes changes
git checkout -- <file>              # Discard changes in file
git stash                           # Temporarily save changes
git stash pop                       # Restore stashed changes
git stash drop                      # Discard stashed changes
git reflog                          # See all branch operations
git tag <tag-name>                  # Create tag
git push origin <tag-name>          # Push tag

# ═══════════════════════════════════════════════════════════════
# CLEANING UP
# ═══════════════════════════════════════════════════════════════
git clean -fd                       # Remove untracked files
git gc                              # Garbage collection
git prune                           # Remove unreachable commits
```

### 9.2 Advanced Git Workflows

```bash
# ═══════════════════════════════════════════════════════════════
# SQUASH MULTIPLE COMMITS INTO ONE
# ═══════════════════════════════════════════════════════════════
git rebase -i origin/develop

# Change lines in interactive editor:
# pick abc1234 feat: first
# squash def5678 feat: second
# squash ghi9012 feat: third

# Result: 1 commit with all 3 changes

# ═══════════════════════════════════════════════════════════════
# CHERRY-PICK SPECIFIC COMMITS
# ═══════════════════════════════════════════════════════════════
git cherry-pick <commit-hash>       # Apply one commit
git cherry-pick <hash1> <hash2> <hash3>  # Multiple commits

# ═══════════════════════════════════════════════════════════════
# FIND WHICH COMMIT INTRODUCED A BUG
# ═══════════════════════════════════════════════════════════════
git bisect start
git bisect bad HEAD                 # Current is bad
git bisect good v1.0                # Old version is good
# Git checks out commits to test
git bisect bad                      # If current is bad
# or
git bisect good                     # If current is good
# Continue until bug is found
git bisect reset                    # Done, back to original branch

# ═══════════════════════════════════════════════════════════════
# SEARCH FOR STRING IN HISTORY
# ═══════════════════════════════════════════════════════════════
git log -S "search string"          # Search commit diffs
git grep "search string"            # Search in tracked files
git log --grep="search string"      # Search commit messages

# ═══════════════════════════════════════════════════════════════
# MANAGE MULTIPLE STASHES
# ═══════════════════════════════════════════════════════════════
git stash list                      # Show all stashes
git stash save "description"        # Save with description
git stash pop stash@{2}             # Apply specific stash
git stash drop stash@{2}            # Delete specific stash
```

---

## 10. IMPLEMENTASI UNTUK PESENMAK

### 10.1 Recommended Setup untuk PesenMak

```bash
# ═══════════════════════════════════════════════════════════════
# WEEK 1: SETUP INFRASTRUCTURE
# ═══════════════════════════════════════════════════════════════

Step 1: Create repository structure
├─ Create develop branch from main
├─ Setup branch protection rules for main and develop
├─ Create PR template
├─ Create CONTRIBUTING.md

Step 2: Setup CI/CD
├─ Create GitHub Actions workflow for build & test
├─ Setup code coverage reports
├─ Enable security scanning
├─ Enable code quality checks (SonarQube, CodeClimate)

Step 3: Team preparation
├─ Add team members as collaborators
├─ Assign reviewer roles
├─ Document review SLA
├─ Schedule training session

# ═══════════════════════════════════════════════════════════════
# WEEK 2: TEAM TRAINING
# ═══════════════════════════════════════════════════════════════

Session 1: Git Basics (2 hours)
├─ Git config and setup
├─ Clone, branch, commit basics
├─ Push and pull workflow
└─ Q&A

Session 2: Branching Strategy (2 hours)
├─ Git Flow explained
├─ When to create which branch
├─ Feature branch lifecycle
└─ Hands-on: Create first feature branch

Session 3: PR & Code Review (2 hours)
├─ Creating good pull requests
├─ Code review process and etiquette
├─ Conventional commits
└─ Hands-on: Create and review sample PR

Session 4: Conflict Resolution (1 hour)
├─ What causes conflicts
├─ Resolving conflicts safely
├─ Best practices
└─ Hands-on: Create and resolve conflict

# ═══════════════════════════════════════════════════════════════
# WEEK 3: DRY RUN (Practice with small changes)
# ═══════════════════════════════════════════════════════════════

All developers:
├─ Create feature/practice-branch
├─ Make small documentation change
├─ Create PR with template
├─ Get reviewed by Tech Lead
├─ Address feedback
├─ Merge to develop
└─ Verify CI/CD workflow completes successfully

# ═══════════════════════════════════════════════════════════════
# WEEK 4: PRODUCTION READY
# ═══════════════════════════════════════════════════════════════

Team starts actual development:
├─ Create real feature branches
├─ Implement features following SCM process
├─ Follow all branching, PR, review rules
├─ Deploy to staging and production via CI/CD
└─ Monitor and iterate
```

### 10.2 Git Aliases untuk PesenMak Team

Tambahkan ke `~/.gitconfig` untuk workflow yang lebih cepat:

```bash
[alias]
    # Branching
    fb = "!git branch --list | grep"
    cb = "checkout -b"
    
    # Viewing
    lg = "log --oneline --graph --all --decorate --color"
    st = "status"
    co = "checkout"
    
    # Committing
    aa = "add ."
    ci = "commit -m"
    
    # Pushing/Pulling
    p = "push"
    pl = "pull"
    plo = "pull origin"
    plor = "pull origin --rebase"
    
    # Cleanup
    cleanup = "!git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -d"
    
    # Create PR-ready branch
    newfeature = "!git checkout -b"
    
    # See what I changed today
    today = "!git log --since='1 day ago' --oneline"
    
    # See what others changed
    changes = "!git log -1 --format='%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'"
```

Usage:

```bash
git lg               # Instead of git log --oneline --graph --all --decorate --color
git cb feature/new   # Instead of git checkout -b feature/new develop
git newfeature feature/new develop
```

---

## RINGKASAN KESELURUHAN

Implementasi SCM yang baik menggunakan Git dan GitHub untuk proyek PesenMak mencakup:

### ✅ Strategi Branching
- Main → Production, always stable
- Develop → Staging, integration point
- Feature/* → Development branches
- Release/* → Pre-release preparation
- Hotfix/* → Critical production fixes

### ✅ Git Best Practices
- Conventional Commits format
- Small, focused commits
- Meaningful commit messages
- Regular syncing with main branch
- Proper conflict resolution

### ✅ GitHub Collaboration
- Branch Protection Rules enforcement
- Mandatory Pull Requests & Code Review
- Automated CI/CD pipeline
- At least 2 approvals for main
- Conversation resolution requirement

### ✅ Team Culture
- Knowledge sharing through review
- Clear roles and responsibilities
- Traceability and audit trail
- Quality over speed
- Continuous improvement

**Dengan implementasi ini, tim PesenMak dapat mengembangkan aplikasi e-commerce dengan cara yang terstruktur, profesional, dan berkualitas tinggi.**

---

**Document Version:** 1.0  
**Last Updated:** 2026-07-15  
**Created for:** PesenMak E-commerce Development Team  
**Repository:** 411202061-max/Proyek
