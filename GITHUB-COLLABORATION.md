# Konfigurasi Kolaborasi GitHub - PesenMak

## 3. KONFIGURASI KOLABORASI PROFESIONAL DI GITHUB

### 3.1 Branch Protection Rules

**Fungsi:** Mengontrol siapa yang bisa push dan merge ke branch kritis, memastikan kualitas kode sebelum masuk production.

#### **3.1.1 Implementasi Branch Protection untuk `main` dan `develop`**

```
GitHub Repository Settings → Branches → Branch Protection Rules
```

#### **Konfigurasi untuk Branch `main` (Production)**

```
Rule applied to: main

☑ Require a pull request before merging
   ☑ Require approval from reviewers (Minimum: 2)
   ☑ Require review from Code Owners
   ☑ Dismiss stale pull request approvals when new commits are pushed
   ☑ Require approval of the most recent reviewable push

☑ Require status checks to pass before merging
   ☑ Require branches to be up to date before merging
   ☑ Require passing builds/tests:
      - build (GitHub Actions)
      - test-coverage (>80%)
      - security-scan
      - code-quality

☑ Require conversation resolution before merging

☑ Restrict who can push to matching branches
   - Allow pushes from: Only administrators
   
☑ Require signed commits

☑ Include administrators
   (Admin tidak bisa bypass rules)

☑ Allow force pushes
   - Allow force pushes from: Nobody
   
☑ Allow deletions
   - (Disable deletion)

☐ Lock branch (Only if specific reason)
```

**Alur dengan Protection Rules untuk `main`:**

```
Developer                GitHub (main branch)
    │
    ├─ Create feature branch
    │    feature/feature-x
    │
    ├─ Make commits
    │    "feat: add feature"
    │
    ├─ Push to remote
    └─→ Cannot push directly to main!
         MUST create Pull Request

    └─→ Create Pull Request
         Title: [FEATURE] Feature X
         Description: Detailed explanation
         
         PR Status Checks:
         ✓ All conversations resolved
         ✓ 2 approvals minimum
         ✓ Code Owners approved
         ✓ Build passed
         ✓ Tests passed (80%+ coverage)
         ✓ Security scan passed
         ✓ Code quality OK
         
         └─→ Merge to main (only if all checks PASS)
              
              main updated! 🎉
              Deploy to production
```

#### **Konfigurasi untuk Branch `develop` (Staging/Integration)**

```
Rule applied to: develop

☑ Require a pull request before merging
   ☑ Require approval from reviewers (Minimum: 1)
   ☑ Dismiss stale pull request approvals when new commits are pushed
   
☑ Require status checks to pass before merging
   ☑ Require branches to be up to date before merging
   ☑ Require passing builds/tests:
      - build
      - test
      - code-quality

☑ Require conversation resolution before merging

☑ Include administrators
   (Let admins force merge if necessary for critical hotfix)

☑ Allow force pushes
   - Allow force pushes from: Administrators only
   
☑ Allow deletions
   - Allow deletions from: Nobody
```

#### **Konfigurasi untuk Branch Pattern `release/*`**

```
Rule applied to: release/*

☑ Require a pull request before merging
   ☑ Require approval from reviewers (Minimum: 1)
   
☑ Require status checks to pass before merging
   ☑ Require branches to be up to date before merging

☑ Require conversation resolution before merging

☐ Include administrators
   (Release managers bisa bypass jika perlu)
```

#### **Konfigurasi untuk Branch Pattern `hotfix/*`**

```
Rule applied to: hotfix/*

☑ Require a pull request before merging
   ☑ Require approval from reviewers (Minimum: 1)
   
☑ Require status checks to pass before merging
   ☑ Require branches to be up to date before merging

☑ Include administrators
   (Untuk hotfix critical, admin bisa bypass)
```

#### **Manfaat Branch Protection Rules**

| Manfaat | Deskripsi |
|---------|-----------|
| **Kualitas Kode** | Semua code harus pass testing dan review sebelum merge |
| **Security** | Mencegah unauthorized push langsung ke main |
| **Traceability** | Semua perubahan tercatat melalui PR dengan diskusi |
| **Knowledge Sharing** | Code review memastikan team knowledge spread |
| **Stabilitas Production** | Main branch selalu dalam state yang siap deploy |
| **Compliance** | Audit trail untuk kebutuhan regulatory/compliance |

### 3.2 Pull Request (PR) - Workflow Profesional

**Fungsi:** Channel resmi untuk integrate perubahan kode dengan review, testing, dan diskusi.

#### **3.2.1 Struktur Pull Request yang Baik**

