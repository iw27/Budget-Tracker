# Budget Tracker — Web App (iOS + Android)
This is a **single-file web app** that works on iOS and Android. You can open it in your mobile browser, or host it for free and “Add to Home Screen”. Data is saved in **Firebase** so you can access it from any device.

## What you’ll build
- An entry form (amount, category, note).
- A log of transactions for the selected month.
- Running monthly totals with limits:
  - Extra Spending = $600
  - Groceries = $1200
  - Restaurants = $300
- Sign in with Google so your data is private to you.
- Export month data to CSV.

---

## 1) Create your Firebase project
1. Go to **console.firebase.google.com** → **Add project** → give it a name.
2. When asked about Google Analytics, choose whatever you like.
3. In the left nav, click **Build → Authentication → Get started**.
   - **Sign-in method** → enable **Google**.
4. Click **Build → Firestore Database → Create database**.
   - Start in **Production mode** (recommended).
   - Choose a region close to you.
5. Click the gear icon (Project settings) → **General** → **Your apps** → **Web** → **</>** Add app.
   - Register app, then you’ll get your **Firebase config** (apiKey, authDomain, etc.).
   - Copy it for the next step.

## 2) Secure your database
In **Firestore → Rules**, paste these rules and **Publish**:

```
// Only allow the signed-in user to read/write their own data.
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

## 3) Fill in your Firebase config
Open `index.html`, find the `firebaseConfig` object, and replace the placeholders with your real values from Step 1.

```js
const firebaseConfig = {
  apiKey: "…",
  authDomain: "…",
  projectId: "…",
  storageBucket: "…",
  messagingSenderId: "…",
  appId: "…"
};
```

## 4) Run it locally (no tooling needed)
Just double-click `index.html` to open in your browser. Sign in with Google, then add transactions. (If your browser blocks Google sign-in from local files, host it as in Step 5.)

## 5) Host it for free (pick one)
- **GitHub Pages**: Create a repo, upload `index.html`, enable Pages.
- **Netlify**: Go to app.netlify.com → “New site from Git” or “Deploy site” → drag-and-drop the folder.
- **Vercel**: vercel.com → “New Project” → import your repo.

Once deployed, open the URL on your phone and **Add to Home Screen** to use it like an app.

## 6) Customize limits
At the top of `index.html`, edit:
```js
const CATEGORY_LIMITS = {
  "Extra Spending": 600,
  "Groceries": 1200,
  "Restaurants": 300,
};
```

## 7) Data model
Transactions are stored under:  
`/users/{uid}/transactions/{autoId}` with fields:
- `amount` (number)
- `category` (string: "Extra Spending" | "Groceries" | "Restaurants")
- `note` (string, optional)
- `createdAt` (timestamp, set on the server)

## 8) How monthly totals work
The app filters by the selected month using the `createdAt` timestamp and computes sums for each category, updating progress bars against your limits. Bars turn **yellow at ≥ 80%** and **red at ≥ 100%** of the limit.

## 9) Export CSV
Click **Export CSV** to download a file named `budget-YYYY-MM.csv` for the currently selected month.

## 10) Troubleshooting
- **I see a blank screen** → Open **DevTools → Console** for any error; usually a missing/incorrect Firebase config.
- **Permission denied** → Make sure you published the Firestore rules above and that you’re signed in.
- **Google sign-in blocked in file://** → Host the file (see Step 5) or use Chrome to open the local file.
- **Time zones** → Timestamps are server-side; month boundaries use your device’s local time.
- **Multiple devices** → Use the same Google account on each device.

---

That’s it! You now have a working budget tracker web app that runs on iOS, Android, and desktop.
