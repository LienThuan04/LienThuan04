# abozanona/pacman-contribution-graph — Tài liệu cấu hình

Workflow: `.github/workflows/pacman.yml`

---

## Mục đích

Tự động generate hoạt ảnh Pac-Man ăn các ô đóng góp (contribution graph) của GitHub, lưu vào nhánh `output` và hiển thị trong README.

---

## Trigger — Khi nào workflow chạy

```yaml
on:
  schedule:
    - cron: "* */12 * * *"   # tự chạy mỗi 12 tiếng
  workflow_dispatch:          # chạy thủ công trên tab Actions
  push:
    branches:
    - main                    # chạy mỗi khi push lên main
```

---

## Step 1 — Generate SVG

```yaml
- name: generate pacman-contribution-graph.svg
  uses: abozanona/pacman-contribution-graph@main
  with:
    github_user_name: ${{ github.repository_owner }}
```

| Tham số             | Giá trị                            | Ý nghĩa                        |
| ------------------- | ---------------------------------- | ------------------------------ |
| `github_user_name`  | `${{ github.repository_owner }}`   | Tự lấy tên user từ repo, không cần hardcode |

Output sinh ra: thư mục `dist/` chứa 2 file:
- `pacman-contribution-graph.svg` — theme sáng
- `pacman-contribution-graph-dark.svg` — theme tối

---

## Step 2 — Push sang nhánh `output`

```yaml
- name: push pacman-contribution-graph.svg to the output branch
  uses: crazy-max/ghaction-github-pages@v3.1.0
  with:
    target_branch: output    # push vào nhánh output (không phải main)
    build_dir: dist          # lấy nội dung từ thư mục dist/
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}   # token tự động, không cần tạo thủ công
```

---

## Cách README đọc file

SVG nằm trên nhánh `output`, README dùng `raw.githubusercontent.com` để nhúng:

```html
<picture>
  <source media="(prefers-color-scheme: dark)"
    srcset="https://raw.githubusercontent.com/LienThuan04/LienThuan04/output/pacman-contribution-graph-dark.svg" />
  <source media="(prefers-color-scheme: light)"
    srcset="https://raw.githubusercontent.com/LienThuan04/LienThuan04/output/pacman-contribution-graph.svg" />
  <img src="https://raw.githubusercontent.com/LienThuan04/LienThuan04/output/pacman-contribution-graph.svg" />
</picture>
```

---

## Khác với metrics.yml

| | `pacman.yml` | `metrics.yml` |
|---|---|---|
| Lưu file vào | Nhánh `output` | Nhánh `main` (`.github/assets/`) |
| Token dùng | `GITHUB_TOKEN` (tự động) | PAT tự tạo (`LienThuan04_Profile_Readme_GitHub`) |
| Tần suất | Mỗi 12 tiếng | Mỗi ngày lúc 2:00 AM |