```markdown
# Title: [TYPE] Brief description
# Contoh: [FEATURE] Add shopping cart checkout functionality

## Description
Jelaskan fitur/fix yang dibuat secara detail:
- Apa yang diimplementasikan?
- Mengapa perlu diimplementasikan?
- Bagaimana cara kerjanya?

## Motivation and Context
Sebutkan issue yang di-close:
Closes #123, Related to #45

## Type of Change
- [x] New feature (non-breaking change which adds functionality)
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] Breaking change (fix or feature that would cause existing functionality to change)
- [ ] Documentation update

## Testing
Jelaskan testing yang sudah dilakukan:
- [x] Unit tests passed
- [x] Integration tests passed
- [x] Manual testing on feature:
  - Scenario 1: User adds item to cart
  - Scenario 2: User applies discount code
  - Scenario 3: User proceeds to checkout

## Checklist
- [x] My code follows the style guidelines
- [x] I have performed a self-review of my own code
- [x] I have commented my code, particularly in hard-to-understand areas
- [x] I have made corresponding changes to the documentation
- [x] My changes generate no new warnings
- [x] I have added tests that prove my fix is effective or that my feature works
- [x] New and existing unit tests passed locally with my changes
- [x] Any dependent changes have been merged and published

## Screenshots (if applicable)
[Tambahkan screenshot untuk UI changes]

## Breaking Changes
Jelaskan jika ada breaking changes:
- Breaking change: Changed API endpoint from /api/v1/cart to /api/v2/cart
```

#### **3.2.2 Proses Pull Request End-to-End**

```
┌─────────────────────────────────────────────────────────────────┐
│ TAHAP 1: DEVELOPER SIAP MEMBUAT PR                              │
└─────────────────────────────────────────────────────────────────┘

1. Finish feature development
   $ git checkout feature/checkout
   $ git add .
   $ git commit -m "feat(checkout): complete checkout flow"

2. Sync dengan develop terbaru
   $ git fetch origin
   $ git rebase origin/develop

3. Resolve conflicts jika ada
   $ git add <resolved-files>
   $ git rebase --continue

4. Push ke remote
   $ git push origin feature/checkout

5. Buka GitHub dan click "Compare & Pull Request"
   atau "Create Pull Request" button

┌─────────────────────────────────────────────────────────────────┐
│ TAHAP 2: GITHUB WORKFLOW - AUTOMATED CHECKS                      │
└─────────────────────────────────────────────────────────────────┘

6. GitHub Actions trigger otomatis:
   ├─ Build Step
   │  ├─ Compile code
   │  ├─ Run linter
   │  └─ Check syntax
   │
   ├─ Test Step
   │  ├─ Run unit tests
   │  ├─ Run integration tests
   │  └─ Calculate coverage (target: 80%+)
   │
   ├─ Security Scan
   │  ├─ Dependency vulnerabilities
   │  ├─ Secret scanning
   │  └─ SAST (Static Application Security Testing)
   │
   └─ Code Quality Check
      ├─ SonarQube analysis
      ├─ Code duplication
      └─ Code smell detection

7. Status checks result:
   ✓ All checks PASSED → PR siap review
   ✗ Some checks FAILED → Developer fix issues

┌─────────────────────────────────────────────────────────────────┐
│ TAHAP 3: CODE REVIEW PROCESS                                     │
└─────────────────────────────────────────────────────────────────┘

8. Senior Developer / Tech Lead review:
   
   Review checklist:
   ☑ Functional correctness - Apakah logic benar?
   ☑ Code quality - Apakah code clean dan maintainable?
   ☑ Performance - Apakah efficient atau ada bottleneck?
   ☑ Security - Apakah ada vulnerability?
   ☑ Testing - Apakah test coverage adequate?
   ☑ Documentation - Apakah code well-documented?
   ☑ Consistency - Apakah sesuai code standards?

9. Review options:
   
   a) APPROVE
      Developer followed best practices
      Code quality good
      All tests passing
      
   b) REQUEST CHANGES
      Reviewer memberikan feedback
      Developer perlu fix issues tertentu
      
   c) COMMENT
      Reviewer hanya memberikan suggestion
      Tidak blocking merge

10. Developer responds to feedback:
    
    If REQUEST CHANGES:
    - Developer baca feedback
    - Developer edit code
    - Developer push changes baru
    - GitHub auto-update PR
    - Reviewer review lagi
    
    (Loop sampai approved)

┌─────────────────────────────────────────────────────────────────┐
│ TAHAP 4: FINAL CHECKS & MERGE                                    │
└─────────────────────────────────────────────────────────────────┘

11. Merge approval checklist:
    ☑ Minimum 2 approvals (for main) / 1 approval (for develop)
    ☑ All status checks PASSED
    ☑ No merge conflicts
    ☑ Conversations resolved
    ☑ Up-to-date with target branch

12. Merge options:
    
    a) Create a merge commit (Default untuk main)
       └─ Preserves full history
       
    b) Squash and merge (For feature branches ke develop)
       └─ Clean linear history
       
    c) Rebase and merge (For advanced users)
       └─ Linear history, original commits

13. Merge & Deploy:
    
    If merging to main:
    - GitHub auto-triggers deployment workflow
    - Deploy to staging first
    - Run smoke tests
    - Deploy to production
    
    If merging to develop:
    - Update integrate branch
    - Trigger nightly integration tests

14. Cleanup:
    - Delete remote branch
    - Close related issues
    - Notify stakeholders
```

