<p align="center">
  <img src="apps/web/public/icon.png" alt="Mail Cleaner" width="96" height="96" />
</p>

# Mail Cleaner

Privacy-first, self-hosted Gmail cleanup. Runs **locally** on your machine — no cloud backend, no analytics.

Scan Spam, Trash, and Inbox, group messages by sender, and bulk delete, block, keep (whitelist), or mass unsubscribe.

**Repository:** [github.com/Kava4/mail-cleaner](https://github.com/Kava4/mail-cleaner)

---

## Requirements

| Tool | Version |
|------|---------|
| Node.js | 20+ (LTS recommended) |
| npm | 10+ |
| Google account | Gmail |
| Google Cloud project | OAuth + Gmail API enabled |

---

## Quick start (Windows)

Double-click **`run.bat`** in the project root:

- Checks Node.js
- Runs `npm install` if needed
- Warns if `data/credentials.json` is missing
- Opens the browser and starts `npm run dev`

### Manual setup

```bash
git clone https://github.com/Kava4/mail-cleaner.git
cd mail-cleaner
npm install
npm run dev
```

Open **http://localhost:3000** and click **Connect Gmail**.

---

## Google OAuth setup

### 1. Create a project
1. Open [Google Cloud Console](https://console.cloud.google.com/)
2. **Select a project** → **New Project** (e.g. `mail-cleaner`)

### 2. Enable Gmail API
1. **APIs & Services** → **Library**
2. Search **Gmail API** → **Enable**

### 3. OAuth consent screen
1. **APIs & Services** → **OAuth consent screen**
2. User type: **External** (personal Gmail) or **Internal** (Google Workspace)
3. Fill in app name and support email
4. Scopes: the app requests these automatically on sign-in:
   - `https://mail.google.com/` (read, modify, permanent delete)
   - `https://www.googleapis.com/auth/gmail.settings.basic` (block filters)
5. **Test users**: add your Gmail address (required while the app is in **Testing**)

### 4. OAuth client ID
1. **APIs & Services** → **Credentials** → **Create Credentials** → **OAuth client ID**
2. Application type: **Web application**
3. **Authorized redirect URIs**:
   ```
   http://localhost:3000/api/auth/callback
   ```
4. **Download JSON** and save as:

```
mail-cleaner/data/credentials.json
```

> Never commit `credentials.json` to GitHub. It is listed in `.gitignore`.

### 5. Connect
1. Run the app and click **Connect Gmail**
2. Grant permissions (full Gmail access is required for permanent delete from Trash/Spam)
3. Click **Scan mailboxes**

---

## Local data layout

```
mail-cleaner/
├── apps/web/              Next.js UI + API routes
├── packages/shared/       Shared types & spam rules
├── data/                  ← not committed to Git
│   ├── credentials.json   OAuth client (you provide this)
│   ├── token.json         Access/refresh token (created on sign-in)
│   └── whitelist.json     Senders you marked as Keep
└── README.md
```

---

## Usage

| Action | What it does |
|--------|----------------|
| **Scan mailboxes** | Scans Spam, Trash, Inbox (up to 2000 emails per folder) |
| **Block** | Deletes emails + creates a Gmail filter (future mail → Trash) |
| **Delete** | Inbox → Trash · Spam/Trash → permanent delete |
| **Keep** | Whitelist — hidden when "Hide kept" is enabled |
| **Unsub / Mass Unsub** | List-Unsubscribe headers (newsletters) — selected or all visible |
| **Empty Spam / Trash** | Permanently deletes everything in that folder |

**Tips**
- Filter by `Type → Newsletter`, then **Unsub all visible**, then **Delete selected**
- If delete fails: Sign out → delete `data/token.json` → Connect again

---

## Production build (local)

```bash
npm run build
npm run start
```

Default port: **3000**. For another port: `PORT=3001 npm run start`

---

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `NEXT_PUBLIC_GITHUB_URL` | `https://github.com/Kava4/mail-cleaner` | GitHub link shown in the UI |

Copy `.env.example` to `apps/web/.env.local` and adjust if needed.

---

## Distribution

Self-hosted: each user runs their own instance. You share **code only**, never credentials or tokens.

| Method | Notes |
|--------|-------|
| **GitHub repo** | Recommended. Push code without `data/`. Users clone and add their own OAuth client. |
| **GitHub Release (zip)** | Attach source zip; users unzip, `npm install`, add credentials, run. |
| **VPS** | `git clone`, `npm run build`, PM2/systemd, nginx + HTTPS. Add production redirect URI in Google Cloud. |

**Not supported out of the box:** Vercel/Netlify serverless (tokens stored on local filesystem in `data/`).

---

## Privacy

- 100% local — tokens in `data/token.json`
- No third-party analytics
- Revoke access anytime: [Google Account → Third-party apps](https://myaccount.google.com/permissions)

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| OAuth `access_denied` | Add your email as a Test user on the consent screen |
| Delete / Empty Trash fails | Sign out → delete `data/token.json` → Connect again |
| `credentials.json not found` | Save OAuth JSON to `data/credentials.json` |
| Port 3000 in use | Stop the old dev server or use `PORT=3001 npm run dev` |

---

## License

MIT
