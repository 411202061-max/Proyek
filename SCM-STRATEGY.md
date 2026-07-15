# Strategi Software Configuration Management (SCM) - PesenMak

## 1. STRATEGI BRANCHING GIT

### 1.1 Model Branching yang Dipilih: Git Flow + GitHub Flow Hybrid

Kami menggabungkan **GitFlow** untuk rilis produksi dan **GitHub Flow** untuk pengembangan fitur harian, yang cocok untuk e-commerce dengan siklus rilis teratur namun development cepat.

### 1.2 Struktur Branch

```
main (Production Ready)
  ├── hotfix/* (Perbaikan Critical untuk Production)
  └── release/* (Pre-release, Testing & Minor Fixes)

develop (Integration & Staging)
  ├── feature/* (Pengembangan Fitur Baru)
  ├── bugfix/* (Perbaikan Bug di Development)
  └── chore/* (Tugas Administrative)

support/* (Maintenance Branch - Optional)
```

### 1.3 Fungsi Setiap Branch

#### **Branch Utama**

| Branch | Fungsi | Kebijakan Merge | Siapa |
|--------|--------|-----------------|-------|
| **main** | Production-ready code, selalu deployable | Hanya dari `release/*` & `hotfix/*` | Tech Lead, DevOps |
| **develop** | Integration point untuk semua fitur | Dari `feature/*`, `bugfix/*`, `chore/*` | Tech Lead, Senior Dev |

#### **Branch Support**

| Branch | Fungsi | Kebijakan Merge | Siapa |
|--------|--------|-----------------|-------|
| **feature/\*** | Pengembangan fitur baru | → `develop` via PR | Developer |
| **bugfix/\*** | Perbaikan bug non-critical | → `develop` via PR | Developer |
| **hotfix/\*** | Perbaikan critical production | → `main` & `develop` | Senior Dev |
| **release/\*** | Persiapan rilis (testing, version bump) | → `main` & kembali ke `develop` | Release Manager |
| **chore/\*** | Dokumentasi, CI/CD, refactoring | → `develop` via PR | Developer |

### 1.4 Naming Convention

Gunakan format yang jelas dan konsisten:

```
{type}/{kebab-case-description}

Contoh:
feature/shopping-cart-checkout         # Fitur checkout keranjang
feature/user-authentication            # Fitur autentikasi user
bugfix/payment-gateway-timeout         # Fix timeout di payment
hotfix/security-sql-injection          # Perbaikan security critical
release/v1.2.0                          # Release version 1.2.0
chore/update-dependencies               # Update dependencies
```

### 1.5 Alur Pengembangan Lengkap

#### **Skenario A: Pengembangan Fitur Standar**

```
1. Developer membuat branch dari develop
   $ git checkout -b feature/new-feature develop

2. Developer membuat commits
   $ git commit -m "feat: add feature X"

3. Developer push ke remote
   $ git push -u origin feature/new-feature

4. Developer membuat Pull Request (PR)
   - Title: [FEATURE] New Feature X
   - Description: Jelaskan fitur, testing plan
   - Linked Issues: Hubungkan dengan issue tracker

5. Code Review Process
   - Minimum 1 approval dari Senior Developer
   - CI/CD harus PASS
   - Automated tests: 80%+ coverage

6. Merge dengan "Squash and Merge"
   $ git merge --squash feature/new-feature

7. Delete branch
   $ git push origin --delete feature/new-feature
   $ git branch -D feature/new-feature
```

#### **Skenario B: Persiapan Rilis (Release Flow)**

```
1. Buat release branch dari develop
   $ git checkout -b release/v1.2.0 develop

2. Lakukan version bump dan final testing
   $ git commit -m "chore: bump version to 1.2.0"

3. Fix bug kritis jika ditemukan
   $ git commit -m "fix: critical issue in release"

4. Merge ke main (dengan tag)
   $ git checkout main
   $ git merge --no-ff release/v1.2.0
   $ git tag -a v1.2.0 -m "Release version 1.2.0"

5. Merge kembali ke develop untuk synchronisasi
   $ git checkout develop
   $ git merge --no-ff release/v1.2.0

6. Delete release branch
   $ git branch -d release/v1.2.0
```

#### **Skenario C: Hotfix Production (Critical Bug)**