#### **3.2.3 Contoh Skenario PR yang Kompleks**

**Skenario: Feature Checkout dengan Conflict & Review Feedback**

```
DEVELOPER A: Membuat feature/checkout
├─ Commit 1: feat(checkout): create checkout page
├─ Commit 2: feat(checkout): add payment form
├─ Commit 3: feat(checkout): integrate payment gateway
└─ Push ke remote

DEVELOPER B: Saat bersamaan working di feature/cart-discount
├─ Modify: CartService.java
└─ Merge dulu ke develop (sebelum Developer A selesai)

Developer A membuat PR:
├─ Title: "[FEATURE] Implement checkout flow with payment gateway"
├─ Description: Detailed explanation
├─ Linked issues: Closes #42, Relates to #10
└─ Self-review: "Verified locally, all tests passing"

GitHub Automated Checks:
├─ ✗ Merge conflict detected!
│  └─ CartService.java has conflict
│
├─ ✓ Build passed
├─ ✓ Tests passed (82% coverage)
├─ ✓ Security scan passed
└─ ✓ Code quality OK

Developer A resolves conflict:
$ git fetch origin
$ git rebase origin/develop
# Fix conflict di CartService.java
$ git add CartService.java
$ git rebase --continue
$ git push -f origin feature/checkout
(GitHub auto-update PR)

GitHub Checks again:
└─ ✓ All checks PASSED

Code Review oleh Tech Lead:
└─ Request Changes:
   - "Nice implementation! Few suggestions:"
   - "Line 45: Consider adding error handling for payment timeout"
   - "Line 67: This query might be slow, consider caching"
   - "Need test case for failed payment scenario"

Developer A addresses feedback:
├─ Add error handling untuk payment timeout
├─ Add caching untuk query
├─ Add test case untuk failed payment
└─ $ git commit -m "fix(checkout): address code review feedback"
   $ git push origin feature/checkout

Reviewer approves:
└─ "Looks good! Ready to merge"

Team Lead approves (2nd approval required for main):
└─ "Verified - approved for merge"

Merge to main:
├─ GitHub creates merge commit
│  └─ Commit message auto-generated
├─ GitHub deletes remote branch
├─ GitHub triggers deployment workflow
│
├─ Deployment workflow:
│  ├─ Build Docker image
│  ├─ Deploy to staging
│  ├─ Run smoke tests
│  ├─ Deploy to production
│  └─ Send notification: "Checkout feature deployed!"
│
└─ Close related issues:
   └─ Issue #42 closed automatically
```

#### **3.2.4 PR Template (CODEOWNERS)**

Buat file `.github/pull_request_template.md`:

```markdown
## 📋 Description
Provide a general description of the changes you made.

## 🎯 Type of Change
- [ ] 🆕 New feature (non-breaking change)
- [ ] 🐛 Bug fix (non-breaking change)
- [ ] 💥 Breaking change
- [ ] 📚 Documentation update

## 📝 Linked Issues
Closes #(issue number)
Related to #(issue number)

## ✅ Testing
Describe the tests that you ran and how to reproduce the test cases:
- [ ] Unit tests passed
- [ ] Integration tests passed
- [ ] Manual testing scenario:
  1. ...
  2. ...

## 📸 Screenshots (if applicable)
Add screenshots for UI changes

## 🔍 Checklist
- [ ] My code follows the style guidelines
- [ ] I have performed a self-review
- [ ] I have added necessary comments
- [ ] Tests are passing
- [ ] No console errors/warnings
- [ ] Documentation is updated
```

### 3.3 Code Review - Best Practices

**Fungsi:** Memastikan kualitas, knowledge sharing, dan consistency across codebase.

#### **3.3.1 Peran dalam Code Review**

```
┌────────────────────────────────────────────────────────────┐
│ AUTHOR (Developer yang membuat PR)                          │
└────────────────────────────────────────────────────────────┘

Tanggung jawab:
✓ Membuat PR yang jelas dengan deskripsi detail
✓ Self-review sebelum submit ke reviewer
✓ Respons feedback dengan cepat
✓ Tidak defensif saat menerima kritik
✓ Pastikan tests passing
✓ Maintain conversation respectfully
✓ Retest setelah fix feedback

Tidak boleh:
✗ Push perubahan besar tanpa explanation
✗ Abaikan reviewer feedback
✗ Force push setelah PR dibuat (tanpa coordinate)
✗ Merge tanpa minimum approvals
```

