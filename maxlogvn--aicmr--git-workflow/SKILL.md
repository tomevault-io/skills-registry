---
name: git-workflow
description: Skill quản lý Git workflow với hỗ trợ Conventional Commits. Tự động tạo commit message theo chuẩn, quản lý branch, merge, rebase, stash và các thao tác Git khác. Sử dụng khi cần commit code với message tự động, push/pull với remote, quản lý branch (create/switch/merge/rebase), xem trạng thái/history, stash changes, hoặc bất kỳ thao tác Git nào Use when this capability is needed.
metadata:
  author: maxlogvn
---

# Git Workflow Skill

## Nguyên tắc an toàn

**KHÔNG BAO GIỜ** thực hiện các thao tác destructive mà không xác nhận:
- `git reset --hard`
- `git rebase` (trên branch đã chia sẻ)
- `git push --force`
- `git clean -f`
- `branch -D` (force delete)

Luôn kiểm tra `git status` trước và sau các thao tác quan trọng.

## Format Conventional Commits

Tất cả commit messages phải tuân theo format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types (bắt buộc)

| Type | Mô tả |
|------|-------|
| `feat` | Tính năng mới |
| `fix` | Sửa bug |
| `docs` | Thay đổi tài liệu |
| `style` | Format code (không thay đổi logic) |
| `refactor` | Refactor code |
| `test` | Thêm/sửa tests |
| `chore` | Maintenance (deps, config, build) |
| `perf` | Cải thiện performance |
| `ci` | CI/CD changes |

### Scope (tùy chọn)

Phạm vi của thay đổi, ví dụ: `auth`, `api`, `ui`, `db`, `config`.

### Subject (bắt buộc)

- Viết thường, không kết thúc bằng dấu chấm
- Dùng imperative mood: "add" không phải "added" hay "adds"
- Tối đa 72 ký tự

### Body (tùy chọn)

- Giải thích "what" và "why", không phải "how"
- Mỗi dòng tối đa 100 ký tự

### Footer (tùy chọn)

- Breaking changes: `BREAKING CHANGE: <description>`
- References issues: `Closes #123`, `Refs #456`

### Ví dụ commit message

```
feat(auth): thêm cơ chế refresh token

- Tạo endpoint POST /auth/refresh
- Implement token rotation khi refresh
- Cập nhật authentication middleware

```

```
fix(api): sửa lỗi null pointer trong user service

Khởi tạo userMap tránh NullPointerException khi
user chưa được cache.
```

```
refactor(ui): tái cấu trúc component hierarchy

Tách Header component thành các sub-component
để dễ bảo trì và test.
```

## Workflow chi tiết

### 1. Quick Commit & Push

Khi người dùng muốn commit nhanh:

1. Chạy `git status` để xem thay đổi
2. Chạy `git diff --staged` (nếu có staged) và `git diff` (unstaged)
3. Phân tích thay đổi để sinh commit message:
   - Đọc diff để hiểu nội dung thay đổi
   - Xác định type (feat/fix/docs/refactor/etc.)
   - Xác định scope (nếu rõ ràng)
   - Viết subject ngắn gọn
   - Thêm body nếu cần thiết
4. Stage các file phù hợp (ưu tiên stage từng file cụ thể thay vì `.`)
5. Tạo commit với message
6. Chạy `git status` để xác nhận
7. Push nếu được yêu cầu

### 2. Commit có chọn lọc

Khi có nhiều file và chỉ muốn commit một phần:

1. Liệt kê các file đã thay đổi
2. Hỏi người dùng muốn commit những file nào
3. Chỉ stage các file được chọn
4. Tạo commit message phù hợp

### 3. Quản lý Branch

#### Tạo branch mới

```bash
git checkout -b <branch-name>
```

Đặt tên branch theo quy ước:
- Feature: `feat/feature-name`
- Bugfix: `fix/bug-description`
- Hotfix: `hotfix/critical-fix`
- Release: `release/version`

#### Switch branch

```bash
git switch <branch-name>  # hoặc git checkout
```

Kiểm tra status trước khi switch để tránh mất changes.

#### Merge branch

1. Checkout branch đích
2. Pull latest changes
3. Merge branch nguồn: `git merge <source-branch>`
4. Resolve conflicts nếu có
5. Commit merge

#### Rebase (chỉ cho branch local)

```bash
git rebase main  # hoặc origin/main
```

Chỉ rebase branch chưa push hoặc branch cá nhân.

### 4. Stash

Lưu tạm changes:

```bash
git stash push -m "message mô tả"
git stash list
git stash pop  # hoặc apply
```

### 5. Xem history

```bash
git log --oneline --graph --decorate -10
git reflog     # Khôi phục lost commits
```

### 6. Undo Changes

| Tình huống | Lệnh |
|------------|------|
| Unstage file | `git restore --staged <file>` |
| Discard unstaged | `git restore <file>` |
| Undo last commit | `git reset --soft HEAD~1` |
| Undo commit & changes | `git reset --hard HEAD~1` |

### 7. Remote Operations

```bash
git push -u origin <branch>     # Push mới
git push                         # Push existing
git pull --rebase                # Pull với rebase
git fetch --all                  # Fetch tất cả
```

## Quy trình sinh commit message tự động

Khi phân tích diff để sinh message:

1. **Xác định type**:
   - Thêm chức năng mới → `feat`
   - Sửa lỗi → `fix`
   - Chỉ đổi tài liệu → `docs`
   - Chỉ format/style → `style`
   - Tái cấu trúc → `refactor`
   - Thêm test → `test`
   - Config/deps → `chore`

2. **Xác định scope**:
   - Xem đường dẫn file thay đổi
   - auth/, user/ → `auth`
   - api/, controllers/ → `api`
   - ui/, components/ → `ui`
   - db/, models/ → `db`

3. **Viết subject**:
   - Tóm tắt ngắn gọn (< 72 ký tự)
   - Dùng tiếng Việt: "thêm", "sửa", "cập nhật", "xóa"

4. **Viết body** (nếu cần):
   - Liệt kê các thay đổi chính
   - Giải thích lý do (nếu không rõ ràng)

## Aliases hữu ích

Khuyến nghị thêm vào `.gitconfig`:

```ini
[alias]
  st = status
  co = checkout
  br = branch
  ci = commit
  unstage = reset HEAD --
  last = log -1 HEAD
  lg = log --graph --oneline --decorate --all
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxlogvn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
