# abozanona/pacman-contribution-graph — Configuration Reference · Tài liệu cấu hình

Workflow: `.github/workflows/pacman.yml`

---

## Purpose · Mục đích

Automatically generates a Pac-Man animation eating the GitHub contribution graph squares, saves it to the `output` branch, and displays it in the README.  
Tự động generate hoạt ảnh Pac-Man ăn các ô đóng góp (contribution graph) của GitHub, lưu vào nhánh `output` và hiển thị trong README.

---

## Trigger — When the workflow runs · Khi nào workflow chạy

```yaml
on:
  schedule:
    - cron: "* */12 * * *"   # every 12 hours · tự chạy mỗi 12 tiếng
  workflow_dispatch:          # manual trigger from Actions tab · chạy thủ công trên tab Actions
  push:
    branches:
    - main                    # on every push to main · mỗi khi push lên main
```

---

## Step 1 — Generate SVG

```yaml
- name: generate pacman-contribution-graph.svg
  uses: abozanona/pacman-contribution-graph@main
  with:
    github_user_name: ${{ github.repository_owner }}
```

| Parameter · Tham số | Value · Giá trị | Notes · Ghi chú |
| ------------------- | --------------- | --------------- |
| `github_user_name`  | `${{ github.repository_owner }}` | Auto-reads repo owner, no hardcoding needed · Tự lấy tên user từ repo, không cần hardcode |

Output is generated in the `dist/` folder with 2 files:  
Output sinh ra trong thư mục `dist/` gồm 2 file:

- `pacman-contribution-graph.svg` — light theme · theme sáng
- `pacman-contribution-graph-dark.svg` — dark theme · theme tối

---

## Step 2 — Push to `output` branch · Push sang nhánh `output`

```yaml
- name: push pacman-contribution-graph.svg to the output branch
  uses: crazy-max/ghaction-github-pages@v3.1.0
  with:
    target_branch: output    # push to output branch, not main · push vào nhánh output, không phải main
    build_dir: dist          # take content from dist/ folder · lấy nội dung từ thư mục dist/
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}   # auto token, no manual setup needed · token tự động, không cần tạo thủ công
```

---

## How README reads the files · Cách README đọc file

The SVG lives on the `output` branch. The README embeds it via `raw.githubusercontent.com`:  
SVG nằm trên nhánh `output`. README dùng `raw.githubusercontent.com` để nhúng:

```html
<picture>
  <source media="(prefers-color-scheme: dark)"
    srcset="https://raw.githubusercontent.com/LienThuan04/LienThuan04/output/pacman-contribution-graph-dark.svg" />
  <source media="(prefers-color-scheme: light)"
    srcset="https://raw.githubusercontent.com/LienThuan04/LienThuan04/output/pacman-contribution-graph.svg" />
  <img src="https://raw.githubusercontent.com/LienThuan04/LienThuan04/output/pacman-contribution-graph.svg" />
</picture>
```

> **If you forked this repo · Nếu bạn fork repo này**: replace `LienThuan04/LienThuan04` with `your-username/your-username` in both `srcset` URLs.  
> Thay `LienThuan04/LienThuan04` thành `your-username/your-username` trong cả 2 URL `srcset`.

---

## Comparison with metrics.yml · Khác với metrics.yml

| | `pacman.yml` | `metrics.yml` |
|---|---|---|
| Saves file to · Lưu file vào | `output` branch | `main` branch (`.github/assets/`) |
| Token used · Token dùng | `GITHUB_TOKEN` (automatic · tự động) | PAT created manually · PAT tự tạo |
| Frequency · Tần suất | Every 12 hours · Mỗi 12 tiếng | Daily at 2:00 AM UTC · Mỗi ngày 2:00 AM |
| README references · README trỏ đến | `raw.githubusercontent.com` URL | Local file path · Đường dẫn file local |