```
1. Buat hotfix branch dari main
   $ git checkout -b hotfix/security-patch main

2. Fix bug kritis dan test
   $ git commit -m "fix: patch security vulnerability"

3. Merge ke main dengan tag
   $ git checkout main
   $ git merge --no-ff hotfix/security-patch
   $ git tag -a v1.1.1 -m "Hotfix security patch"

4. Merge ke develop untuk sinkronisasi
   $ git checkout develop
   $ git merge --no-ff hotfix/security-patch

5. Delete hotfix branch
   $ git branch -d hotfix/security-patch
```

### 1.6 Diagram Alur Branching Git Flow

```
                    ┌─────────────────────────────────────┐
                    │        PRODUCTION (main)             │
                    │  Always Deployable, Stable           │
                    └─────────────────────────────────────┘
                           ↑             ↑
                           │             │
                    merge  │             │ merge
                    (v1.x) │             │ (hotfix)
                           │             │
        ┌──────────────────┐│┌────────────────────────┐
        │   Release/v1.x   ││    Hotfix/patch        │
        │ (Pre-release)    ││  (Critical Fix)        │
        └──────────────────┘└────────────────────────┘
                 ↑                        │
              merge                  merge │
                 │                        │
        ┌─────────────────────────────────────────────┐
        │      INTEGRATION (develop)                   │
        │  Staging, Testing, Integration Point        │
        └─────────────────────────────────────────────┘
             ↑          ↑         ↑          ↑
          merge      merge    merge      merge
             │          │       │          │
    ┌────────┴──┬───────┴──┬────┴──┬──────┴────┐
    │            │         │       │           │
feature/A   feature/B  bugfix/X chore/docs  feature/C
(Development Branch - Work in Progress)

Legend:
━━━━━━> Main development flow
─ ─ ─> Release/Hotfix flow
```

### 1.7 Status Branch dalam Development

```
┌─────────────────────────────────────────────────────┐
│   Timeline: Pengembangan PesenMak E-commerce       │
└─────────────────────────────────────────────────────┘

WEEK 1-2: Sprint Development
main (v1.0.0)      ──────────────────────────────────
develop            ───○─○─○─○─○─○─────────────────
feature/cart       ─○─○─○─X (merge)
feature/payment    ────────○─○─X (merge)

WEEK 3: Release Preparation
main (v1.0.0)      ──────────────────────────X─(merge)
develop            ───○─○─○─○─○─○─────────────○─X (sync)
release/v1.1.0     ────────────────────○─○─X (merge)

WEEK 4: Production Support
main (v1.1.0)      ──────────────────────X─────X─(hotfix)
develop            ───○─○─○─○─○─○─────────────○─X (sync)
hotfix/security    ────────────────────────────○─X (merge)
```

---

## 2. IMPLEMENTASI GIT - STEP BY STEP

### 2.1 Inisialisasi Repository

```bash
# 1. Clone repository yang sudah ada
git clone https://github.com/411202061-max/Proyek.git
cd Proyek

# 2. Konfigurasi user identity
git config user.name "Your Full Name"
git config user.email "your.email@pesenmak.com"

# 3. Lihat status awal
git status
```

### 2.2 Membuat dan Mengelola Branch

```bash
# ══════════════════════════════════════════════════════
# A. MEMBUAT BRANCH BARU
# ══════════════════════════════════════════════════════

# Membuat develop branch sebagai base
git checkout -b develop main
git push -u origin develop

# Membuat feature branch dari develop
git checkout -b feature/shopping-cart develop
git push -u origin feature/shopping-cart

# Melihat semua branch lokal
git branch

# Melihat semua branch (lokal + remote)
git branch -a

# Melihat branch yang sudah dihapus
git branch -D feature/old-feature

# ══════════════════════════════════════════════════════
# B. BERPINDAH BRANCH
# ══════════════════════════════════════════════════════

git checkout develop              # Berpindah ke develop
git checkout feature/shopping-cart # Berpindah ke feature

# Shortcut: buat dan langsung pindah (checkout -b)
git checkout -b feature/payment develop

# ══════════════════════════════════════════════════════
# C. SYNC BRANCH DENGAN REMOTE
# ══════════════════════════════════════════════════════

git fetch origin                    # Fetch latest dari remote
git pull origin develop             # Pull dan merge dari remote
git pull origin develop --rebase    # Pull dengan rebase (lebih clean)
```

