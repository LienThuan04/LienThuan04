# lowlighter/metrics — Tài liệu cấu hình

Workflow: `.github/workflows/metrics.yml`

---

## Tổng quan 2 step hiện tại

```yaml
# Step 1 — sinh ra metrics-stats.svg (stats + languages)
base: header, activity, community, repositories
plugin_languages: yes

# Step 2 — sinh ra metrics-activity.svg (chỉ calendar)
base: ""               # để trống = không hiện base sections
plugin_isocalendar: yes
```

**Tại sao Step 2 có `base: ""`?**
Nếu không để trống, `lowlighter/metrics` tự động thêm các section mặc định (`header`, `activity`, ...) vào trên calendar — bị trùng với Step 1. Để trống thì file SVG chỉ chứa đúng calendar, không có gì khác.

---

## Cấu hình chung

```yaml
token: ${{ secrets.LienThuan04_Profile_Readme_GitHub }}  # PAT để đọc dữ liệu GitHub
filename: .github/assets/metrics-stats.svg               # đường dẫn file SVG output trong repo
config_timezone: Asia/Ho_Chi_Minh                        # múi giờ Việt Nam (+7)
config_output: svg                                       # định dạng output: svg | png | markdown | json
config_display: regular                                  # kích thước layout: regular | large | columns
config_padding: 0, 8                                     # padding trên/dưới (px hoặc %)
committer_token: ${{ secrets.GITHUB_TOKEN }}             # token để commit file (mặc định dùng token trên)
```

---

## `base` — Sections mặc định

```yaml
base: header, activity, community, repositories   # bật tất cả (mặc định)
base: header, activity                            # chỉ bật 2 section
base: ""                                          # tắt tất cả (dùng khi chỉ muốn hiện plugin)
```

| Giá trị        | Hiển thị gì                                          |
| -------------- | ---------------------------------------------------- |
| `header`       | Avatar, tên, bio, ngày tạo tài khoản                 |
| `activity`     | Commits, Pull Requests, Issues, Code Reviews         |
| `community`    | Followers, Following, Stars nhận được, Sponsors      |
| `repositories` | Tổng repo, forks, stars, ngôn ngữ chính              |
| `metadata`     | Thời gian generate, phiên bản metrics                |

---

## `plugin_languages` — Ngôn ngữ lập trình

```yaml
plugin_languages: yes             # bật plugin (yes | no)
plugin_languages_limit: 8         # số ngôn ngữ tối đa hiển thị (mặc định: 8)
plugin_languages_threshold: 0%    # ẩn ngôn ngữ có tỉ lệ thấp hơn ngưỡng này (vd: 2%)
plugin_languages_ignored: ""      # danh sách ngôn ngữ muốn ẩn, cách nhau bởi dấu phẩy
                                  # vd: html, css, dockerfile

plugin_languages_details: percentage        # chỉ hiện %
# plugin_languages_details: bytes-size     # chỉ hiện dung lượng (KB, MB)
# plugin_languages_details: lines          # chỉ hiện số dòng code
# plugin_languages_details: percentage, lines        # % + số dòng
# plugin_languages_details: percentage, bytes-size   # % + dung lượng

plugin_languages_sections: most-used       # most-used: ngôn ngữ dùng nhiều nhất
# plugin_languages_sections: recently-used # recently-used: ngôn ngữ dùng gần đây nhất
# plugin_languages_sections: most-used, recently-used  # hiện cả 2

plugin_languages_categories: programming   # chỉ đếm ngôn ngữ lập trình
# plugin_languages_categories: markup      # chỉ đếm markup (HTML, XML, ...)
# plugin_languages_categories: programming, markup, data, prose  # tất cả loại

plugin_languages_indepth: no              # no: đếm theo repo | yes: đếm từng file (chậm hơn)
plugin_languages_analysis_timeout: 15     # timeout (giây) khi phân tích ngôn ngữ indepth
```

---

## `plugin_isocalendar` — Lịch đóng góp dạng 3D isometric

```yaml
plugin_isocalendar: yes                    # bật plugin (yes | no)
plugin_isocalendar_duration: full-year     # full-year: cả năm | half-year: 6 tháng gần nhất
```

---

## Các plugin khác có thể thêm vào

### `plugin_calendar` — Lịch đóng góp dạng phẳng (heatmap)

```yaml
plugin_calendar: yes
plugin_calendar_limit: 1     # số năm hiển thị (0 = tất cả, 1 = năm hiện tại)
```

> Khác với `plugin_isocalendar`: cái này hiện dạng bảng 2D thay vì 3D.

---

### `plugin_habits` — Thói quen code

```yaml
plugin_habits: yes
plugin_habits_from: 200        # lấy từ N events gần nhất để phân tích
plugin_habits_days: 14         # phân tích trong N ngày gần nhất
plugin_habits_charts: yes      # hiện biểu đồ giờ code trong ngày
plugin_habits_trim: yes        # ẩn các giờ không có activity
```

---

### `plugin_achievements` — Thành tích

```yaml
plugin_achievements: yes
plugin_achievements_threshold: C         # chỉ hiện từ hạng C trở lên (S > A > B > C > X)
plugin_achievements_secrets: yes         # hiện các thành tích ẩn
plugin_achievements_display: detailed    # detailed | compact
plugin_achievements_limit: 0            # 0 = hiện tất cả
```

---

### `plugin_notable` — Đóng góp nổi bật

```yaml
plugin_notable: yes
plugin_notable_from: organization        # organization | user | both
plugin_notable_repositories: yes         # hiện tên repo
plugin_notable_indepth: no              # phân tích sâu hơn (cần thêm quyền token)
```

---

### `plugin_stars` — Repo được star gần đây

```yaml
plugin_stars: yes
plugin_stars_limit: 4     # số repo hiển thị
```

---

### `plugin_followup` — Tình trạng Issues & PRs

```yaml
plugin_followup: yes
plugin_followup_sections: repositories   # repositories | user
plugin_followup_indepth: no
```

---

### `plugin_traffic` — Lượt xem repo

```yaml
plugin_traffic: yes    # cần token có scope: repo (không chỉ public_repo)
```

---

## Ví dụ: thêm plugin vào Step 1

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
    plugin_habits: yes                   # thêm habits
    plugin_habits_charts: yes
    plugin_achievements: yes             # thêm achievements
    plugin_achievements_threshold: B
    config_timezone: Asia/Ho_Chi_Minh
```
