TF Talent Analytics — Dashboard Hub
> **Confidential.** Authorised Tearfund staff only. Access restricted to `@tearfund.org` Google accounts.
A GitHub Pages site hosting Tearfund's People & Culture workforce dashboards, protected by Google authentication via Firebase.
---
🔗 Live Site
`https://priscamwithi-cell.github.io/TFGroupDashboard/`
---
📁 Repository Structure
```
TFGroupDashboard/
│
├── index.html                  ← Hub landing page (login + dashboard selector)
├── talent_strategy.html        ← Talent Strategy 2026–2029 (primary dashboard)
├── regional_dashboard.html     ← Regional Workforce Profiles dashboard
├── group_dashboard.html        ← Group Workforce Dashboard
│
└── README.md
```
> Each dashboard is a standalone HTML file. To add a new dashboard, drop its `.html` file into the root and add a card to `index.html` (see [Adding a Dashboard](#adding-a-dashboard) below).
---
🔐 Authentication Setup (Firebase)
Authentication is handled by Firebase Authentication using Google Sign-In. Access is restricted to `@tearfund.org` email addresses only.
Step 1 — Create a Firebase Project
Go to https://console.firebase.google.com
Click Add project → name it (e.g. `tf-talent-hub`) → follow the prompts
You do not need Google Analytics for this project
Step 2 — Register a Web App
In your Firebase project, click the web icon (`</>`) to add a web app
Give it a nickname (e.g. `TF Dashboard Hub`)
Do not enable Firebase Hosting (you're using GitHub Pages)
Copy the `firebaseConfig` object shown — you'll need it in Step 4
Step 3 — Enable Google Sign-In
In the Firebase Console, go to Authentication → Sign-in method
Click Google → toggle Enable
Set the Project support email to your Tearfund email
Click Save
Step 4 — Add Authorised Domain
Still in Authentication, go to the Settings tab → Authorised domains
Click Add domain and enter:
```
   priscamwithi-cell.github.io
   ```
Click Add
Step 5 — Set Up Firestore Allowlist
The site uses a Firestore database to store the list of approved email addresses. Only emails on this list can access the dashboards after signing in.
In the Firebase Console, go to Build → Firestore Database
Click Create database
Choose Start in production mode → select a region close to your users (e.g. `europe-west2` for UK) → click Enable
Once created, go to the Rules tab and replace the default rules with:
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
This means: a signed-in user can only read their own allowlist entry — and nobody can write via the app (only you can, via the Firebase Console). Click Publish.
Go to the Data tab → click Start collection
Collection ID: `allowlist`
Click Next
Add your first approved user:
Document ID: the person's full email address in lowercase, e.g. `prisca.mwithi@tearfund.org`
Add a field: `name` → type `string` → value: their name (e.g. `Prisca Mwithi`)
Click Save
Repeat step 6 for each person who should have access. To remove someone, simply delete their document from the `allowlist` collection.
Step 7 — Paste Your Firebase Config into index.html
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
🚀 Deploying to GitHub Pages
Push all files to the `main` branch of this repository
Go to Settings → Pages in the GitHub repo
Under Source, select Deploy from a branch
Choose branch: `main`, folder: `/ (root)`
Click Save
GitHub will publish the site at:
```
https://priscamwithi-cell.github.io/TFGroupDashboard/
```
Allow 1–2 minutes for the first deployment. Subsequent pushes update automatically.
---
➕ Adding a Dashboard
Add the file — drop the new dashboard `.html` file into the root of the repo (same level as `index.html`)
Add a card — open `index.html` and find the `<!-- Dashboard 1 -->` section inside the `.dash-grid` div. Copy one of the existing cards and update three things:
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
Commit and push — the new dashboard will appear on the hub immediately after GitHub Pages redeploys (usually under 1 minute).
---
👥 Managing Access
Access is controlled via a Firestore `allowlist` collection. Only you can manage it via the Firebase Console.
Action	How
Add someone	Firebase Console → Firestore → `allowlist` → Add document (Document ID = their email, lowercase)
Remove someone	Firebase Console → Firestore → `allowlist` → find their document → Delete
Check who has access	Firebase Console → Firestore → `allowlist` → view all documents
The `@tearfund.org` domain restriction is enforced first — even if an email is on the allowlist, a non-Tearfund Google account will be rejected.
---
🎨 Brand
Token	Value
Primary teal	`#1088A8`
Dark teal	`#0a6880`
Yellow	`#F8D000`
Typography	Nunito / Nunito Sans (Google Fonts)
---
📋 Dashboards
Dashboard	File	Description	Permissions
Talent Strategy 2026–2029	`talent_strategy.html`	Four-pillar talent strategy with editable content and team commenting	Editor: `prisca.mwithi@tearfund.org` · All others: read + comment
Regional Workforce Profiles	`regional_dashboard.html`	Staff distribution, grades, contracts across 7 regions	Read
Group Workforce Dashboard	`group_dashboard.html`	Per-group profiles across all 5 Tearfund groups	Read
✏️ Edit & Comment Permissions
The Talent Strategy dashboard has two permission levels:
Editor (`prisca.mwithi@tearfund.org` only)
An ✎ Edit button appears in the topbar after sign-in
Click it to enter edit mode — all content fields become editable in place
Changes are saved locally (browser storage) and can be exported as a `.txt` file
Comments from all users can be deleted by the editor
All other allowlist members (read + comment)
Select any text on the page to trigger a 💬 Add Comment prompt
Comments are stored in Firestore (`comments` collection) and persist across sessions
All team members can see each other's comments via the 💬 Comments button in the topbar
Each person can delete their own comments
Firestore Comments Collection Structure
Comments are stored in Firestore under the `comments` collection with these fields:
`page` — identifies which dashboard the comment belongs to (e.g. `talent_strategy`)
`quote` — the highlighted text the comment refers to
`comment` — the comment text
`author` — the commenter's email
`authorName` — display name
`timestamp` — server timestamp
🌙 Light / Dark Mode
The hub has a ☀️/🌙 toggle in the topbar. Each dashboard keeps its own styling — the toggle only applies to the hub landing page. The preference is saved in the browser.
---
Maintained by the Tearfund People & Culture team.
