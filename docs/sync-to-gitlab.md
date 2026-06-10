# Sync to GitLab — Tài liệu cấu hình

Workflow: `.github/workflows/sync-to-gitlab.yml`

---

## Mục đích

Tự động đồng bộ toàn bộ repo từ GitHub sang GitLab mỗi khi có push lên nhánh `main`, giúp GitLab profile luôn được cập nhật giống GitHub.

---

## Trigger — Khi nào workflow chạy

```yaml
on:
  push:
    branches: [ main ]   # chạy mỗi khi push lên main (kể cả commit từ workflow khác)
```

Vì trigger này bắt cả commit từ các workflow khác (metrics, pacman), nên mỗi khi SVG được generate và commit vào main → sync sang GitLab tự động.

---

## Step 1 — Checkout code

```yaml
- name: Checkout code
  uses: actions/checkout@v4
  with:
    fetch-depth: 0   # lấy toàn bộ lịch sử commit, không chỉ commit mới nhất
```

`fetch-depth: 0` quan trọng — nếu để mặc định (`1`) thì git push sang GitLab sẽ thiếu lịch sử và có thể bị lỗi.

---

## Step 2 — Push sang GitLab

```yaml
- name: Push to GitLab
  env:
    GITLAB_TOKEN: ${{ secrets.GITLAB_TOKEN }}
  run: |
    git push "https://oauth2:${GITLAB_TOKEN}@gitlab.com/LienThuan04/LienThuan04.git" --all --force
```

| Tham số | Ý nghĩa |
|---|---|
| `--all` | Push tất cả các nhánh (main, output, ...) |
| `--force` | Ghi đè lịch sử trên GitLab nếu bị lệch — GitLab luôn theo GitHub |
| `oauth2:${GITLAB_TOKEN}` | Xác thực bằng GitLab Personal Access Token |

---

## Secret cần thiết

| Secret | Lấy từ đâu | Scope cần |
|---|---|---|
| `GITLAB_TOKEN` | GitLab → Settings → Access Tokens | `write_repository` |

Cách tạo GitLab Token:
1. Vào `gitlab.com` → avatar → **Preferences**
2. **Access Tokens** → **Add new token**
3. Tick scope `write_repository`
4. Copy token → thêm vào GitHub repo secret với tên `GITLAB_TOKEN`

---

## Luồng hoạt động đầy đủ

```
Push lên GitHub main
  → sync-to-gitlab.yml chạy → push sang GitLab

Metrics workflow chạy (schedule/thủ công)
  → commit SVG vào GitHub main
  → trigger sync-to-gitlab.yml → SVG cũng lên GitLab

Pacman workflow chạy
  → push SVG vào nhánh output trên GitHub
  → trigger sync-to-gitlab.yml → nhánh output cũng lên GitLab
```
