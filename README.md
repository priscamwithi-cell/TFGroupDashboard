# TF Talent Analytics — Dashboard Hub

> **Confidential.** Authorised Tearfund staff only. Access restricted to `@tearfund.org` Google accounts.

A GitHub Pages site hosting Tearfund's People & Culture workforce dashboards, protected by Google authentication via Firebase.

---

## 🔗 Live Site

`https://priscamwithi-cell.github.io/TFGroupDashboard/`

---

## 📁 Repository Structure

```
TFGroupDashboard/
│
├── index.html                  ← Hub landing page (login + dashboard selector)
├── regional_dashboard.html     ← Regional Workforce Profiles dashboard
├── group_dashboard.html        ← Group Workforce Dashboard
│
└── README.md
```

> Each dashboard is a standalone HTML file. To add a new dashboard, drop its `.html` file into the root and add a card to `index.html` (see [Adding a Dashboard](#adding-a-dashboard) below).

---

## 🔐 Authentication Setup (Firebase)

Authentication is handled by **Firebase Authentication** using Google Sign-In. Access is restricted to `@tearfund.org` email addresses only.

### Step 1 — Create a Firebase Project

1. Go to [https://console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it (e.g. `tf-talent-hub`) → follow the prompts
3. You do **not** need Google Analytics for this project

### Step 2 — Register a Web App

1. In your Firebase project, click the **web icon** (`</>`) to add a web app
2. Give it a nickname (e.g. `TF Dashboard Hub`)
3. **Do not** enable Firebase Hosting (you're using GitHub Pages)
4. Copy the `firebaseConfig` object shown — you'll need it in Step 4

### Step 3 — Enable Google Sign-In

1. In the Firebase Console, go to **Authentication → Sign-in method**
2. Click **Google** → toggle **Enable**
3. Set the **Project support email** to your Tearfund email
4. Click **Save**

### Step 4 — Add Authorised Domain

1. Still in **Authentication**, go to the **Settings** tab → **Authorised domains**
2. Click **Add domain** and enter:
   ```
   priscamwithi-cell.github.io
   ```
3. Click **Add**

### Step 5 — Set Up Firestore Allowlist

The site uses a Firestore database to store the list of approved email addresses. Only emails on this list can access the dashboards after signing in.

1. In the Firebase Console, go to **Build → Firestore Database**
2. Click **Create database**
3. Choose **Start in production mode** → select a region close to your users (e.g. `europe-west2` for UK) → click **Enable**
4. Once created, go to the **Rules** tab and replace the default rules with:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /allowlist/{email} {
         allow read: if request.auth != null && request.auth.token.email == email;
         allow write: if false;
       }
     }
   }
   ```

   This means: a signed-in user can only read their own allowlist entry — and nobody can write via the app (only you can, via the Firebase Console). Click **Publish**.

5. Go to the **Data** tab → click **Start collection**
   - Collection ID: `allowlist`
   - Click **Next**

6. Add your first approved user:
   - Document ID: the person's full email address in lowercase, e.g. `prisca.mwithi@tearfund.org`
   - Add a field: `name` → type `string` → value: their name (e.g. `Prisca Mwithi`)
   - Click **Save**

7. Repeat step 6 for each person who should have access. To **remove** someone, simply delete their document from the `allowlist` collection.

### Step 7 — Paste Your Firebase Config into index.html

Open `index.html` and find this block near the top:

```javascript
const firebaseConfig = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT_ID.firebaseapp.com",
  projectId:         "YOUR_PROJECT_ID",
  storageBucket:     "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId:             "YOUR_APP_ID"
};
```

Replace each `"YOUR_..."` value with the corresponding value from your Firebase project's web app config. Save and commit.

---

## 🚀 Deploying to GitHub Pages

1. Push all files to the `main` branch of this repository
2. Go to **Settings → Pages** in the GitHub repo
3. Under **Source**, select **Deploy from a branch**
4. Choose branch: `main`, folder: `/ (root)`
5. Click **Save**

GitHub will publish the site at:
```
https://priscamwithi-cell.github.io/TFGroupDashboard/
```

Allow 1–2 minutes for the first deployment. Subsequent pushes update automatically.

---

## ➕ Adding a Dashboard

1. **Add the file** — drop the new dashboard `.html` file into the root of the repo (same level as `index.html`)

2. **Add a card** — open `index.html` and find the `<!-- Dashboard 1 -->` section inside the `.dash-grid` div. Copy one of the existing cards and update three things:

   ```html
   <div class="dash-card" onclick="openDashboard('YOUR_FILE.html', 'Your Dashboard Title')">
     <div class="card-icon">📈</div>   <!-- Choose a relevant emoji -->
     <div>
       <div class="card-meta">
         <span class="card-tag">Workforce</span>   <!-- or 'Recruitment', 'DEI', etc. -->
       </div>
       <h3>Your Dashboard Title</h3>
       <p>A one or two sentence description of what this dashboard shows.</p>
     </div>
     <div class="card-arrow">→</div>
   </div>
   ```

3. **Commit and push** — the new dashboard will appear on the hub immediately after GitHub Pages redeploys (usually under 1 minute).

---

## 👥 Managing Access

Access is controlled via a Firestore `allowlist` collection. Only you can manage it via the Firebase Console.

| Action | How |
|---|---|
| **Add someone** | Firebase Console → Firestore → `allowlist` → Add document (Document ID = their email, lowercase) |
| **Remove someone** | Firebase Console → Firestore → `allowlist` → find their document → Delete |
| **Check who has access** | Firebase Console → Firestore → `allowlist` → view all documents |

The `@tearfund.org` domain restriction is enforced first — even if an email is on the allowlist, a non-Tearfund Google account will be rejected.

---

## 🎨 Brand

| Token | Value |
|---|---|
| Primary teal | `#1088A8` |
| Dark teal | `#0a6880` |
| Yellow | `#F8D000` |
| Typography | Nunito / Nunito Sans (Google Fonts) |

---

## 📋 Dashboards

| Dashboard | File | Description |
|---|---|---|
| Regional Workforce Profiles | `regional_dashboard.html` | Staff distribution, grades, contracts across 7 regions |
| Group Workforce Dashboard | `group_dashboard.html` | Per-group profiles across all 5 Tearfund groups |

---

*Maintained by the Tearfund People & Culture team.*