```
┌────────────────────────────────────────────────────────────┐
│ REVIEWER (Senior Developer / Tech Lead)                     │
└────────────────────────────────────────────────────────────┘

Tanggung jawab:
✓ Review dalam timeframe yang reasonable (24-48 jam)
✓ Check functional correctness
✓ Verifikasi code quality, performance, security
✓ Berikan constructive feedback
✓ Appreciate good code: "Nice approach!"
✓ Explain WHY not just WHAT
✓ Approve atau Request Changes, jangan Comment saja untuk blocker

Tidak boleh:
✗ Approve tanpa benar-benar review
✗ Terlalu pedantic tentang style (gunakan linter)
✗ Memberikan feedback yang tidak actionable
✗ Gatekeeping / blocking without reason
```

#### **3.3.2 Review Checklist Template**

```markdown
## Code Quality
- [ ] Code is readable and maintainable
- [ ] Naming conventions followed
- [ ] Duplicated code eliminated
- [ ] Functions have single responsibility
- [ ] Methods not too long (max 50 lines)

## Functionality
- [ ] Logic is correct for the use case
- [ ] Edge cases handled
- [ ] Error handling implemented
- [ ] No obvious bugs

## Performance
- [ ] No N+1 database queries
- [ ] Algorithmic complexity acceptable
- [ ] No memory leaks
- [ ] API response time acceptable

## Security
- [ ] No hardcoded credentials
- [ ] Input validation implemented
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (output encoding)
- [ ] CORS properly configured
- [ ] Authentication/Authorization checks

## Testing
- [ ] Unit tests cover business logic
- [ ] Integration tests for APIs
- [ ] Coverage meets minimum threshold (80%)
- [ ] Tests are meaningful, not just coverage

## Documentation
- [ ] Code comments explain WHY, not WHAT
- [ ] Public APIs documented
- [ ] Complex algorithms explained
- [ ] README updated if needed

## DevOps / Infrastructure
- [ ] Configuration properly managed
- [ ] Secrets handled securely
- [ ] Deployment strategy clear
- [ ] Rollback plan if needed
```

#### **3.3.3 Contoh Review Comment - Baik dan Buruk**

```markdown
❌ BAD REVIEW COMMENT:
"This is wrong"
"Change this variable name"
"Delete this comment"

✅ GOOD REVIEW COMMENT:
"I think there might be an issue here. 

If the user has admin role, they might be able 
to access protected resources. Consider checking 
authorization here:

```java
if (!user.hasRole("ADMIN")) {
    return unauthorized();
}
```

This prevents privilege escalation. What do you think?"

---

❌ BAD:
"This method is too long"

✅ GOOD:
"This method is doing multiple things: validating, 
processing, and caching. Consider extracting validation 
to a separate validateOrder() method. This will make 
testing easier and improve readability.

Here's an example:
```java
private void validateOrder(Order order) { ... }
```"

---

❌ BAD:
"Why did you use this variable name?"

✅ GOOD:
"The variable name 'x' doesn't convey meaning. 
Since this represents the user's current balance, 
consider renaming to 'currentBalance' for clarity."
```

#### **3.3.4 Metrics untuk Code Review Effectiveness**

```
Metrics to track:
1. Review Turnaround Time
   - Target: < 24 hours
   - Measure: Time from PR creation to first review

2. Approval Rate
   - Target: 80-90% approved without changes
   - Measure: PRs approved vs total PRs

3. Review Depth
   - Target: 3+ comments per PR
   - Measure: Average comments per PR

4. Defect Prevention
   - Target: Catch 70%+ of bugs before production
   - Measure: Bugs found in staging vs production

5. Knowledge Distribution
   - Target: Multiple reviewers per PR type
   - Measure: Different reviewers across PRs
```

### 3.4 Conventional Commits - Format Commit Messages

**Fungsi:** Standardize commit messages untuk clarity, automation, dan better history tracking.

#### **3.4.1 Struktur Conventional Commit**

```
{type}({scope}): {description}

{body}

{footer}
```

#### **3.4.2 Type - Jenis Commit**

| Type | Deskripsi | Contoh |
|------|-----------|--------|
| **feat** | Fitur baru | `feat(auth): add login functionality` |
| **fix** | Bug fix | `fix(cart): correct total calculation` |
| **docs** | Documentation | `docs(readme): update setup instructions` |
| **style** | Code style (format, missing semicolon) | `style(checkout): fix indentation` |
| **refactor** | Code refactoring (no logic change) | `refactor(service): extract helper method` |
| **perf** | Performance improvement | `perf(db): add index to users table` |
| **test** | Adding/updating tests | `test(auth): add login edge cases` |
| **chore** | Build, dependencies, tooling | `chore(deps): update Spring Boot to 3.0` |
| **ci** | CI/CD configuration | `ci: add GitHub Actions workflow` |
| **revert** | Revert previous commit | `revert: "feat(cart): add item"`  |

#### **3.4.3 Scope - Area Komit**

Scope mengidentifikasi area kode yang berubah:

```
feat(cart): ...          # Fitur untuk cart
feat(auth): ...          # Fitur untuk authentication
fix(payment): ...        # Fix di payment module
docs(api): ...           # Documentation untuk API
```