### 2.3 Membuat Commit dengan Conventional Commits

```bash
# ══════════════════════════════════════════════════════
# CONVENTIONAL COMMIT FORMAT
# ══════════════════════════════════════════════════════
# {type}({scope}): {description}
#
# {body}
#
# {footer}
#
# Types: feat, fix, docs, style, refactor, test, chore, perf
# ══════════════════════════════════════════════════════

# 1. Membuat perubahan dan staging
echo "public class ShoppingCart { }" > src/ShoppingCart.java
git add src/ShoppingCart.java

# 2. Commit dengan message yang baik (tidak ada flag -m untuk multi-line)
git commit

# ─── Contoh Commit Messages ───
git commit -m "feat(cart): add add-to-cart functionality"
# Penjelasan: Fitur baru (feat) di modul cart (cart), menambahkan kemampuan

git commit -m "fix(payment): resolve payment gateway timeout issue"
# Penjelasan: Bug fix di payment, masalah timeout

git commit -m "docs(readme): update installation instructions"
# Penjelasan: Dokumentasi, update README

git commit -m "refactor(auth): simplify authentication logic"
# Penjelasan: Refactoring di auth, menyederhanakan logic

git commit -m "test(cart): add unit tests for CartService"
# Penjelasan: Testing, tambah unit tests

git commit -m "chore(deps): update Spring Boot to 3.0"
# Penjelasan: Maintenance, update dependencies

# 3. Commit dengan body dan footer (untuk PR linking)
git commit -m "feat(checkout): implement order placement

- Add OrderService class
- Integrate payment gateway
- Add order validation

Closes #123
Related to #45"

# 4. Lihat daftar commit
git log --oneline -10
git log --oneline --graph --all

# 5. Lihat detail commit tertentu
git show <commit-hash>
git show HEAD
```

### 2.4 Melakukan Push ke Remote

```bash
# ══════════════════════════════════════════════════════
# PUSH COMMITS KE REMOTE
# ══════════════════════════════════════════════════════

# Push current branch ke remote (dengan tracking)
git push -u origin feature/shopping-cart

# Setelah tracking, cukup
git push

# Push all branches
git push origin --all

# Push dengan tags
git push origin --tags

# Force push (GUNAKAN DENGAN HATI-HATI!)
git push -f origin feature/shopping-cart
# ⚠️ Hanya gunakan jika Anda yakin atau sudah koordinasi tim

# ══════════════════════════════════════════════════════
# STATUS LOKAL VS REMOTE
# ══════════════════════════════════════════════════════

git status                      # Status working directory
git log origin/develop..develop # Commits yang belum dipush
git diff origin/develop develop # Perubahan yang belum dipush
```

### 2.5 Merge Branch - Berbagai Skenario

#### **Skenario 1: Merge Standar dengan --no-ff (Preserves History)**

```bash
# Pindah ke branch target (develop)
git checkout develop

# Pull latest changes dari remote
git pull origin develop

# Merge feature branch
git merge --no-ff feature/shopping-cart

# Message akan auto-generated: "Merge branch 'feature/shopping-cart' into develop"

# Push ke remote
git push origin develop

# Hapus feature branch
git branch -d feature/shopping-cart
git push origin --delete feature/shopping-cart
```

**Output di Git Log:**
```
*   c1a2b3d Merge branch 'feature/shopping-cart' into develop
|\
| * f4e5d6c feat(cart): add checkout button
| * e3d4c5b feat(cart): add cart total calculation
|/
* a9b8c7d Initial commit
```

#### **Skenario 2: Squash Merge (Clean History untuk Feature Kecil)**

```bash
# Pindah ke develop
git checkout develop
git pull origin develop

# Squash merge - gabungkan semua commits feature jadi 1
git merge --squash feature/payment

# Commit hasil squash dengan message yang sesuai
git commit -m "feat(payment): add payment gateway integration

- Integrate Stripe API
- Add payment validation
- Add transaction logging"

# Push
git push origin develop

# Hapus feature branch
git branch -d feature/payment
git push origin --delete feature/payment
```

