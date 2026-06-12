# Sync to GitLab — Configuration Reference · Tài liệu cấu hình

Workflow: `.github/workflows/sync-to-gitlab.yml`

---

## Purpose · Mục đích

Automatically mirrors the entire repo from GitHub to GitLab on every push to `main`, keeping the GitLab profile always in sync with GitHub.  
Tự động đồng bộ toàn bộ repo từ GitHub sang GitLab mỗi khi có push lên nhánh `main`, giúp GitLab profile luôn được cập nhật giống GitHub.

---

## Trigger — When the workflow runs · Khi nào workflow chạy

```yaml
on:
  push:
    branches: [ main ]   # runs on every push to main, including commits from other workflows
                         # chạy mỗi khi push lên main, kể cả commit từ workflow khác
```

Because this trigger catches commits from other workflows (metrics, pacman), every time an SVG is generated and committed to main → GitLab is synced automatically.  
Vì trigger này bắt cả commit từ các workflow khác (metrics, pacman), nên mỗi khi SVG được generate và commit vào main → sync sang GitLab tự động.

---

## Step 1 — Checkout code

```yaml
- name: Checkout code
  uses: actions/checkout@v4
  with:
    fetch-depth: 0   # fetch full commit history, not just the latest commit
                     # lấy toàn bộ lịch sử commit, không chỉ commit mới nhất
```

`fetch-depth: 0` is required. Without it, the git push to GitLab will be missing history and may error.  
`fetch-depth: 0` là bắt buộc. Nếu để mặc định (`1`) thì git push sang GitLab sẽ thiếu lịch sử và có thể bị lỗi.

---

## Step 2 — Push to GitLab · Push sang GitLab

```yaml
- name: Push to GitLab
  env:
    GITLAB_TOKEN: ${{ secrets.GITLAB_TOKEN }}
  run: |
    git push "https://oauth2:${GITLAB_TOKEN}@gitlab.com/LienThuan04/LienThuan04.git" --all --force
```

| Flag | Meaning · Ý nghĩa |
|---|---|
| `--all` | Push all branches (main, output, ...) · Push tất cả các nhánh |
| `--force` | Overwrite GitLab history if diverged — GitLab always follows GitHub · Ghi đè lịch sử trên GitLab nếu bị lệch |
| `oauth2:${GITLAB_TOKEN}` | Authenticate with GitLab Personal Access Token · Xác thực bằng GitLab PAT |

> **If you forked this repo · Nếu bạn fork repo này**: replace `gitlab.com/LienThuan04/LienThuan04.git` with `gitlab.com/your-username/your-username.git`.  
> Thay `LienThuan04/LienThuan04.git` thành `your-username/your-username.git`.

---

## Required secret · Secret cần thiết

| Secret | Where to get it · Lấy từ đâu | Scope needed · Scope cần |
|---|---|---|
| `GITLAB_TOKEN` | GitLab → Settings → Access Tokens | `write_repository` |

**How to create the GitLab token · Cách tạo GitLab Token:**

1. Go to · Vào: `gitlab.com` → **avatar** → **Preferences**
2. **Access Tokens** → **Add new token**
3. Tick scope · Tick scope: `write_repository`
4. Copy token → add to GitHub repo secrets as `GITLAB_TOKEN`  
   Copy token → thêm vào GitHub repo secrets với tên `GITLAB_TOKEN`

See `setup.md` for the full secret setup guide · Xem `setup.md` để biết hướng dẫn cài đặt đầy đủ.

---

## Troubleshooting · Xử lý lỗi

### `--force` rejected on protected branch · Force push bị từ chối do nhánh được bảo vệ

**Error · Lỗi:**
```
remote: GitLab: You are not allowed to force push code to a protected branch on this project.
! [remote rejected] main -> main (pre-receive hook declined)
```

**Cause · Nguyên nhân:**  
GitLab protects the `main` branch by default, blocking force pushes. Since this repo is a **mirror** (GitHub is the source of truth), force push must be allowed.  
GitLab mặc định bảo vệ nhánh `main`, chặn force push. Vì repo này chỉ là **mirror** (GitHub mới là nguồn chính), cần cho phép force push.

**Fix · Cách sửa:**

1. Go to · Vào: GitLab repo → **Settings** → **Repository**
2. Scroll to · Kéo xuống: **Protected branches**
3. Find `main` → click **Unprotect**  
   _(or enable **Allowed to force push** if you want to keep other protections)_  
   _(hoặc bật **Allowed to force push** nếu muốn giữ các bảo vệ khác)_
4. Save

> This is safe because no one commits directly to GitLab — all changes flow from GitHub.  
> An toàn vì không ai commit trực tiếp lên GitLab — mọi thay đổi đều đến từ GitHub.

---

## Full automation flow · Luồng hoạt động đầy đủ

```
Push to GitHub main
  → sync-to-gitlab.yml runs → pushed to GitLab

Metrics workflow runs (scheduled or manual)
  → commits SVG to GitHub main
  → triggers sync-to-gitlab.yml → SVG also lands on GitLab

Pacman workflow runs
  → pushes SVG to output branch on GitHub
  → triggers sync-to-gitlab.yml → output branch also on GitLab
```
