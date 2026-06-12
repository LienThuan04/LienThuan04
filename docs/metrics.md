# lowlighter/metrics — Configuration Reference · Tài liệu cấu hình

Workflow: `.github/workflows/metrics.yml`

---

## Overview · Tổng quan

Two steps generate two separate SVG files committed directly into the repo.  
Hai step sinh ra hai file SVG riêng biệt, commit thẳng vào repo.

```yaml
# Step 1 — generates metrics-stats.svg (stats + languages)
base: header, activity, community, repositories
plugin_languages: yes

# Step 2 — generates metrics-activity.svg (calendar only)
base: ""               # empty = no base sections shown · để trống = không hiện base sections
plugin_isocalendar: yes
```

**Why is `base: ""` in Step 2? · Tại sao Step 2 có `base: ""`?**  
Without it, `lowlighter/metrics` auto-appends default sections (`header`, `activity`, ...) above the calendar — duplicating what Step 1 already shows. An empty `base` means the SVG contains only the explicitly enabled plugin.  
Nếu không để trống, `lowlighter/metrics` tự thêm các section mặc định lên trên calendar — trùng với Step 1. Để trống thì SVG chỉ chứa đúng plugin được bật.

---

## General config · Cấu hình chung

```yaml
token: ${{ secrets.LienThuan04_Profile_Readme_GitHub }}  # PAT to read GitHub data · đọc dữ liệu GitHub
filename: .github/assets/metrics-stats.svg               # output SVG path in repo · đường dẫn file output
config_timezone: Asia/Ho_Chi_Minh                        # timezone (Vietnam +7)
config_output: svg                                       # output format: svg | png | markdown | json
config_display: regular                                  # layout size: regular | large | columns
config_padding: 0, 8                                     # top/bottom padding in px or %
committer_token: ${{ secrets.GITHUB_TOKEN }}             # token to commit the file (defaults to token above)
                                                         # token để commit file (mặc định dùng token trên)
```

---

## `base` — Default sections · Sections mặc định

```yaml
base: header, activity, community, repositories   # show all (default) · bật tất cả
base: header, activity                            # show only 2 sections · chỉ bật 2 section
base: ""                                          # disable all · tắt tất cả (dùng khi chỉ muốn hiện plugin)
```

| Value · Giá trị | Shows · Hiển thị |
| --------------- | ---------------- |
| `header`        | Avatar, name, bio, account creation date · Avatar, tên, bio, ngày tạo tài khoản |
| `activity`      | Commits, Pull Requests, Issues, Code Reviews |
| `community`     | Followers, Following, Stars received, Sponsors · Followers, Following, Stars nhận được, Sponsors |
| `repositories`  | Total repos, forks, stars, primary language · Tổng repo, forks, stars, ngôn ngữ chính |
| `metadata`      | Generation time, metrics version · Thời gian generate, phiên bản metrics |

---

## `plugin_languages` — Programming languages · Ngôn ngữ lập trình

```yaml
plugin_languages: yes             # enable plugin · bật plugin (yes | no)
plugin_languages_limit: 8         # max languages to show (default: 8) · số ngôn ngữ tối đa
plugin_languages_threshold: 0%    # hide languages below this percentage · ẩn ngôn ngữ dưới ngưỡng này
                                  # e.g. 2% hides any language under 2%
plugin_languages_ignored: ""      # comma-separated languages to hide · ẩn ngôn ngữ cụ thể
                                  # MUST be lowercase · phải viết thường
                                  # e.g. html, css, php, dockerfile

plugin_languages_details: percentage             # show % only · chỉ hiện %
# plugin_languages_details: bytes-size           # show file size only · chỉ hiện dung lượng
# plugin_languages_details: lines                # show line count only · chỉ hiện số dòng
# plugin_languages_details: percentage, lines    # % + line count · % + số dòng
# plugin_languages_details: percentage, bytes-size  # % + file size · % + dung lượng

plugin_languages_sections: most-used            # most-used: by total usage · theo tổng lượng dùng
# plugin_languages_sections: recently-used      # recently-used: by recent activity · theo hoạt động gần đây
# plugin_languages_sections: most-used, recently-used  # both · cả hai

plugin_languages_categories: programming        # count only programming languages · chỉ đếm ngôn ngữ lập trình
# plugin_languages_categories: markup           # markup only (HTML, XML, ...) · chỉ đếm markup
# plugin_languages_categories: programming, markup, data, prose  # all types · tất cả loại

plugin_languages_indepth: no              # no: count by repo | yes: count per file (slower)
                                          # no: đếm theo repo | yes: đếm từng file (chậm hơn)
plugin_languages_analysis_timeout: 15    # timeout in seconds for indepth analysis · timeout (giây)
```

