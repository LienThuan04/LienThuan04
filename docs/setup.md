# Setup Guide · Hướng dẫn cài đặt

How to fork and personalize this profile README for your own use.  
Hướng dẫn fork và tuỳ chỉnh profile README này cho cá nhân.

---

## 1. Fork the repo · Fork repo

1. Click **Fork** on this repository
2. Rename the new repo to match **your GitHub username** exactly  
   _(e.g. if your username is `john`, the repo must be named `john/john`)_

> **VI**: Đổi tên repo thành **đúng username GitHub của bạn** — GitHub chỉ nhận diện profile README khi tên repo trùng với username.

---

## 2. Required Tokens · Các token cần thiết

| Secret name | Used in | Required scopes |
|---|---|---|
| `LienThuan04_Profile_Readme_GitHub` | `metrics.yml` | `read:user`, `public_repo` |
| `GITLAB_TOKEN` | `sync-to-gitlab.yml` | `write_repository` |

> **VI**: Thay tên secret `LienThuan04_Profile_Readme_GitHub` thành bất kỳ tên nào bạn muốn, miễn là cập nhật lại trong file `metrics.yml`.

---

## 3. GitHub Personal Access Token (PAT)

Used by: `metrics.yml` → generates stats/language/calendar SVGs.  
Dùng trong: `metrics.yml` → generate SVG stats, ngôn ngữ, lịch đóng góp.

**Steps · Các bước:**

1. Go to · Vào: `github.com` → **avatar** → **Settings**
2. Scroll to · Kéo xuống: **Developer settings** → **Personal access tokens** → **Tokens (classic)**
3. Click **Generate new token (classic)**
4. Fill in · Điền:
   - **Note**: anything, e.g. `METRICS_TOKEN`
   - **Expiration**: `No expiration` _(or set a date · hoặc đặt ngày hết hạn)_
   - **Scopes**: tick ✅ `read:user` and `public_repo`
5. Click **Generate token** → **copy the token** _(shown only once · chỉ hiện 1 lần)_

**Add to repo · Thêm vào repo:**

1. Go to · Vào your repo → **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Fill in · Điền:
   - **Name**: `LienThuan04_Profile_Readme_GitHub` _(or your chosen name)_
   - **Secret**: paste the token
4. Click **Add secret**

---

## 4. GitLab Token _(optional · tuỳ chọn)_

Used by: `sync-to-gitlab.yml` → mirrors the entire repo to GitLab automatically.  
Dùng trong: `sync-to-gitlab.yml` → tự động đồng bộ repo sang GitLab.

_Skip this step if you don't use GitLab · Bỏ qua nếu không dùng GitLab._

**Steps · Các bước:**

1. Go to · Vào: `gitlab.com` → **avatar** → **Preferences** → **Access Tokens**
2. Click **Add new token**
3. Fill in · Điền:
   - **Token name**: anything, e.g. `GITHUB_SYNC`
   - **Expiration date**: optional
   - **Scopes**: tick ✅ `write_repository`
4. Click **Create personal access token** → **copy the token**

**Add to GitHub repo · Thêm vào GitHub repo:**

1. GitHub repo → **Settings** → **Secrets and variables** → **Actions**
2. **New repository secret**
3. **Name**: `GITLAB_TOKEN`, **Secret**: paste the token

---

## 5. Personalize content · Tuỳ chỉnh nội dung

### README.md

| What to change · Cần đổi | Where · Ở đâu |
|---|---|
| Your name | Line 15–18: `lines=Lien+Hoa+Thuan;LianHarman` |
| Your role/location | Line 25–28: `lines=IT+Student...` |
| Social links | Lines 34–80: `href` attributes |
| Spotify UID | Line 99: `uid=316jq374l35difgshqzgcymbjolu` |
| Discord ID | Line 104: replace `983724520789655613` with your Discord user ID |
| Discord card domain | Line 104: `lianharman-discord.vercel.app` → deploy your own instance (see §7) |

### Workflows

| File | What to change · Cần đổi |
|---|---|
| `metrics.yml` | Secret name if different from `LienThuan04_Profile_Readme_GitHub` |
| `sync-to-gitlab.yml` | GitLab repo URL: `gitlab.com/LienThuan04/LienThuan04.git` → your username |
| `pacman.yml` | Nothing — reads username automatically via `${{ github.repository_owner }}` |

---

## 6. Run workflows for the first time · Chạy workflow lần đầu

After setting up secrets, manually trigger each workflow once:  
Sau khi cài xong secrets, chạy thủ công từng workflow một lần:

1. Go to · Vào: your repo → **Actions** tab
2. Select · Chọn: **Metrics** → **Run workflow** → **Run workflow**
3. Wait ~1–2 minutes for SVGs to be generated and committed  
   Chờ ~1–2 phút để SVG được generate và commit vào repo
4. Repeat · Lặp lại: **Generate pacman animation** → **Run workflow**

After the first run, all workflows run automatically on schedule.  
Sau lần đầu, tất cả workflow tự chạy theo lịch.

---

## 7. Discord Presence Card (self-host) · Tự host Discord card

The Discord status card uses **Lanyard** to read your Discord presence and a self-hosted image generator on Vercel to render it.  
Card Discord dùng **Lanyard** để đọc presence và một image generator tự host trên Vercel để render.

### How it works · Cách hoạt động

```
Discord client → Lanyard bot (api.lanyard.rest) → Image generator (Vercel) → README.md
```

### Step 1 — Join Lanyard's Discord server

The Lanyard bot can only track you if you're a member of their server:  
**https://discord.gg/lanyard**

### Step 2 — Deploy the image generator to Vercel

1. Fork **https://github.com/kyrie25/lanyard-profile-readme**
2. Go to **https://vercel.com** → sign in with GitHub → **Add New Project** → import your fork
3. Leave all settings as-is → **Deploy**
4. Vercel gives you a URL like `https://your-app.vercel.app`

### Step 3 — Update README.md

Replace the domain in line 104:

```md
<!-- Replace lianharman-discord.vercel.app with your own Vercel URL -->
<img src="https://your-app.vercel.app/api/YOUR_DISCORD_ID?..." />
```

### Query params reference · Các tham số tùy chỉnh

| Param | Values | Description |
|---|---|---|
| `animatedDecoration` | `true/false` | Hiệu ứng decoration avatar |
| `showDisplayName` | `true/false` | Hiển thị display name Discord |
| `forceGradient` | `true/false` | Bật gradient background |
| `showBanner` | `animated/static/false` | Banner profile (animated = GIF nếu có) |
| `imgStyle` | `circle/square` | Shape avatar |
| `theme` | `dark/light` | Theme card |
| `ignoreAppId` | Discord app ID | Ẩn activity của app cụ thể |

**Why self-host · Lý do tự host:** avoids shared rate limits and third-party downtime; Vercel free tier is sufficient and the domain is not blocked on GitLab/Bitbucket.  
Tránh rate limit chung và downtime của bên thứ ba; Vercel free tier đủ dùng và domain không bị block trên GitLab/Bitbucket.