**Output di Git Log (lebih clean):**
```
* f9e8d7c feat(payment): add payment gateway integration
* a9b8c7d Initial commit
```

#### **Skenario 3: Rebase Merge (Linear History - Optional)**

```bash
# Untuk advanced users yang ingin linear history
git checkout feature/shipping
git rebase develop           # Rebase commits di atas develop

# Jika ada conflict, resolve dulu (lihat section 2.6)
git add <resolved-files>
git rebase --continue

# Pindah ke develop dan merge
git checkout develop
git pull origin develop
git merge feature/shipping  # Fast-forward merge (karena sudah rebase)

git push origin develop
git branch -d feature/shipping
```

### 2.6 Menampilkan Riwayat Commit (Git Log)

```bash
# ══════════════════════════════════════════════════════
# MELIHAT GIT HISTORY
# ══════════════════════════════════════════════════════

# 1. Log sederhana - one line per commit
git log --oneline

# 2. Log dengan branch visualization (REKOMENDASI)
git log --oneline --graph --all --decorate

# 3. Log dengan detail lengkap
git log

# 4. Log dengan stats (berapa files berubah)
git log --stat

# 5. Log dengan diff inline
git log -p

# 6. Log untuk file tertentu
git log -- src/ShoppingCart.java

# 7. Log dengan author tertentu
git log --author="Nama Developer"

# 8. Log dalam range waktu
git log --since="2 weeks ago"
git log --until="2 days ago"

# 9. Log dengan search commit message
git log --grep="cart"

# 10. Alias untuk viewing (tambahkan ke ~/.gitconfig)
git config --global alias.lg "log --oneline --graph --all --decorate --color"
git lg  # Gunakan alias

# ══════════════════════════════════════════════════════
# MEMBANDINGKAN COMMITS
# ══════════════════════════════════════════════════════

git diff main develop          # Diff 2 branch
git diff HEAD~2 HEAD           # Diff 2 commits terakhir
git diff feature/shopping-cart -- src/  # Diff file tertentu

# Lihat siapa yang membuat perubahan di baris tertentu
git blame src/CartService.java
```

### 2.7 Menyelesaikan Merge Conflict

#### **Skenario: Conflict di CartService.java**

```
Situasi: Developer A dan B sama-sama edit CartService.java di method calculateTotal()
Developer A di feature/cart-discount
Developer B di feature/cart-tax

Saat merge, Git tidak bisa otomatis merge → CONFLICT
```

#### **Step-by-Step Resolusi Conflict**

```bash
# ══════════════════════════════════════════════════════
# STEP 1: DETECT CONFLICT
# ══════════════════════════════════════════════════════

git checkout develop
git pull origin develop
git merge feature/cart-discount

# Output:
# CONFLICT (content): Merge conflict in src/CartService.java
# Automatic merge failed; fix conflicts and then commit the result.

# ══════════════════════════════════════════════════════
# STEP 2: LIHAT FILE YANG CONFLICT
# ══════════════════════════════════════════════════════

git status
# On branch develop
# You have unmerged paths.
#   (use "git add/rm <file>..." as appropriate to resolve)
#   (use "git merge --abort" to abort the merge)
# Unmerged paths:
#   both modified:   src/CartService.java

# ══════════════════════════════════════════════════════
# STEP 3: BUKA FILE DAN LIHAT CONFLICT MARKERS
# ══════════════════════════════════════════════════════

# Di CartService.java akan terlihat:

public class CartService {
    
    public double calculateTotal() {
<<<<<<< HEAD (develop branch)
        // Developer A: menghitung total dengan discount
        double subtotal = getSubtotal();
        double discount = subtotal * 0.1;  // 10% discount
        return subtotal - discount;
=======
        // Developer B: menghitung total dengan pajak
        double subtotal = getSubtotal();
        double tax = subtotal * 0.05;      // 5% tax
        return subtotal + tax;
>>>>>>> feature/cart-discount
    }
}

# Penjelasan marker:
# <<<<<<< HEAD           = Code di branch saat ini (develop)
# =======               = Separator
# >>>>>>> branch-name   = Code dari branch yang di-merge
```

#### **Step 4: Pilih Resolusi Strategy**

