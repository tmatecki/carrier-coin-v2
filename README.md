# Carrier Coin [CARY] — Setup & Deploy Guide

Single-file app. No build step. Firebase for persistence + cross-device sync.

---

## FILE STRUCTURE

```
carrier-coin-v2/
├── index.html       ← The entire app (edit this in VS Code)
├── firebase.json    ← Firebase hosting config (don't edit)
└── README.md        ← This file
```

---

## STEP 1: CREATE FIREBASE PROJECT

1. Open **https://console.firebase.google.com**
2. Click **Add project**
3. Name it (e.g. `carrier-coin-v2`) → **disable** Google Analytics → **Create project**
4. Wait for it to finish → click **Continue**

---

## STEP 2: CREATE REALTIME DATABASE

1. In Firebase console sidebar → **Build → Realtime Database**
2. Click **Create Database**
3. Pick any location → **Next**
4. Choose **Start in test mode** → **Enable**
5. Done — you'll see an empty database

---

## STEP 3: REGISTER WEB APP & GET CONFIG

1. In Firebase console → **Project Settings** (gear icon top-left)
2. Scroll down to **"Your apps"** → click the **web icon** (`</>`)
3. App nickname: anything (e.g. `cary-web`) → click **Register app**
4. You'll see a config block — copy these values:
   - `apiKey`
   - `authDomain`
   - `projectId`
   - `storageBucket`
   - `messagingSenderId`
   - `appId`
5. You also need the `databaseURL` — go back to Realtime Database and copy the URL from the top (looks like `https://carrier-coin-v2-default-rtdb.firebaseio.com`)
6. Click **Continue to console**

---

## STEP 4: PASTE CONFIG INTO index.html

Open `index.html` in VS Code. Find this block (around line 50):

```js
const FIREBASE_CONFIG = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT.firebaseapp.com",
  databaseURL:       "https://YOUR_PROJECT-default-rtdb.firebaseio.com",
  ...
};
```

Replace with your actual values. Save.

---

## STEP 5: INSTALL FIREBASE CLI

Open VS Code terminal (`Ctrl + backtick`):

```bash
npm install -g firebase-tools
```

Then log in:

```bash
firebase login
```

A browser window opens → sign in with the same Google account you used for Firebase.

---

## STEP 6: DEPLOY TO FIREBASE (first time)

Navigate to your project folder:

```bash
cd ~/carrier-coin-v2
```

Link to your Firebase project:

```bash
firebase use --add
```

Select your project from the list → give it an alias (e.g. `default`).

Deploy:

```bash
firebase deploy --only hosting
```

Done! It prints your live URL:

```
✓ Hosting URL: https://carrier-coin-v2.web.app
```

Open this on your laptop AND phone — they share the same data in real-time.

---

## STEP 7: CREATE GITHUB REPO

1. Go to **https://github.com/new**
2. Repository name: `carrier-coin-v2`
3. Set to **Public** (or Private)
4. **Don't** tick any checkboxes (no README, no .gitignore)
5. Click **Create repository**

Now in your VS Code terminal:

```bash
cd ~/carrier-coin-v2
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/carrier-coin-v2.git
git push -u origin main
```

---

## STEP 8: CONNECT GITHUB → FIREBASE AUTO-DEPLOY

This makes every `git push` automatically deploy to Firebase.

```bash
firebase init hosting:github
```

It will ask:

| Question | Answer |
|----------|--------|
| GitHub repo | `YOUR_USERNAME/carrier-coin-v2` |
| Set up workflow for deploys | `Yes` |
| Auto deploy on merge to main | `Yes` |
| Branch | `main` |

This creates a `.github/workflows/` folder with deploy actions. Commit and push:

```bash
git add .
git commit -m "add auto-deploy"
git push
```

---

## DAILY WORKFLOW

After setup, your workflow is:

1. **Edit** `index.html` in VS Code (or ask Claude Code to help)
2. **Save** the file
3. **Deploy** with three commands:

```bash
git add .
git commit -m "description of change"
git push
```

GitHub Actions runs → Firebase deploys → live in ~60 seconds.

Or use VS Code's Source Control panel (branch icon in sidebar) to stage, commit, and push with buttons.

---

## EDITING IN VS CODE

### Change user names or balances
Search for `const USERS` near the top of the script:

```js
const USERS = [
  { id: "u1", name: "Stuart E.",  balance: 367000 },
  { id: "u2", name: "Thomas M.",  balance: 189763 },
  ...
];
```

### Change coin name or ticker
```js
const COIN_NAME    = "Carrier Coin";
const COIN_SYMBOL  = "CARY";
const PEG_ASSET    = "EUR";
```

### Change colours
Edit CSS variables in `:root` at the top of the `<style>` block:
```css
--accent: #3366ff;
--green: #22b455;
--orange: #e8700a;
```

### Reset all data
Go to Firebase Console → Realtime Database → hover over root node → click ✕ to delete.
Refresh the app — it creates fresh genesis state.

---

## TROUBLESHOOTING

**"Firebase not configured" in footer:**
→ You haven't pasted your config values into `index.html`, or the values are wrong.

**QR code doesn't scan:**
→ Make sure you're scanning the QR on the **deployed** URL (not localhost). The QR encodes the full URL.

**Changes not syncing between devices:**
→ Check that Firebase Realtime Database is in **test mode** and the `databaseURL` is correct.

**`firebase deploy` fails:**
→ Run `firebase use --add` and select your project again.

**Auto-deploy not working after push:**
→ Check GitHub → Actions tab → see if the workflow is running or has errors.
