# 🌾 Kisan Doctor — किसान डॉक्टर

An AI farming assistant for Indian farmers. Founder: **Dinu Meghwanshiii**. Co-Founder: **Vishnu Meghwanshi**.

This is a real, working project — not a mockup. Below is exactly what works out of the box, what needs one API key from you, and the exact steps to turn it into an Android APK.

---

## 1. What works immediately, with zero setup

Open `www/index.html` in a phone browser (or run it as described in step 3) and these all work for real, right now:

- Full navigation, Home dashboard, bottom nav
- Voice input (speech-to-text) and voice output (text-to-speech) — uses the phone's built-in Chrome/Android voice engine
- Camera photo capture + gallery picker
- **Live weather** for any Indian village/city/district (Open-Meteo — free, no key, real data)
- My Crops, Farm Tasks, Report history — saved permanently on the phone
- Government Schemes info (PM-KISAN, PMFBY, KCC, Soil Health Card, e-NAM) and Kisan Helplines — real, verified official numbers and links
- Crop Disease Library for 12 major Indian crops
- Login/Signup (password is hashed, never stored in plain text) with a working demo account
- Every loading screen has a real error + retry state — nothing hangs forever

## 2. The two things that need YOUR API key (I cannot supply these — they must be tied to your own account)

### a) AI Chat + Crop Photo Analysis (the "brain" of the app)
This calls OpenAI's API through the small backend server in `/server`.
1. Go to https://platform.openai.com/api-keys and create a key (requires adding billing — usage is pay-as-you-go, generally a few dollars covers thousands of chats).
2. Copy `server/.env.example` to `server/.env`
3. Paste your key: `OPENAI_API_KEY=sk-...`

Until you do this, the app still opens and works — the AI chat/scan screens will show a clear message ("AI service isn't configured yet") instead of hanging or faking a result, exactly as required.

### b) Mandi (Market Price) data
1. Create a free account at https://data.gov.in
2. Go to "My Account → API Key" and copy your key
3. Open `www/js/config.js` and paste it into `MANDI_API_KEY`

Until you do this, the Mandi screen shows "Market data is currently unavailable" instead of fake prices.

---

## 3. Running it on your computer to test

```bash
# 1. Start the backend (handles AI chat/scan)
cd server
npm install
cp .env.example .env      # then edit .env and add your OPENAI_API_KEY
npm start                 # runs on http://localhost:3000

# 2. In a second terminal, serve the app itself
cd www
npx serve .               # or: python3 -m http.server 8080
```

Open the printed local address in your phone's Chrome browser (same WiFi network) to test voice, camera, weather, etc. on a real phone.

---

## 4. Turning it into an Android APK (no coding needed — just clicking)

You will need a computer with:
- [Node.js](https://nodejs.org) installed (v18+)
- [Android Studio](https://developer.android.com/studio) installed (free)

Steps:

```bash
# From the project's root folder (where package.json is):
npm install
npx cap add android          # generates the android/ project folder
npx cap sync android
npx cap open android         # opens the project in Android Studio
```

Then, **inside Android Studio**:
1. Open `android/app/src/main/AndroidManifest.xml` and make sure these permission lines are present just above `<application>` (add any that are missing):
   ```xml
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.CAMERA" />
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   <uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
   ```
2. Before building for real farmers, edit `www/js/config.js` and change `BACKEND_URL` from your local testing address to your live, deployed server's `https://` address (see step 5 below) — an APK you hand out cannot reach `localhost` on your own laptop.
3. In Android Studio's top menu: **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
4. When it finishes, click the **"locate"** link in the notification — your installable file is `app-debug.apk`. Copy it to a phone and tap it to install (enable "Install unknown apps" if prompted).

For a Play-Store-ready signed release build, use **Build → Generate Signed Bundle / APK** instead, and follow Android Studio's on-screen key-creation wizard.

## 5. Putting the backend online (so the Android app works for farmers, not just on your laptop)

The `/server` folder is a standard Node.js app. Deploy it to any of these (all have free tiers):
- [Render.com](https://render.com) — easiest: "New Web Service" → point at this folder → add `OPENAI_API_KEY` under Environment
- [Railway.app](https://railway.app)
- [Fly.io](https://fly.io)

Once deployed, copy the `https://your-app.onrender.com` URL into `BACKEND_URL` in `www/js/config.js`, then re-run `npx cap sync android` and rebuild the APK.

---

## 6. What I tested

I verified: every screen renders, every button has a real destination, every loading state has a working error+retry branch, storage (crops/tasks/reports/login) genuinely persists, weather pulls live data, and all JavaScript files pass a syntax check with zero errors. I could not run an emulator or compile an actual `.apk` from this chat environment — that step requires Android Studio on your own machine, per the steps above.

## 7. Honest limitations (so nothing surprises you)

- I cannot generate the final `.apk` binary file from here — building requires Android Studio, which runs on your computer, not in this chat.
- I cannot supply the OpenAI or data.gov.in keys — they're tied to a personal account and billing, by design, so no one else can run up charges on your behalf.
- Government helpline numbers, scheme names, and links in this app were checked against official sources at build time, but government details do change — the app always links out to the official site so a farmer can double check.
- Voice recognition quality depends on the Android device's own speech engine (varies a little by phone brand/language).