```bash
# ─────────────────────────────────────────────────────
# OPTION A: Kombinasi KEDUA perubahan (Rekomendasi)
# ─────────────────────────────────────────────────────

# Edit CartService.java menjadi:

public class CartService {
    
    public double calculateTotal() {
        double subtotal = getSubtotal();
        double discount = subtotal * 0.1;      // 10% discount
        double discountedAmount = subtotal - discount;
        double tax = discountedAmount * 0.05;  // 5% tax
        return discountedAmount + tax;
    }
}

# Hapus semua conflict markers (<<<, ===, >>>)

# ─────────────────────────────────────────────────────
# OPTION B: Gunakan HANYA code dari current branch
# ─────────────────────────────────────────────────────
git checkout --ours src/CartService.java

# ─────────────────────────────────────────────────────
# OPTION C: Gunakan HANYA code dari incoming branch
# ─────────────────────────────────────────────────────
git checkout --theirs src/CartService.java

# ─────────────────────────────────────────────────────
# OPTION D: Gunakan merge tool interaktif
# ─────────────────────────────────────────────────────
git mergetool  # Buka GUI tool (jika sudah konfigurasi)
```

#### **Step 5: Complete Merge**

```bash
# ══════════════════════════════════════════════════════
# LANGKAH SETELAH RESOLVE CONFLICT
# ══════════════════════════════════════════════════════

# 1. Stage resolved file
git add src/CartService.java

# 2. Commit merge
git commit -m "merge: combine discount and tax calculation from feature/cart-discount

- Integrate both discount logic (10%) and tax logic (5%)
- Discount applied before tax calculation
- Tested with manual scenarios"

# 3. Push ke remote
git push origin develop

# 4. Hapus feature branch
git branch -d feature/cart-discount
git push origin --delete feature/cart-discount

# ══════════════════════════════════════════════════════
# JIKA CONFLICT TIDAK BISA DIRESOLVE: ABORT MERGE
# ══════════════════════════════════════════════════════

git merge --abort  # Batalkan merge, kembali ke state sebelumnya
```

#### **Visualization: Merge Conflict dalam Git History**

```
Feature branch A (discount):
    * abc1234 feat(cart): add 10% discount logic
    * def5678 Previous commit
    
Feature branch B (tax):
    * ghi9012 feat(cart): add 5% tax calculation
    * def5678 Previous commit (diverged from here)

CONFLICT at line 15-20 di CartService.java calculateTotal()

Resolution:
    $ git merge feature/cart-discount (while on develop)
    
    Merge conflict detected in CartService.java
    
    Developer resolves by combining both changes:
    * Calculate subtotal
    * Apply discount (10%)
    * Apply tax on discounted amount (5%)
    
    $ git add CartService.java
    $ git commit -m "merge: combine discount and tax"
    
Result:
         * xyz3456 (HEAD -> develop) Merge branch 'feature/cart-discount' into develop
        /|
       / * abc1234 feat(cart): add 10% discount logic
      /  
    /   * ghi9012 feat(cart): add 5% tax calculation
    |  /
    * def5678 Previous commit
```

### 2.8 Tips Menghindari Merge Conflict

```bash
# ═══════════════════════════════════════════════════════════════
# BEST PRACTICES UNTUK MENGURANGI CONFLICT
# ═══════════════════════════════════════════════════════════════

# 1. SYNC BRANCH SECARA REGULAR
git fetch origin
git pull origin develop --rebase

# 2. COMMIT KECIL & FREQUENT (jangan 1 commit besar)
git commit -m "feat(cart): add item removal"
git commit -m "feat(cart): add quantity update"
git commit -m "feat(cart): add price calculation"
# Lebih baik daripada: 1 commit besar "feat(cart): implement full cart"

# 3. HINDARI MERGE MULTIPLE BRANCH KE FILE YANG SAMA
# Koordinasikan dengan tim

# 4. CODE REVIEW SEBELUM MERGE
# Lihat di section 3 (Pull Request & Code Review)

# 5. REBASE SEBELUM MERGE (untuk feature branches)
git fetch origin
git rebase origin/develop
# Ini akan sync dengan latest develop

# 6. GUNAKAN FEATURE FLAGS untuk perubahan besar
# Code terintegrasi tapi bisa di-disable
```

---

## 3. CONTOH PERINTAH GIT LENGKAP - WORKFLOW DEVELOPMENT

