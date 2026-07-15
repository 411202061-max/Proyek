# Contributing to PesenMak

Terima kasih telah tertarik berkontribusi pada proyek PesenMak! 🎉

Dokumen ini menjelaskan proses dan standar yang kami gunakan untuk menjaga kualitas kode dan kolaborasi tim yang baik.

---

## 📋 Daftar Isi

1. [Code of Conduct](#code-of-conduct)
2. [Cara Memulai](#cara-memulai)
3. [Proses Development](#proses-development)
4. [Branching Strategy](#branching-strategy)
5. [Commit Guidelines](#commit-guidelines)
6. [Pull Request Process](#pull-request-process)
7. [Code Review Standards](#code-review-standards)
8. [Testing Requirements](#testing-requirements)
9. [Documentation](#documentation)
10. [Troubleshooting](#troubleshooting)

---

## Code of Conduct

### Kami Percaya Pada

✅ **Respect** - Hormati pendapat dan keahlian rekan kerja  
✅ **Collaboration** - Bekerja sama untuk mencapai tujuan bersama  
✅ **Quality** - Komitmen pada kualitas kode dan produk  
✅ **Learning** - Berbagi pengetahuan dan tumbuh bersama  
✅ **Professionalism** - Berkomunikasi dengan profesional dan konstruktif  

### Kami Tidak Toleransi

❌ Harassment, discrimination, atau bullying  
❌ Disrespectful communication  
❌ Deliberate sabotage atau negligence  
❌ Violation of intellectual property  

**Lapor masalah** kepada tech lead atau team manager segera.

---

## Cara Memulai

### 1. Setup Local Environment

```bash
# Clone repository
git clone https://github.com/411202061-max/Proyek.git
cd Proyek

# Setup git configuration
git config user.name "Your Full Name"
git config user.email "your.email@pesenmak.com"

# Install dependencies (tergantung tech stack)
# Untuk Java/Spring Boot:
mvn clean install

# Untuk Node.js:
npm install

# Untuk Python:
pip install -r requirements.txt
```

### 2. Verifikasi Setup

```bash
# Run tests untuk memastikan semua berjalan
mvn test              # Java
npm test             # Node.js
pytest               # Python

# Atau check build
mvn clean build
npm run build
```

### 3. Baca Dokumentasi

Pelajari dokumentasi SCM kami:
- 📖 **SCM-STRATEGY.md** - Strategi branching & Git workflow
- 📖 **GITHUB-COLLABORATION.md** - GitHub features & code review
- 📖 **IMPLEMENTATION-GUIDE.md** - Practical guides & diagrams
- 📖 **README-RINGKASAN.md** - Quick reference

---

## Proses Development

### Workflow Standar (5 Hari)

**DAY 1: Planning & Setup**
```
1. Pilih issue dari backlog
2. Diskusi approach dengan tech lead
3. Create feature branch
4. Setup local environment
```

**DAY 2-3: Active Development**
```
1. Implement feature/fix
2. Write unit tests (parallel dengan development)
3. Commit frequently dengan Conventional Commits
4. Push ke remote daily
5. Sync dengan develop branch
```

**DAY 4: Code Review Preparation**
```
1. Ensure all tests passing locally
2. Run linter & code quality checks
3. Final self-review
4. Push to remote
5. Create Pull Request
```

**DAY 5: Code Review & Merge**
```
1. Tech lead reviews code
2. Address feedback
3. Get minimum approvals
4. Merge to develop
5. Verify CI/CD completes
```

### Best Practices

✅ **Commit Frequently** - Setiap 1-2 jam, jangan tunggu end of day  
✅ **Push Daily** - Backup code Anda, prevent data loss  
✅ **Sync Regularly** - Pull from develop setiap hari untuk avoid conflicts  
✅ **Test Before PR** - Pastikan semua tests passing locally  
✅ **Small Changes** - Keep PR focused, easier to review  
✅ **Ask Questions** - Jika tidak mengerti, tanya tech lead  

---

## Branching Strategy

### Branch Types

```
main                    # Production-ready, always stable
├── release/vX.Y.Z     # Pre-release, testing phase
└── hotfix/vX.Y.Z      # Critical production fixes

develop                 # Integration point, staging
├── feature/task-name   # New features
├── bugfix/task-name    # Bug fixes
└── chore/task-name     # Maintenance, docs
```

### Naming Convention

```
{type}/{kebab-case-description}

Examples:
feature/shopping-cart-checkout
feature/payment-gateway-integration
bugfix/cart-calculation-error
bugfix/session-timeout-fix
hotfix/security-vulnerability
release/v1.2.0
chore/update-dependencies
```

### Creating Branch

```bash
# Update local develop
git fetch origin
git checkout develop
git pull origin develop --rebase

# Create feature branch FROM develop
git checkout -b feature/your-feature develop

# Push to remote
git push -u origin feature/your-feature

# Verify it appears on GitHub
# Go to https://github.com/411202061-max/Proyek/branches
```

### Merging Strategy

**Branch ke develop**: Squash and Merge (clean history)  
**Branch ke main**: Create a Merge Commit (preserve history)  
**Hotfix**: Merge to both main AND develop  

---

## Commit Guidelines

### Conventional Commits Format

```
{type}({scope}): {description}

{body}

{footer}
```

### Type - Jenis Commit

| Type | Deskripsi | SemVer Impact |
|------|-----------|---------------|
| **feat** | Feature baru | Minor |
| **fix** | Bug fix | Patch |
| **docs** | Documentation | None |
| **style** | Code style (format, semicolon) | None |
| **refactor** | Code refactoring (no logic change) | None |
| **perf** | Performance improvement | Patch |
| **test** | Adding/updating tests | None |
| **chore** | Build, deps, tooling | None |
| **ci** | CI/CD configuration | None |

### Commit Message Rules

```
✓ GOOD:
feat(auth): add two-factor authentication
fix(cart): resolve total calculation bug
docs(readme): update installation guide

✗ BAD:
Added new feature              (past tense, vague)
FEAT(AUTH): ADD TWO-FACTOR     (all caps, no scope)
fix cart stuff                 (too vague)
feat(auth): add two-factor.    (period at end)
```

### Writing Good Commit Messages

**Subject Line (First Line)**
- Imperative mood: "add" not "added"
- Don't capitalize first letter
- No period at the end
- Max 50 characters
- Describes WHAT changed, not WHY

```bash
git commit -m "feat(payment): add Stripe integration"
```

**Body (Optional, for complex changes)**
- Explain WHAT changed
- Explain WHY you changed it
- Explain consequences/implications
- Wrap at 72 characters
- Separated from subject by blank line

```bash
git commit -m "fix(auth): resolve session token expiration

The session token was expiring after 5 minutes instead of 1 hour
because the expiration time comparison was using strict equality.

Changed from:
  getTime() > expirationTime

To:
  getTime() >= expirationTime

This fixes the edge case where session expires exactly at
the comparison timestamp."
```

**Footer (Optional)**
- Reference issues: Closes #123, Fixes #456
- Co-authors: Co-authored-by: Name <email>
- Breaking changes: BREAKING CHANGE: description

```bash
git commit -m "feat(api): add new user endpoint

...body...

Closes #42
Related-to: #15
Co-authored-by: Jane Doe <jane@pesenmak.com>"
```

---

## Pull Request Process

### Before Creating PR

```bash
# 1. Ensure local changes are committed
git status
# Should show: "working tree clean"

# 2. Sync with latest develop
git fetch origin
git pull origin develop --rebase

# 3. Resolve conflicts if any
# Edit files, then:
git add <resolved-files>
git rebase --continue

# 4. Run tests locally
mvn test              # or npm test, pytest, etc.

# 5. Run linter
mvn checkstyle:check  # or eslint, black, etc.

# 6. Verify code coverage (should be 80%+)
# Check coverage report

# 7. Push to remote
git push origin feature/your-feature
```

### Creating PR on GitHub

1. Go to repository → Pull Requests → New Pull Request
2. Base: `develop` | Compare: `feature/your-feature`
3. Fill out PR template:

```markdown
## Description
Brief description of what this PR does.

Detailed explanation if needed:
- What problem does it solve?
- How does it solve it?
- Any side effects or trade-offs?

## Type of Change
- [x] New feature (non-breaking change)
- [ ] Bug fix (non-breaking change)
- [ ] Breaking change
- [ ] Documentation update

## Linked Issues
Closes #123
Related to #45

## Testing
Describe how you tested this:
- [x] Unit tests: 15/15 passed
- [x] Integration tests: 8/8 passed
- [x] Manual testing:
  - Scenario 1: ...
  - Scenario 2: ...

## Checklist
- [x] Code follows style guidelines
- [x] Self-review performed
- [x] Comments added for complex logic
- [x] Documentation updated
- [x] Tests added/updated
- [x] All tests passing
- [x] No new warnings introduced
```

### During Code Review

1. **Check notifications** - Reviews akan mention Anda
2. **Read feedback** - Tech lead akan give constructive feedback
3. **Respond to comments** - Reply inline atau dengan new commits
4. **Address changes** - Edit code berdasarkan feedback
5. **Push updates** - New commits akan auto-update PR
6. **Request re-review** - Click "Request review" setelah changes
7. **Repeat** - Sampai mendapat approval

### Merging PR

Ketika PR siap (2+ approvals, all checks pass):

1. Tech lead akan merge dengan "Squash and merge"
2. Merge commit message auto-generated
3. Remote branch auto-deleted
4. GitHub auto-closes related issues

After merge:

```bash
# Clean up locally
git checkout develop
git pull origin develop
git branch -D feature/your-feature
```

---

## Code Review Standards

### For Authors

✅ **Do:**
- Provide clear PR description
- Keep PR focused & not too large (max 400 lines changes)
- Respond to feedback constructively
- Ask questions if feedback unclear
- Push updates after feedback
- Test locally before pushing

❌ **Don't:**
- Merge own PR
- Push changes directly to develop/main
- Ignore reviewer feedback
- Be defensive about criticism
- Leave conversations unresolved
- Commit without testing

### For Reviewers

✅ **Do:**
- Review within 24 hours
- Check code logic and correctness
- Verify tests are adequate
- Look for security issues
- Provide constructive feedback
- Explain WHY, not just WHAT
- Approve with confidence

❌ **Don't:**
- Approve without reading
- Focus only on style (let linter handle that)
- Be too pedantic
- Gatekeep without reason
- Approve if tests failing
- Request changes for personal preferences

### Review Checklist

Before approving, ensure:

```
□ Functional correctness - Does it solve the problem?
□ Code quality - Is it clean, readable, maintainable?
□ Performance - Any N+1 queries or inefficient algorithms?
□ Security - No hardcoded secrets? Input validation? SQL injection safe?
□ Testing - Coverage adequate? Edge cases covered?
□ Documentation - Code comments explain complex logic?
□ Consistency - Follows project conventions & patterns?
□ No breaking changes - Or clearly documented if intentional?
```

---

## Testing Requirements

### Unit Tests

**Target Coverage:** 80%+

**Requirements:**
- Test business logic
- Test edge cases
- Test error handling
- Clear test names
- Independent tests (no dependencies)

```java
// Example
@Test
public void testCalculateDiscount_WithValidPercentage() {
    // Arrange
    double subtotal = 100.0;
    double discountPercent = 10.0;
    
    // Act
    double result = CartService.calculateDiscount(subtotal, discountPercent);
    
    // Assert
    assertEquals(10.0, result, 0.01);
}

@Test
public void testCalculateDiscount_WithNegativePercentage_ThrowsException() {
    assertThrows(IllegalArgumentException.class, () -> {
        CartService.calculateDiscount(100.0, -10.0);
    });
}
```

### Integration Tests

**Requirements:**
- Test API endpoints
- Test database interactions
- Test service integrations
- Use test database/containers

```java
@SpringBootTest
@AutoConfigureMockMvc
public class CartControllerTest {
    
    @Test
    public void testAddItemToCart() {
        mockMvc.perform(post("/api/cart/items")
            .contentType(MediaType.APPLICATION_JSON)
            .content("""{"productId": 123, "quantity": 2}"""))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.itemCount").value(2));
    }
}
```

### Running Tests

```bash
# Run all tests
mvn test              # Java/Maven
npm test             # Node.js
pytest               # Python

# Run specific test
mvn test -Dtest=CartServiceTest
npm test -- CartService
pytest tests/test_cart.py

# Run with coverage
mvn jacoco:report
npm run test:coverage
pytest --cov=src tests/
```

### CI/CD Test Pipeline

Our GitHub Actions workflow automatically:
- ✅ Compiles code
- ✅ Runs unit tests
- ✅ Runs integration tests
- ✅ Calculates code coverage
- ✅ Runs security scan
- ✅ Checks code quality

**PR cannot merge if:**
- ❌ Build fails
- ❌ Tests fail
- ❌ Coverage < 80%
- ❌ Security vulnerabilities found
- ❌ Code quality issues flagged

---

## Documentation

### Code Comments

**Comment When:**
- ✅ Complex business logic (WHY, not WHAT)
- ✅ Tricky algorithms
- ✅ Non-obvious parameter usage
- ✅ Important edge cases

**Don't Comment:**
- ❌ Obvious code: `i++; // increment i`
- ❌ Already clear from variable names
- ❌ Implementation details (that's what code is for)

```java
// Good: Explains WHY
// We batch write to database every 1000 records to optimize memory usage
if (records.size() % 1000 == 0) {
    database.flush();
}

// Bad: Explains WHAT (obvious from code)
// Increment i
i++;

// Good: Explains complex logic
/*
 * Use Levenshtein distance to find fuzzy matches.
 * This allows typos (e.g., "pesenmak" matches "pesenmack")
 * Threshold of 2 means max 2 character differences.
 */
List<Product> fuzzySearch(String query) { ... }
```

### README Updates

Update README.md if you:
- Add new feature
- Change setup instructions
- Add new dependencies
- Document configuration

### API Documentation

If adding/changing endpoints:

```java
/**
 * Add item to shopping cart
 *
 * @param cartId ID of the cart
 * @param productId ID of the product to add
 * @param quantity Number of items to add
 * @return Updated cart
 * @throws CartNotFoundException if cart doesn't exist
 * @throws InvalidQuantityException if quantity <= 0
 */
@PostMapping("/carts/{cartId}/items")
public Cart addItemToCart(
    @PathVariable Long cartId,
    @RequestParam Long productId,
    @RequestParam Integer quantity
) { ... }
```

### Changelog

For major changes, update CHANGELOG.md:

```markdown
## [1.2.0] - 2026-07-15

### Added
- Two-factor authentication support
- User profile endpoint

### Fixed
- Session timeout calculation error
- Cart total rounding issue

### Changed
- Payment API v1 deprecated, use v2

### Breaking Changes
- Removed old `/api/user/me` endpoint, use `/api/users/me`
```

---

## Troubleshooting

### Problem: "I accidentally committed to main"

```bash
# Undo commit but keep changes
git reset --soft HEAD~1
git status

# Create proper branch
git checkout -b feature/my-feature develop
git commit -m "feat: ..."
git push -u origin feature/my-feature
```

### Problem: "I have merge conflicts"

```bash
# Sync with develop
git fetch origin
git rebase origin/develop

# Conflicts will appear, edit files to resolve
# Then:
git add <resolved-files>
git rebase --continue
git push -f origin feature/my-feature
```

### Problem: "I need to undo a commit"

```bash
# If not pushed yet:
git reset --soft HEAD~1

# If already pushed:
git revert <commit-hash>
git push origin feature/my-feature
```

### Problem: "I forgot to update my branch"

```bash
git fetch origin
git pull origin develop --rebase
# Resolve conflicts if any
git push origin feature/my-feature
```

### Problem: "My PR shows conflicts on GitHub"

```bash
# Sync locally first
git fetch origin
git rebase origin/develop
# Fix conflicts
git add .
git commit -m "merge: resolve conflicts"
git push -f origin feature/my-feature
```

### Problem: "I pushed sensitive data"

⚠️ **Immediately notify tech lead!**

```bash
# DO NOT push again or revert yourself
# Tech lead will handle removal from history

# For future:
# Add to .gitignore:
echo "secrets.env" >> .gitignore
git add .gitignore
git commit -m "chore: ignore secrets"
```

---

## Getting Help

### Resources

📖 **Documentation:**
- SCM-STRATEGY.md - Git strategy & commands
- GITHUB-COLLABORATION.md - GitHub workflow
- IMPLEMENTATION-GUIDE.md - Practical guides

🎓 **Learning:**
- GitHub Skills: https://github.com/skills
- Git Documentation: https://git-scm.com/book
- Pro Git Book: Free online

💬 **Communication:**
- Slack/Teams: Ask team questions
- Tech Lead Office Hours: Weekly 1:1
- Code Review Comments: Use for technical discussion

### Escalation

1. Check documentation first
2. Ask in Slack/Teams
3. Pair program with senior dev
4. Schedule with tech lead
5. Team sync meeting

---

## Useful Commands

### Daily Workflow

```bash
# Start work
git checkout -b feature/task develop

# During work
git add .
git commit -m "feat: ..."
git push origin feature/task

# Sync with develop
git pull origin develop --rebase

# Create PR
# (Go to GitHub)

# After merge
git checkout develop
git pull origin develop
git branch -D feature/task
```

### Git Aliases (Optional)

Add to `~/.gitconfig`:

```bash
[alias]
    lg = log --oneline --graph --all --decorate
    st = status
    co = checkout
    cb = checkout -b
    p = push
    pl = pull
    plr = pull --rebase
```

---

## Questions?

- 📖 Read documentation in this repo
- 💬 Ask in team Slack/Teams channel
- 👥 Schedule time with tech lead
- 📧 Email: team@pesenmak.com

---

**Thank you for contributing to PesenMak! 🙏**

Together, we build a great product with excellent code quality.

---

**Last Updated:** 2026-07-15  
**Repository:** https://github.com/411202061-max/Proyek  
**Team:** PesenMak Development