#### **3.4.4 Description - Deskripsi Singkat**

```
Aturan:
- Use imperative, present tense: "add" not "added"
- Don't capitalize first letter
- No period (.) at the end
- Max 50 characters

Contoh:
✓ "feat(cart): add item to cart"
✓ "fix(auth): resolve login timeout issue"
✗ "Added new feature to cart"          (past tense)
✗ "FEAT(CART): ADD ITEM TO CART"      (capitalized)
✗ "feat(cart): add item to cart."     (period)
```

#### **3.4.5 Body - Deskripsi Detail (Optional)**

```
Gunakan untuk:
- Explain WHAT and WHY, not HOW
- Reference issues
- Break the explanation into multiple lines
- Line length: max 72 characters

Contoh:
feat(checkout): implement order payment processing

This commit introduces the ability to process payments
for checkout orders. The implementation integrates with
the Stripe payment gateway API.

This enables:
- One-click checkout
- Support for multiple payment methods
- Order confirmation emails

Previously, users couldn't complete purchase without
manual intervention from admin.
```

#### **3.4.6 Footer - Metadata (Optional)**

```
Gunakan untuk:
- Reference issues: Closes #123, Fixes #456
- Co-authored commits: Co-authored-by: name <email>
- Breaking changes: BREAKING CHANGE: description

Contoh:
feat(api): add user profile endpoint

Add new GET /api/users/{id}/profile endpoint
that returns user profile information.

Closes #42
Related-to: #45
Co-authored-by: Jane Doe <jane@example.com>

BREAKING CHANGE: Old endpoint /api/user/me is deprecated
and will be removed in v2.0
```

#### **3.4.7 Contoh Conventional Commits**

```bash
# ═══════════════════════════════════════════════════════════════
# CONTOH 1: Simple Feature
# ═══════════════════════════════════════════════════════════════
git commit -m "feat(wishlist): add add-to-wishlist button"

# ═══════════════════════════════════════════════════════════════
# CONTOH 2: Bug Fix dengan detail
# ═══════════════════════════════════════════════════════════════
git commit -m "fix(auth): resolve session expiration error

The session expiration check was comparing timestamp
incorrectly, causing sessions to expire after 5 minutes
instead of 1 hour.

Changed comparison from getTime() > expirationTime
to getTime() >= expirationTime.

Closes #234"

# ═══════════════════════════════════════════════════════════════
# CONTOH 3: Breaking Change
# ═══════════════════════════════════════════════════════════════
git commit -m "refactor(api): restructure user endpoint

BREAKING CHANGE: /api/user endpoint is now /api/users/me
The response format has changed from:
  { id, name, email }
to:
  { user: { id, name, email, createdAt } }

Closes #567"

# ═══════════════════════════════════════════════════════════════
# CONTOH 4: Documentation
# ═══════════════════════════════════════════════════════════════
git commit -m "docs(setup): add Docker installation guide

Added comprehensive guide for Docker setup including:
- Docker installation steps
- Running with docker-compose
- Environment variables reference
- Troubleshooting section"

# ═══════════════════════════════════════════════════════════════
# CONTOH 5: Performance Optimization
# ═══════════════════════════════════════════════════════════════
git commit -m "perf(db): add index to products table

Added database index on 'category' and 'price' columns
to improve query performance for product filtering.

Benchmark results:
- Before: 450ms average query time
- After: 50ms average query time (~9x faster)

Closes #789"
```

#### **3.4.8 Manfaat Conventional Commits**

```
┌─────────────────────────────────────────────────────────┐
│ MANFAAT MENGGUNAKAN CONVENTIONAL COMMITS                │
└─────────────────────────────────────────────────────────┘

1. AUTOMATED CHANGELOG GENERATION
   Tool dapat otomatis generate changelog dari commits:
   
   ## Features
   - Add item to cart (#123)
   - Add user profile endpoint (#456)
   
   ## Bug Fixes
   - Fix session expiration error (#234)
   - Resolve payment timeout (#567)
   
   ## Breaking Changes
   - Restructure user endpoint

2. SEMANTIC VERSIONING
   Tool dapat auto-determine version based on commits:
   
   No commits    → 1.0.0
   feat commits  → 1.1.0 (Minor version bump)
   fix commits   → 1.0.1 (Patch version bump)
   BREAKING      → 2.0.0 (Major version bump)

3. BETTER GIT HISTORY
   $ git log --oneline
   
   a1b2c3d fix(auth): resolve session expiration error
   f4e5d6c feat(wishlist): add add-to-wishlist button
   c7b8a9d docs(readme): update installation guide
   
   Mudah dipahami sekilas apa yang berubah

4. AUTOMATED RELEASE NOTES
   Jira, GitHub Release notes dapat auto-generated
   dengan link ke commits

5. CI/CD AUTOMATION
   - Skip tests jika hanya docs yang berubah (chore/docs)
   - Auto-deploy jika feat/fix yang berubah
   - Trigger different workflows based on type

6. ISSUE LINKING
   Dengan "Closes #123" dalam footer, GitHub
   otomatis close issue saat PR di-merge

7. CODE BLAME & HISTORY
   Lebih mudah understand WHY sesuatu dibuat dengan
   membaca commit message yang descriptive
```