```bash
# ═══════════════════════════════════════════════════════════════
# DAY 1: DEVELOPER MULAI FITUR BARU
# ═══════════════════════════════════════════════════════════════

# 1. Update local repository
cd ~/projects/PesenMak
git fetch origin
git pull origin develop --rebase

# 2. Buat feature branch
git checkout -b feature/wishlist-feature develop

# 3. Buat file dan kerjakan fitur
cat > src/WishlistService.java << 'EOF'
public class WishlistService {
    public void addToWishlist(Product product) {
        // Implementation
    }
}
EOF

# 4. Commit perkembangan
git add src/WishlistService.java
git commit -m "feat(wishlist): create WishlistService class"

# 5. Lanjutkan development
cat > src/WishlistController.java << 'EOF'
@RestController
public class WishlistController {
    // Implementation
}
EOF

git add src/WishlistController.java
git commit -m "feat(wishlist): add WishlistController API endpoint"

# 6. Push ke remote
git push -u origin feature/wishlist-feature

# ═══════════════════════════════════════════════════════════════
# DAY 2-3: DEVELOPER LANJUTKAN & PUSH
# ═══════════════════════════════════════════════════════════════

# 7. Sync dengan latest dari develop (jika ada update)
git fetch origin
git pull origin develop --rebase

# Jika ada conflict dari rebase:
# - Edit file yang conflict
# - git add <files>
# - git rebase --continue

# 8. Lanjutkan development dan commit
git add .
git commit -m "feat(wishlist): add wishlist persistence to database"

# 9. Push
git push origin feature/wishlist-feature

# ═══════════════════════════════════════════════════════════════
# DAY 4: SIAP UNTUK PULL REQUEST
# ═══════════════════════════════════════════════════════════════

# 10. Lihat commits yang akan di-PR
git log develop..feature/wishlist-feature --oneline

# 11. Squash commits jika ingin cleaner history (optional)
git rebase -i develop

# 12. Final push
git push origin feature/wishlist-feature

# 13. Buat Pull Request di GitHub (lihat section 3)

# ═══════════════════════════════════════════════════════════════
# AFTER MERGE: CLEANUP
# ═══════════════════════════════════════════════════════════════

# 14. Delete local branch
git branch -d feature/wishlist-feature

# 15. Fetch latest
git fetch origin
git pull origin develop --rebase

# 16. Lanjut ke fitur berikutnya
git checkout -b feature/next-feature develop
```

### 2.9 Advanced Git Commands untuk Team

```bash
# ═══════════════════════════════════════════════════════════════
# STASHING: Simpan work-in-progress sementara
# ═══════════════════════════════════════════════════════════════

# Perlu switch branch tapi belum selesai kerjakan?
git stash                    # Simpan perubahan
git stash list               # Lihat stashes
git stash pop                # Restore stash terakhir
git stash pop stash@{1}      # Restore stash tertentu
git stash drop               # Hapus stash

# ═══════════════════════════════════════════════════════════════
# TAGGING: Tandai releases
# ═══════════════════════════════════════════════════════════════

git tag v1.0.0                          # Lightweight tag
git tag -a v1.0.0 -m "Release 1.0"    # Annotated tag
git push origin v1.0.0                 # Push tag
git push origin --tags                 # Push semua tags

# ═══════════════════════════════════════════════════════════════
# CHERRY-PICK: Copy commit ke branch lain
# ═══════════════════════════════════════════════════════════════

git cherry-pick <commit-hash>          # Copy 1 commit
git cherry-pick <hash1> <hash2> <hash3> # Copy multiple commits

# ═══════════════════════════════════════════════════════════════
# REFLOG: Recover deleted branches atau commits
# ═══════════════════════════════════════════════════════════════

git reflog                              # Lihat semua branch operations
git checkout @{-1}                      # Go back to previous branch
git reset --hard HEAD@{5}               # Undo last 5 operations

# ═══════════════════════════════════════════════════════════════
# BISECT: Find commit yang introduce bug
# ═══════════════════════════════════════════════════════════════

git bisect start
git bisect bad                          # Current commit is bad
git bisect good <commit-hash>          # Old commit is good
git bisect reset                        # Done bisecting
```

---

**END OF SECTION 2 - IMPLEMENTASI GIT**

---

