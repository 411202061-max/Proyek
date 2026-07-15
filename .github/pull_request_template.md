## 📝 Description

Berikan deskripsi singkat tentang apa yang PR ini lakukan.

**Masalah yang diselesaikan:**
<!-- Jelaskan masalah yang ada sebelumnya -->

**Solusi yang diimplementasikan:**
<!-- Jelaskan bagaimana solusi mengatasi masalah -->

---

## 🎯 Type of Change

Hapus opsi yang tidak relevan:

- [ ] 🆕 New feature (non-breaking change which adds functionality)
- [ ] 🐛 Bug fix (non-breaking change which fixes an issue)
- [ ] 💥 Breaking change (fix or feature that would cause existing functionality to change)
- [ ] 📚 Documentation update
- [ ] 🔧 Configuration/Infrastructure change
- [ ] ♻️ Refactoring (no logic change)
- [ ] ⚡ Performance improvement
- [ ] 🧪 Test addition/update

---

## 🔗 Linked Issues

Hubungkan dengan issue yang relevan:

```
Closes #<issue-number>
Related to #<issue-number>
Fixes #<issue-number>
```

**Contoh:**
```
Closes #123
Related to #45
```

---

## ✅ Testing

Jelaskan testing yang sudah dilakukan:

### Unit Tests
- [ ] Unit tests added/updated
- [ ] All unit tests passing locally
- [ ] Coverage >= 80%

**Test cases:**
- [ ] Happy path scenario
- [ ] Edge cases
- [ ] Error handling

### Integration Tests
- [ ] Integration tests added/updated
- [ ] All integration tests passing
- [ ] Database interactions tested

**Test scenarios:**
1. Scenario 1: ...
2. Scenario 2: ...
3. Scenario 3: ...

### Manual Testing
- [ ] Tested locally on feature branch
- [ ] Tested on different browsers (if applicable)
- [ ] Tested with different data sets

**Manual test results:**
- [ ] Scenario A: PASS/FAIL
- [ ] Scenario B: PASS/FAIL
- [ ] Scenario C: PASS/FAIL

---

## 📸 Screenshots (if applicable)

Untuk UI changes, tambahkan screenshots:

### Before
<!-- Screenshot atau description sebelum perubahan -->

### After
<!-- Screenshot atau description sesudah perubahan -->

---

## 🔍 Code Quality

- [ ] Code follows the style guidelines of this project
- [ ] Code passes linter/formatting checks
- [ ] No new warnings introduced
- [ ] Comments added for complex logic
- [ ] Variable names are clear and descriptive

---

## 📖 Documentation

- [ ] README.md updated (if needed)
- [ ] API documentation updated (if needed)
- [ ] Code comments explain WHY, not WHAT
- [ ] CHANGELOG.md updated (if needed)
- [ ] Migration guide provided (if breaking change)

---

## 🔒 Security & Performance

- [ ] No hardcoded secrets/credentials
- [ ] No sensitive data exposed in logs
- [ ] Input validation implemented
- [ ] No SQL injection vulnerabilities
- [ ] No performance regressions
- [ ] N+1 query issues addressed

---

## 🚀 Deployment

### Deployment Strategy
- [ ] Safe for immediate production deployment
- [ ] Requires feature flag
- [ ] Requires database migration
- [ ] Requires configuration change
- [ ] Other: ...

### Breaking Changes
- [ ] No breaking changes
- [ ] Breaking changes documented below

**Breaking Changes Description:**
```
If this PR contains breaking changes, describe them:
- Old API: /api/v1/users
  New API: /api/v2/users
- Removed field: ...
- Changed field type: ...
```

### Migration Steps (if applicable)
```
1. Deploy this PR to staging
2. Run database migration: ...
3. Verify data integrity
4. Deploy to production
5. Monitor for errors
```

---

## 📋 Checklist

Pastikan semua ini sudah selesai sebelum submit PR:

- [ ] Branch dibuat dari `develop` (bukan dari branch lain)
- [ ] Branch name mengikuti convention: `feature/*`, `bugfix/*`, dll
- [ ] Code locally tested dan semua tests passing
- [ ] Local linter/formatter dijalankan
- [ ] Rebase dengan latest develop: `git pull origin develop --rebase`
- [ ] No merge conflicts
- [ ] Commit messages menggunakan Conventional Commits format
- [ ] Push ke remote: `git push origin feature/my-feature`
- [ ] PR description jelas dan detail
- [ ] Linked issues yang relevan
- [ ] Screenshot added (jika ada UI changes)
- [ ] Self-review completed
- [ ] Tidak ada debug code atau console.log
- [ ] No hardcoded passwords atau secrets
- [ ] Dependencies di-update (jika ditambah)
- [ ] Ready untuk code review ✓

---

## 💡 Additional Context

Tambahkan informasi tambahan yang mungkin berguna bagi reviewer:

### Design Decisions
```
Jelaskan mengapa Anda memilih approach ini:
- Alternatif yang dipertimbangkan: ...
- Alasan memilih solusi ini: ...
```

### Potential Issues
```
Masalah potensial yang mungkin timbul:
- Issue 1: ...
  Mitigation: ...
```

### Performance Impact
```
Dampak terhadap performa:
- Database queries: Sebelum X, sesudah Y
- API response time: Sebelum X ms, sesudah Y ms
- Memory usage: Sebelum X MB, sesudah Y MB
```

### Rollback Strategy
```
Jika ada masalah di production, cara rollback:
1. Revert commit ...
2. Run database rollback migration
3. Restart services
4. Verify system health
```

---

## 🎓 Knowledge Sharing

Informasi untuk tim:

### What I Learned
```
Hal yang dipelajari saat membuat PR ini:
- ...
- ...
```

### Recommended Reading
```
Resource untuk reviewer yang ingin understand lebih dalam:
- Link to documentation: ...
- Related PR: #...
- Research paper: ...
```

---

## 👥 Reviewers Notes

Catatan khusus untuk reviewer:

```
Hal yang perlu diperhatikan reviewer:
- Area kritis untuk di-review: ...
- Known limitations: ...
- Questions untuk reviewer:
  1. ...
  2. ...
```

---

## 📞 Contact

Jika ada pertanyaan:
- Slack: @username
- Email: your.email@pesenmak.com
- Schedule pair programming: [link to calendar]

---

**Thank you for reviewing this PR! 🙏**

---

## Review Checklist (untuk Reviewer)

> ℹ️ Bagian ini untuk reviewer, bukan untuk author

- [ ] PR description jelas dan lengkap
- [ ] Commit messages sesuai Conventional Commits
- [ ] Code logic correct dan solve stated problem
- [ ] Tests adequate dan passing
- [ ] Code coverage >= 80%
- [ ] No security vulnerabilities
- [ ] No performance issues
- [ ] Code quality acceptable
- [ ] Documentation updated
- [ ] No breaking changes (atau documented)
- [ ] Ready to merge

**Reviewer Name:** _______________  
**Review Date:** _______________  
**Approval Status:** ☐ Approved | ☐ Request Changes | ☐ Comment