#### **3.4.9 Implementasi dengan Git Hooks**

Opsional: Enforce Conventional Commits dengan `commitlint`:

```bash
# ═══════════════════════════════════════════════════════════════
# SETUP COMMITLINT (Optional for automation)
# ═══════════════════════════════════════════════════════════════

# 1. Install dependencies
npm install --save-dev @commitlint/cli @commitlint/config-conventional husky

# 2. Setup husky
npx husky install

# 3. Add commitlint hook
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'

# 4. Config file: commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'perf', 'test', 'chore', 'ci', 'revert']
    ],
    'subject-case': [2, 'never', ['start-case', 'pascal-case', 'upper-case']]
  }
};

# Sekarang saat git commit dengan format salah akan ERROR:
$ git commit -m "Added new feature"
# Output: ⚠ Subject case must use lowercase
# Commit FAILED - harus fix message dulu
```

---

## 4. RINGKASAN WORKFLOW KOLABORASI PROFESIONAL

### 4.1 End-to-End Developer Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│ DEVELOPER WORKFLOW - HARI 1 KE HARI 5                           │
└─────────────────────────────────────────────────────────────────┘

DAY 1: Setup & Planning
├─ Clone repository
├─ Setup local environment
├─ Read issue/requirement
└─ Plan approach dengan team

DAY 2-3: Development
├─ $ git checkout -b feature/X develop
├─ Make code changes
├─ $ git commit -m "feat(X): ..."  (Conventional format)
├─ $ git push origin feature/X
├─ Run local tests
│  └─ Coverage target: 80%+
└─ Sync dengan develop jika ada update
   └─ $ git pull origin develop --rebase

DAY 4: Pull Request & Code Review
├─ Create PR di GitHub
│  ├─ Title: "[FEATURE] Meaningful description"
│  ├─ Description: Detailed explanation
│  └─ Linked issues: Closes #123
│
├─ GitHub Automated Checks run:
│  ├─ Build ✓
│  ├─ Tests ✓
│  ├─ Coverage (80%+) ✓
│  ├─ Security scan ✓
│  └─ Code quality ✓
│
├─ Tech Lead review code
│  └─ Request Changes / Approve
│
├─ Developer respond to feedback
│  ├─ Edit code
│  ├─ $ git commit -m "fix: address review feedback"
│  ├─ $ git push origin feature/X
│  └─ Reviewer review lagi
│
└─ Minimum 2 approvals obtained

DAY 5: Merge & Deploy
├─ Merge to main
│  └─ GitHub auto-triggers deployment
│
├─ Deployment pipeline:
│  ├─ Build Docker image
│  ├─ Deploy to staging
│  ├─ Run smoke tests
│  ├─ Deploy to production
│  └─ Notify team
│
├─ Cleanup:
│  ├─ Delete feature branch
│  ├─ Issue auto-closes
│  └─ Update milestone status
│
└─ Update docs if needed
   └─ $ git commit -m "docs: update readme"
```

### 4.2 Tabel Perbandingan: Before vs After SCM Implementation

```
ASPEK                    | TANPA SCM                  | DENGAN SCM
─────────────────────────────────────────────────────────────────
Merge Conflict           | Frequent, manual resolve   | Rare, systematic resolve
Code Quality             | Inconsistent               | Consistent standards
Review Process           | Ad-hoc, not structured     | Formal with checklist
Testing                  | Maybe tested               | Always tested (80%+)
Security                 | Vulnerabilities missed     | Scanned & caught
Deployment              | Manual, error-prone        | Automated CI/CD
Rollback                 | Complex, time-consuming    | Automated, 1 click
Traceability             | Hard to track changes      | Full audit trail
Documentation            | Often outdated             | Auto-generated, updated
Team Communication       | Informal                   | PR discussions tracked
Production Issues        | Frequent                   | Rare, caught in staging
```

### 4.3 Role & Responsibility Matrix

```
┌──────────────────────────────────────────────────────────────────┐
│ SIAPA MELAKUKAN APA DALAM SCM WORKFLOW                           │
└──────────────────────────────────────────────────────────────────┘

JUNIOR DEVELOPER
├─ ✓ Create feature branches
├─ ✓ Commit dengan Conventional format
├─ ✓ Create PR dengan detailed description
├─ ✓ Implement code review feedback
├─ ✓ Write unit tests
├─ ✓ Self-review code sebelum submit
└─ ✗ Don't: Merge ke main, Configure CI/CD, Set branch rules