---

## `plugin_isocalendar` — 3D isometric contribution calendar · Lịch đóng góp dạng 3D

```yaml
plugin_isocalendar: yes                    # enable plugin · bật plugin (yes | no)
plugin_isocalendar_duration: full-year     # full-year: entire year | half-year: last 6 months
                                           # full-year: cả năm | half-year: 6 tháng gần nhất
```

---

## Other available plugins · Các plugin khác có thể thêm

### `plugin_calendar` — Flat contribution heatmap · Lịch đóng góp dạng phẳng

```yaml
plugin_calendar: yes
plugin_calendar_limit: 1     # number of years to show (0 = all) · số năm hiển thị (0 = tất cả)
```

> Different from `plugin_isocalendar`: 2D heatmap grid instead of 3D.  
> Khác với `plugin_isocalendar`: hiện dạng bảng 2D thay vì 3D.

---

### `plugin_habits` — Coding habits · Thói quen code

```yaml
plugin_habits: yes
plugin_habits_from: 200        # analyze from last N events · lấy từ N events gần nhất
plugin_habits_days: 14         # analyze last N days · phân tích trong N ngày gần nhất
plugin_habits_charts: yes      # show hourly activity chart · hiện biểu đồ giờ code trong ngày
plugin_habits_trim: yes        # hide inactive hours · ẩn các giờ không có activity
```

---

### `plugin_achievements` — Achievements · Thành tích

```yaml
plugin_achievements: yes
plugin_achievements_threshold: C         # show from rank C and above (S > A > B > C > X)
                                          # chỉ hiện từ hạng C trở lên
plugin_achievements_secrets: yes         # show hidden achievements · hiện thành tích ẩn
plugin_achievements_display: detailed    # detailed | compact
plugin_achievements_limit: 0            # 0 = show all · 0 = hiện tất cả
```

---

### `plugin_notable` — Notable contributions · Đóng góp nổi bật

```yaml
plugin_notable: yes
plugin_notable_from: organization        # organization | user | both
plugin_notable_repositories: yes         # show repo names · hiện tên repo
plugin_notable_indepth: no              # deeper analysis (requires extra token scope)
                                         # phân tích sâu hơn (cần thêm quyền token)
```

---

### `plugin_stars` — Recently starred repos · Repo được star gần đây

```yaml
plugin_stars: yes
plugin_stars_limit: 4     # number of repos to show · số repo hiển thị
```

---

### `plugin_followup` — Issues & PRs status · Tình trạng Issues & PRs

```yaml
plugin_followup: yes
plugin_followup_sections: repositories   # repositories | user
plugin_followup_indepth: no
```

---

### `plugin_traffic` — Repo view traffic · Lượt xem repo

```yaml
plugin_traffic: yes    # requires token scope: repo (not just public_repo)
                       # cần token có scope: repo (không chỉ public_repo)
```

---

## Example: adding plugins to Step 1 · Ví dụ thêm plugin vào Step 1

```yaml
- name: Stats & Languages
  uses: lowlighter/metrics@latest
  with:
    token: ${{ secrets.LienThuan04_Profile_Readme_GitHub }}
    filename: .github/assets/metrics-stats.svg
    base: header, activity, community, repositories
    plugin_languages: yes
    plugin_languages_limit: 8
    plugin_languages_details: percentage
    plugin_languages_threshold: 0%
    plugin_habits: yes                   # add habits · thêm habits
    plugin_habits_charts: yes
    plugin_achievements: yes             # add achievements · thêm achievements
    plugin_achievements_threshold: B
    config_timezone: Asia/Ho_Chi_Minh
```
