# lowlighter/metrics — Tài liệu cấu hình

Workflow: `.github/workflows/metrics.yml`

---

## Step 1 — Stats & Languages

### `base` — Chọn sections hiển thị

```yaml
base: header, activity, community, repositories
```

| Giá trị        | Hiển thị gì                              |
| -------------- | ---------------------------------------- |
| `header`       | Avatar, tên, bio, ngày tạo tài khoản     |
| `activity`     | Commits, Pull Requests, Issues, Reviews  |
| `community`    | Followers, Following, Stars, Sponsors    |
| `repositories` | Tổng số repo, forks, stars toàn bộ repo  |

Ví dụ chỉ giữ stats cơ bản:
```yaml
base: header, activity
```

---

### `plugin_languages` — Ngôn ngữ lập trình

#### Bật/tắt

```yaml
plugin_languages: yes   # bật
plugin_languages: no    # tắt (ẩn toàn bộ phần languages)
```

#### Giới hạn số lượng ngôn ngữ

```yaml
plugin_languages_limit: 8   # hiện tối đa 8 ngôn ngữ
plugin_languages_limit: 5   # hiện tối đa 5 ngôn ngữ
```

#### Lọc theo ngưỡng phần trăm

```yaml
plugin_languages_threshold: 0%   # hiện tất cả (kể cả < 1%)
plugin_languages_threshold: 2%   # ẩn ngôn ngữ chiếm < 2%
plugin_languages_threshold: 5%   # ẩn ngôn ngữ chiếm < 5%
```

#### Ẩn ngôn ngữ cụ thể

```yaml
plugin_languages_ignored: html, css              # ẩn HTML và CSS
plugin_languages_ignored: html, css, dockerfile  # ẩn nhiều hơn
```

#### Chi tiết hiển thị

```yaml
plugin_languages_details: percentage             # chỉ hiện %
plugin_languages_details: bytes-size             # chỉ hiện dung lượng (KB, MB)
plugin_languages_details: lines                  # chỉ hiện số dòng code
plugin_languages_details: percentage, lines      # hiện % + số dòng
plugin_languages_details: percentage, bytes-size # hiện % + dung lượng
```

---

## Step 2 — Activity Calendar

### `plugin_isocalendar` — Lịch đóng góp dạng 3D

#### Bật/tắt

```yaml
plugin_isocalendar: yes   # bật
plugin_isocalendar: no    # tắt
```

#### Khoảng thời gian hiển thị

```yaml
plugin_isocalendar_duration: full-year   # hiện cả năm (365 ngày)
plugin_isocalendar_duration: half-year   # hiện 6 tháng gần nhất
```

---

## Cấu hình chung (áp dụng cho cả 2 step)

```yaml
config_timezone: Asia/Ho_Chi_Minh   # timezone Việt Nam (múi giờ +7)
```