SENIOR DEVELOPER / TECH LEAD
├─ ✓ Do: All junior developer tasks, plus:
├─ ✓ Review code (minimum 2 approvals for main)
├─ ✓ Approve/reject PRs
├─ ✓ Identify architectural issues
├─ ✓ Merge PRs to main
├─ ✓ Handle hotfix branches
├─ ✓ Mentor junior developers
└─ ✓ Setup branch protection rules

DEVOPS / INFRASTRUCTURE
├─ ✓ Configure CI/CD pipeline
├─ ✓ Setup GitHub Actions workflows
├─ ✓ Configure automated testing
├─ ✓ Setup staging & production environments
├─ ✓ Monitor deployments
├─ ✓ Configure security scanning
└─ ✓ Handle infrastructure-related issues

PROJECT MANAGER / SCRUM MASTER
├─ ✓ Create issues in GitHub
├─ ✓ Link issues to PRs
├─ ✓ Track PR progress
├─ ✓ Ensure team follows SCM process
├─ ✓ Generate reports from GitHub data
└─ ✓ Facilitate backlog refinement
```

### 4.4 Guidelines & Best Practices Summary

```
┌──────────────────────────────────────────────────────────────────┐
│ DO's ✓                                                            │
└──────────────────────────────────────────────────────────────────┘

✓ Keep commits small and focused (1 logical change per commit)
✓ Commit frequently (daily or more)
✓ Write descriptive commit messages in Conventional format
✓ Push code daily to avoid losing work
✓ Sync with main/develop branch regularly (daily)
✓ Use meaningful branch names (feature/payment, bugfix/timeout)
✓ Create PRs early, even if not complete (use draft PRs)
✓ Request review from multiple people
✓ Review others' code regularly (knowledge sharing)
✓ Test locally before pushing
✓ Keep tests updated with code changes
✓ Document complex logic in code comments
✓ Resolve conflicts locally, not in UI
✓ Delete merged branches
✓ Tag releases with semantic versioning

┌──────────────────────────────────────────────────────────────────┐
│ DON'Ts ✗                                                          │
└──────────────────────────────────────────────────────────────────┘

✗ Don't commit to main branch directly
✗ Don't merge without PR (except emergency hotfix)
✗ Don't ignore failing tests or CI checks
✗ Don't have merge conflicts lasting > 1 day
✗ Don't commit credentials/secrets/API keys
✗ Don't force push to shared branches (develop, main)
✗ Don't merge without minimum approvals
✗ Don't bypass branch protection rules
✗ Don't write vague commit messages ("fix bug", "update code")
✗ Don't mix unrelated changes in one commit
✗ Don't review your own PR only
✗ Don't leave PR comments unresolved
✗ Don't merge PRs with failing tests
✗ Don't delete develop or main branch
✗ Don't revert public commits without discussion
✗ Don't work directly on release/hotfix without synchronization
```

---

## 5. STUDI KASUS: PESENMAK DEVELOPMENT CYCLE

### 5.1 Skenario Realistis: Sprint 1 PesenMak

```
┌──────────────────────────────────────────────────────────────────┐
│ SPRINT 1: Implementasi Shopping Cart & Checkout                  │
└──────────────────────────────────────────────────────────────────┘

SPRINT PLANNING (Monday 09:00)
├─ Issue #10: Implement shopping cart UI
├─ Issue #11: Add cart persistence to database
├─ Issue #12: Implement checkout flow
└─ Issue #13: Integrate payment gateway

┌─ DAY 1-2 (Monday-Tuesday) ───────────────────────────────────────┐

DEVELOPER 1: Shopping Cart UI
├─ $ git checkout -b feature/cart-ui develop
├─ $ git commit -m "feat(ui): create shopping cart page component"
├─ $ git commit -m "feat(ui): add item quantity control"
├─ $ git commit -m "feat(ui): implement cart total calculation"
├─ $ git push origin feature/cart-ui
└─ Automated checks: ✓ Build ✓ Tests (75% coverage) ✗ Tests need more

   Developer 1 adds more tests:
   ├─ $ git commit -m "test(cart): add unit tests for calculations"
   ├─ $ git push origin feature/cart-ui
   └─ Automated checks: ✓ Build ✓ Tests (85% coverage) ✓ All pass

DEVELOPER 2: Cart Persistence (Database)
├─ $ git checkout -b feature/cart-persistence develop
├─ $ git commit -m "feat(db): create cart_items table migration"
├─ $ git commit -m "feat(service): implement CartItemService"
├─ $ git commit -m "feat(api): add save-cart API endpoint"
├─ $ git push origin feature/cart-persistence
└─ Automated checks: ✓ All pass

┌─ DAY 3 (Wednesday) ──────────────────────────────────────────────┐

PULL REQUESTS & CODE REVIEW

