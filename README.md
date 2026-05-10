# Cricket Odds Analyser — Setup Guide

## What you have
- A fully working web app (index.html) with live CricketData API + OpenWeather
- GitHub Pages deployment (auto-deploys on every push)
- TWA config to build a real Android APK from the hosted URL

---

## STEP 1 — Create the GitHub repo

1. Go to https://github.com and sign in (or create a free account)
2. Click **New repository** (green button top right)
3. Name it: `cricket-analyser`
4. Set to **Public**
5. Click **Create repository**

---

## STEP 2 — Upload all these files

In your new repo, upload the following files maintaining the folder structure:

```
cricket-analyser/
├── index.html
├── manifest.json
├── icons/
│   ├── icon-192.png       ← create a 192×192 cricket icon (see note below)
│   └── icon-512.png       ← create a 512×512 cricket icon
├── public/
│   └── .well-known/
│       └── assetlinks.json
└── .github/
    └── workflows/
        └── deploy.yml
```

**For icons:** Go to https://favicon.io or https://realfavicongenerator.net, upload any cricket/sports image, download the 192×192 and 512×512 PNG files.

To upload on GitHub:
- Click **Add file → Upload files**
- Drag all files in
- For subfolders, create the folder by typing `icons/icon-192.png` in the filename box

---

## STEP 3 — Enable GitHub Pages

1. In your repo → **Settings** → **Pages** (left sidebar)
2. Under **Source** → select **GitHub Actions**
3. Click **Save**
4. The workflow will auto-run. After ~2 minutes your app is live at:
   `https://YOUR_USERNAME.github.io/cricket-analyser/`

Test it in your browser — it should fully work.

---

## STEP 4 — Build the Android APK with Bubblewrap

### Prerequisites (do this on your PC/laptop):
- Install **Node.js** from https://nodejs.org (LTS version)
- Install **Java JDK 11+** from https://adoptium.net
- Install **Android SDK** — easiest via Android Studio: https://developer.android.com/studio

### Install Bubblewrap:
```bash
npm install -g @bubblewrap/cli
```

### Build the APK:

```bash
# Create a folder for the APK build
mkdir cricket-apk && cd cricket-apk

# Initialize with your live GitHub Pages URL
bubblewrap init --manifest https://YOUR_USERNAME.github.io/cricket-analyser/manifest.json

# It will ask questions — use these answers:
# Package ID: com.cricketodds.analyser
# App name: Cricket Odds Analyser
# Launcher name: Cricket Odds
# Theme colour: #0a1628
# Background colour: #0a1628
# Start URL: /cricket-analyser/

# Build the APK
bubblewrap build
```

The APK will be created as `app-release-unsigned.apk` in the folder.

### Sign the APK (required to install on Android):
```bash
# Bubblewrap generates a keystore automatically during build
# Just run:
bubblewrap build --skipPwaValidation
```

The final signed APK will be: `app-release-signed.apk`

---

## STEP 5 — Install on Android

### Option A — Direct install (sideloading):
1. Transfer `app-release-signed.apk` to your Android phone (via USB, WhatsApp, email, Google Drive)
2. On Android: **Settings → Security → Install unknown apps** → allow your browser/file manager
3. Open the APK file → Install
4. Done — Cricket Odds Analyser appears in your app drawer

### Option B — Google Play Store (future):
- Create a Google Play Developer account ($25 one-time)
- Upload the signed APK/AAB to Play Console

---

## STEP 6 — Update the assetlinks.json (for full TWA, no browser bar)

After building, get your keystore fingerprint:
```bash
keytool -list -v -keystore android.keystore -alias android
```

Copy the **SHA-256** fingerprint and paste it into `public/.well-known/assetlinks.json`:
```json
"sha256_cert_fingerprints": ["AA:BB:CC:...your fingerprint here..."]
```

Also replace `YOUR_GITHUB_USERNAME` with your actual username in:
- `android/twa-manifest.json`

Push the update to GitHub — Pages will redeploy automatically.

---

## Notes

- **Internet required** — the app needs connectivity for live scores + AI analysis
- **API limits** — CricketData free tier: 100 calls/day. Upgrade at cricketdata.org if you need more
- **OpenWeather** free tier: 1000 calls/day — more than enough
- **To update the app**: just edit `index.html` and push to GitHub. The APK auto-fetches the latest version since it's a TWA (the APK is just a shell that loads your website)

---

## Quick alternative — PWABuilder (no terminal needed)

If you don't want to use command line at all:
1. Deploy to GitHub Pages (Steps 1-3 above)
2. Go to https://www.pwabuilder.com
3. Enter your GitHub Pages URL
4. Click **Package for stores** → **Android**
5. Download the APK — done!
