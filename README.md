# 🤖 KBAgenT Studio

**AI-Powered News Video Automation Platform**

Generate complete YouTube news videos — script, voice, images, music, upload — all in your browser.

[![PHP](https://img.shields.io/badge/PHP-8.1%2B-blue.svg)](https://php.net)
[![MySQL](https://img.shields.io/badge/MySQL-8.0%2B-orange.svg)](https://mysql.com)
[![Version](https://img.shields.io/badge/version-2.0-green.svg)](https://github.com/yourname/kbagent)
[![Made in Bangladesh](https://img.shields.io/badge/Made%20in-Bangladesh%20%F0%9F%87%A7%F0%9F%87%A9-006a4e.svg)]()

</div>

---

## 📺 What is KBAgenT Studio?

KBAgenT Studio is a **self-hosted, browser-based AI video automation platform** built for content creators, news channels, and digital agencies. From a single prompt or RSS feed, it produces a fully-edited broadcast video ready for YouTube — no FFmpeg, no server-side video encoding, no monthly SaaS fee.

**Perfect for:**
- 📰 News channels (Bangla / English)
- 🎥 Faceless YouTube automation
- 📈 News agencies uploading daily
- 💼 Content agencies managing multiple clients
- 🎓 Educational channels

---

## ✨ Key Features

### 🧠 AI Script Generation
- **OpenRouter** (GPT-4o-mini, Llama 3.3, DeepSeek R1)
- **3 input modes**: RSS News / Free prompt / Custom script
- Auto-generated SEO: title, description, 18 tags, 5 hashtags
- Language-aware target word count (Bengali & English)
- Minimum 1-minute enforcement

### 🎙 Text-to-Speech
- **Pollinations.ai** (male voices: onyx, echo, fable)
- **Google Translate TTS** fallback (female voices)
- Speech-rate-tuned chunking (Bengali: 2.05 w/s, English: 2.45 w/s)
- Server-side audio cache (30-min TTL)
- Retry with exponential backoff

### 🖼 Visual Assets
- **Pollinations.ai** — AI-generated editorial images per segment
- **Pixabay** — Stock photos (large, HD)
- **Pixabay Videos** — Optional B-roll clips
- All proxied through PHP (no CORS taint)

### 🎬 In-Browser Rendering
- `Canvas.captureStream(30)` + `MediaRecorder` — 1080p/720p
- No FFmpeg, no server load, no cron
- Real-time avatar lip-sync driven by Web Audio analyser
- Dynamic lower-third, ticker, LIVE badge, clock
- Custom thumbnail generation

### 🎵 Background Music
- Synthesized ambient pad (Web Audio API)
- Auto fade-in/out with speech
- One-click toggle (🎵 Music: ON/OFF)

### 📤 YouTube Upload
- Resumable upload via Google API
- OAuth with refresh token
- Auto thumbnail upload
- Quota guard (6 uploads/day by default)
- Analytics sync (views, watch time, subs)

### 🤖 Autopilot
- Runs on dashboard heartbeat (60s)
- Language slots (BN + EN daily targets)
- Auto research → script → queue production
- Manual "Run agent" button

### 🗂 Script Management
- **Active Scripts** — ready for production
- **Resend Video Skipped** — archived (video already made)
- View / Send to Studio / Delete
- Custom script builder (paste your own)

### 🔒 Security
- AES-256-GCM encrypted API keys
- CSRF protection on all POST
- Session hardening (SameSite, HttpOnly)
- Rate limiting on TTS
- Cache auto-cleanup (30 min)

---

## 🚀 Quick Start

### Requirements
| Component | Minimum |
|-----------|---------|
| PHP | 8.1+ |
| MySQL | 8.0+ |
| cURL | Enabled |
| OpenSSL | Enabled |
| Hosting | Any shared host (InfinityFree works) |

### 1. Upload Files
Upload all files to your web root:
```
config.php
lib.php
api.php
index.php
studio.php
```

### 2. Create Database
In your hosting panel, create a MySQL database, then edit `config.php`:

```php
define('DB_HOST', 'sql.yourhost.com');
define('DB_NAME', 'your_db_name');
define('DB_USER', 'your_db_user');
define('DB_PASS', 'your_db_password');
```

### 3. Set Encryption Key
Generate a random 32-byte key:
```bash
openssl rand -base64 32
```

Add to `config.php`:
```php
define('APP_KEY', 'base64:YOUR_GENERATED_KEY_HERE');
```

### 4. Visit `index.php`
Tables are auto-created. Login with:
- **Username:** `◉⁠‿⁠◉`
- **Password:** `(⁠θ⁠‿⁠θ⁠)`

⚠️ **Change the password immediately from Settings.**

### 5. Add API Keys
Go to **API & Keys** and add:

| Service | Where to get | Free tier |
|---------|-------------|-----------|
| OpenRouter | [openrouter.ai/keys](https://openrouter.ai/keys) | ✅ Yes |
| Pixabay | [pixabay.com/api/docs](https://pixabay.com/api/docs/) | ✅ Yes |
| YouTube OAuth | [console.cloud.google.com](https://console.cloud.google.com) | ✅ Yes |

### 6. YouTube Setup
1. Create OAuth **Web Application** in Google Cloud Console
2. Enable **YouTube Data API v3** + **YouTube Analytics API**
3. Add redirect URI: `https://yourdomain.com/api.php?action=oauth_callback`
4. Paste Client ID + Secret in **API & Keys**
5. Click **Connect YouTube** in YouTube tab
6. Approve → back to dashboard

---

## 📖 How to Use

### Create Your First Video

1. **Newsroom** → Refresh Bangla/English news
2. Click **Generate script** on any news item → choose minutes
3. Go to **Scripts** → find your script → **Send to Studio**
4. In **Studio**:
   - Click **1 · Prepare audio** (30-60 sec)
   - Click **2 · Record** (keeps tab visible)
   - Click **3 · Upload** to YouTube
5. Done! ✅

### Prompt-Based Video
1. **Scripts** page → "✨ Generate from prompt" box
2. Type e.g. `আজকের আবহাওয়ার আপডেট, ৩ দিনের বৃষ্টির পূর্বাভাস`
3. Choose language + minutes → **Generate**
4. Send to Studio → render

### Custom Script
1. **Scripts** page → "📝 Custom script" box
2. Paste your own text (paragraphs become segments)
3. **Save custom script** → Send to Studio

---

## 🏗 Architecture

```
┌──────────────┐     ┌─────────────┐     ┌─────────────┐
│  index.php   │────▶│   api.php   │────▶│  OpenRouter │
│  (Dashboard) │     │ (Endpoints) │     │  (AI text)  │
└──────────────┘     └──────┬──────┘     └─────────────┘
                            │
┌──────────────┐            ▼            ┌─────────────┐
│  studio.php  │     ┌─────────────┐     │ Pollinations│
│  (Renderer)  │◀───▶│   lib.php   │◀───▶│  (TTS + IMG)│
└──────┬───────┘     │ (Core Logic)│     └─────────────┘
       │             └──────┬──────┘
       │                    ▼            ┌─────────────┐
       │             ┌─────────────┐     │   Pixabay   │
       └────────────▶│    MySQL    │◀───▶│  (IMG + VID)│
                     └─────────────┘     └─────────────┘
                            │
                            ▼
                     ┌─────────────┐
                     │  YouTube    │
                     │  Data API   │
                     └─────────────┘
```

**Client-side rendering** — video is captured entirely in the browser via `Canvas.captureStream()` + `MediaRecorder`. Server never touches video bytes until the YouTube upload.

---

## 📁 File Structure

```
kbagent/
├── config.php          # Database, keys, defaults (~80 lines)
├── lib.php             # Core: DB, AI, TTS, YouTube (~1400 lines)
├── api.php             # 40+ REST endpoints (~500 lines)
├── index.php           # Dashboard UI (~900 lines)
├── studio.php          # Video renderer (~1200 lines)
├── data/               # Auto-created (cache, logs)
│   ├── cache/          # TTS audio, images, videos (auto-clean 30min)
│   ├── .htaccess       # Deny direct access
│   └── php-error.log
└── README.md
```

---

## 🔧 Configuration Reference

| Setting | Default | Description |
|---------|---------|-------------|
| `openrouter_model` | `openai/gpt-4o-mini` | AI model for scripts |
| `tts_provider` | `pollinations` | `pollinations` / `google` |
| `tts_voice` | `onyx` | `onyx` / `echo` / `fable` / `alloy` / `nova` |
| `tts_fallback_google` | `1` | Fall back to Google if Pollinations fails |
| `image_source` | `both` | `both` / `pollinations` / `pixabay` |
| `use_pixabay_videos` | `0` | Include Pixabay video B-roll |
| `music_enabled` | `1` | Background music on/off |
| `music_volume` | `0.10` | Background music gain |
| `max_uploads_per_day` | `6` | YouTube quota guard |
| `min_video_seconds` | `60` | Minimum video length |
| `autopilot` | `0` | Auto-generation on/off |
| `bn_per_day` | `3` | Bangla video target |
| `en_per_day` | `3` | English video target |

---

## ⚠️ Important Notes

### YouTube Quota
YouTube Data API gives 10,000 units/day per project. **Each upload costs 1,600 units** → maximum ~6 uploads/day. To exceed this, request quota increase in Google Cloud Console.

### TTS Voice
- `pollinations` provider → **male voices** (onyx/echo/fable)
- `google` fallback → **female voices** (Google Bengali default)
- To guarantee male-only: set `tts_fallback_google = 0`

### Autopilot & Cron
- Shared hosting (InfinityFree, ByetHost) has **no cron**
- The agent runs while the **dashboard tab is open** (60-sec heartbeat)
- For 24/7: use external uptime service on `api.php?action=tick_external&token=...`

### Video Rendering
- Runs **entirely in the browser** — must keep the Studio tab visible
- Background-tab throttling can corrupt the video
- Chrome/Edge/Firefox recommended

### Cache
- All TTS audio, images, video clips cached for **30 minutes**
- Auto-cleanup on every API call
- Manual clean from **Logs & Health**

---

## 🐛 Troubleshooting

| Problem | Solution |
|---------|----------|
| **Female voice plays** | Pollinations failed → set `tts_fallback_google = 0` OR retry |
| **Audio overlaps** | Fixed in v2.0 — do not use older versions |
| **"redirect_uri_mismatch"** | Copy exact URI from API & Keys into Google Cloud |
| **No refresh token** | Remove app access in Google Account → reconnect |
| **OpenRouter 401** | Wrong key OR out of credits |
| **Pixabay 400** | Invalid API key |
| **Upload CORS error** | Google blocked browser upload — check browser console |
| **"Script under 1 min"** | Regenerate with longer target duration |
| **Video shows old script** | Reload Studio tab (`Load next` button) |

---

## 🎯 Roadmap

- [ ] Multi-avatar support (upload photo)
- [ ] Facebook / Instagram Reels export
- [ ] Direct TikTok upload
- [ ] Custom music library upload
- [ ] Multi-language auto-translate
- [ ] Webhook notifications
- [ ] REST API for external automation
- [ ] Docker image

---

## 📞 Support

- 📧 Email: [businessxinfo1@gmail.com](mailto:businessxinfo1@gmail.com)
- 🌐 Live demo: https://khokon-chandra.freedev.app
  - **Admin password:** `contact email`

**Included support:** 30 days of bug fixes and setup help.

---

## 🙏 Credits

- **[OpenRouter](https://openrouter.ai)** — AI script generation
- **[Pollinations.ai](https://pollinations.ai)** — Free AI images & TTS
- **[Pixabay](https://pixabay.com)** — Stock photos & videos
- **[Google Fonts](https://fonts.google.com)** — Hind Siliguri
- **[InfinityFree](https://infinityfree.com)** — Free hosting tested

---

## ⭐ Show Your Support

If this project helped you build something, please:
- ⭐ Star this repo
- 🐛 Report bugs
- 💡 Suggest features
- 📢 Share with others

---

<div align="center">

**Built with ❤️ in Bangladesh**

*Bangla AI tools for the next generation of creators*

[⬆ Back to top](#-kbagent-studio)

</div>