PR 1: Shopping Cart UI
├─ Created by: Developer 1
├─ Title: "[FEATURE] Implement shopping cart UI with calculations"
├─ Description: 
│  - Displays cart items
│  - Quantity control with +/- buttons
│  - Real-time total calculation
│  - Closes #10
├─
├─ Tech Lead (Sarah) Reviews:
│  ├─ Comment 1: "Nice work! Consider adding loading state 
│  │              while fetching cart from API"
│  ├─ Comment 2: "The calculateTotal function could be extracted
│  │              to a utility function for reusability"
│  └─ Request Changes
│
├─ Developer 1 responds:
│  ├─ $ git commit -m "feat(ui): add loading state to cart"
│  ├─ $ git commit -m "refactor(utils): extract total calculation"
│  ├─ $ git push origin feature/cart-ui
│  └─ Sarah re-reviews
│
└─ Sarah Approves (1 approval)

PR 2: Cart Persistence
├─ Created by: Developer 2
├─ Description: Add database backend for cart
├─ Review by Senior Dev (John)
│  ├─ Comment: "Good implementation! Consider adding error 
│  │            handling for database failures"
│  └─ Approves (with suggestion)
└─ Review by Tech Lead (Sarah)
   └─ Approves (2nd approval)

┌─ DAY 4 (Thursday) ──────────────────────────────────────────────┐

MERGES & INTEGRATION TESTING

Developer 1 merges Cart UI to develop:
├─ All 2 approvals obtained
├─ All automated checks PASSED
├─ GitHub squash-merges to develop
└─ Remote branch deleted

Developer 2 merges Cart Persistence to develop:
├─ All 2 approvals obtained
├─ All automated checks PASSED
├─ GitHub squash-merges to develop
└─ Remote branch deleted

Integration Testing in develop:
├─ Cart UI displays items correctly
├─ Quantities can be updated
├─ Cart persists to database
└─ Total calculations accurate

DEVELOPER 3: Checkout Flow
├─ $ git checkout -b feature/checkout develop
├─ $ git pull origin develop --rebase (Get latest cart changes)
├─ $ git commit -m "feat(checkout): create checkout page"
├─ $ git commit -m "feat(checkout): add order form"
├─ $ git push origin feature/checkout
└─ Builds on top of cart features from develop

┌─ DAY 5 (Friday) ────────────────────────────────────────────────┐

DEVELOPER 4: Payment Gateway
├─ Also created from develop
├─ $ git checkout -b feature/payment-gateway develop
├─ $ git commit -m "feat(payment): integrate Stripe API"
├─ $ git commit -m "feat(payment): add payment validation"
├─ $ git push origin feature/payment-gateway

SPRINT REVIEW / DEMO
├─ Checkout PR merged (if ready)
├─ Payment PR merged (if ready)
├─ Both features integration tested
├─ Ready for staging environment testing
├─ Create release/v0.1.0 branch from develop
└─ Prepare for production release next week

┌─ CONFLICTS SCENARIO ─────────────────────────────────────────────┐

WHAT IF THERE'S A MERGE CONFLICT?

Developer 3 (Checkout) and Developer 4 (Payment) both 
modify CartService.java for their features.

When merging:
├─ Developer 3's PR merges first (no conflict)
│  └─ develop updated with checkout code
│
├─ Developer 4 tries to merge Payment Gateway
│  └─ ✗ Merge conflict in CartService.java!
│
├─ Developer 4 resolves locally:
│  ├─ $ git fetch origin
│  ├─ $ git rebase origin/develop
│  │  └─ Conflict detected in CartService.java
│  ├─ # Manual edit: combine checkout + payment changes
│  ├─ $ git add CartService.java
│  ├─ $ git rebase --continue
│  └─ $ git push -f origin feature/payment-gateway
│
├─ PR auto-updates, now conflict shows resolved
├─ All automated checks pass again
└─ PR merges successfully

│
└─ LESSON: Regular syncing prevents conflicts!
```

---

## PENUTUP

Dengan menerapkan strategi SCM yang baik menggunakan Git dan GitHub, tim PesenMak mendapatkan:

✅ **Kualitas Kode Lebih Baik**
- Automated testing & scanning
- Mandatory code review
- Standards enforcement

✅ **Kolaborasi Tim Lebih Efisien**
- Clear workflow dan expectations
- Parallel development tanpa konflik
- Knowledge sharing melalui review

✅ **Manajemen Rilis Lebih Stabil**
- Branch protection mencegah kecelakaan
- Automated deployment reduce manual errors
- Easy rollback jika diperlukan

✅ **Traceability & Compliance**
- Setiap perubahan tercatat dengan detail
- Full audit trail untuk regulatory needs
- Issue linking untuk requirement tracing

✅ **DevOps Excellence**
- CI/CD automation reduce deployment time
- Early bug detection save cost
- Staging environment untuk testing sebelum production

**Implementasi SCM bukanlah hanya tentang tools, tapi tentang culture dan discipline tim dalam mengembangkan software secara profesional.**

---

**Document Version:** 1.0  
**Last Updated:** 2026-07-15  
**Author:** GitHub Copilot / Team Lead  
**Repository:** 411202061-max/Proyek
