# BBA Batch 2029 — Batch Portal

A static website for our batch, hosted free on GitHub Pages, with Firebase handling
admin sign-in and the database. No backend server, no Node.js, no build step —
the files you see are the files that run.

**One Batch. One Journey. One Legacy.**

---

## What's in here

```
index.html            all 14 tabs in one page, switched by the URL hash
css/style.css         navy / white / gold theme
js/firebase-config.js ← paste your Firebase keys here
js/app.js             data loading, routing, all public pages
js/auth.js            Firebase Authentication + admin check
js/admin.js           the dashboard: add / edit / delete everything
firestore.rules       public read, admin-only writes
README.md             this file
```

Open `index.html` by double-clicking it and the site works right away in **sample mode**:
every tab is filled with placeholder content so you can see the design. Sign-in stays
disabled until you add a Firebase config.

---

## 1. Create a Firebase project

1. Go to <https://console.firebase.google.com> and sign in with a Google account.
2. **Add project** → name it `bba-batch-2029` → continue. Google Analytics is optional; skip it.
3. On the project home, click the **web icon (`</>`)** to register a web app. Nickname it `batch-portal`.
   Do **not** tick "Firebase Hosting" — GitHub Pages is doing the hosting.
4. Firebase shows you a `firebaseConfig` object. Keep that tab open for step 4.

## 2. Enable Authentication

1. Left sidebar → **Build → Authentication → Get started**.
2. **Sign-in method** tab → **Email/Password** → enable the first toggle → **Save**.
   (Leave "Email link / passwordless" off.)

## 3. Create Firestore

1. Left sidebar → **Build → Firestore Database → Create database**.
2. Pick a location close to your university. Choose **production mode** — the rules in
   step 6 will open up reading.
3. You don't need to create any collections by hand. The dashboard's
   **Load sample content** button creates all ten of them for you.

## 4. Paste your Firebase configuration

Open `js/firebase-config.js` and replace the placeholder values with the ones from step 1:

```js
export const firebaseConfig = {
  apiKey:            "AIza...",
  authDomain:        "bba-batch-2029.firebaseapp.com",
  projectId:         "bba-batch-2029",
  storageBucket:     "bba-batch-2029.appspot.com",
  messagingSenderId: "123456789012",
  appId:             "1:123456789012:web:abc123"
};
```

These values are meant to be public — they identify your project, they are not a password.
Your data is protected by the rules in step 6, which run on Google's servers where nobody
can edit them from a browser.

## 5. Create the first admin account

Two short steps, both in the Firebase console:

1. **Authentication → Users → Add user.** Enter the admin's email and a strong password → **Add user**.
2. Copy that user's **User UID** from the Users table (a long string like `k3Jd9...`).
   Then go to **Firestore Database → Start collection**:
   - Collection ID: `admins`
   - Document ID: **paste the User UID**
   - Add one field: `email` (string) with the admin's email → **Save**.

That document *is* the permission. To add another admin later, repeat both steps.
To remove one, delete their `admins` document (and their Authentication user).

> The site never stores a password in JavaScript. `auth.js` asks Firebase to verify
> credentials, then checks for the `admins/{uid}` document. Hiding the edit buttons is
> only cosmetic — the rules below are what actually stop unauthorised writes.

## 6. Apply the Firestore security rules

1. **Firestore Database → Rules** tab.
2. Delete everything in the editor and paste the full contents of `firestore.rules`.
3. **Publish**.

What they do: anyone can read the portal content; only a signed-in user with an
`admins/{uid}` document can write; the `admins` collection can't be written from the
website at all; every other path is closed.

## 7. Upload the project to GitHub

Using the website, no command line needed:

1. <https://github.com> → **New repository**. Name it e.g. `bba-batch-2029`,
   set it to **Public**, don't add a README (you already have one) → **Create repository**.
2. On the empty repo page click **uploading an existing file**.
3. Drag in `index.html`, `firestore.rules`, `README.md` **and the `css` and `js` folders**
   (drag the folders themselves so the paths stay `css/style.css` and `js/app.js`).
4. Write a commit message like `Initial portal` → **Commit changes**.

If you prefer Git:

```bash
git init
git add .
git commit -m "Initial portal"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/bba-batch-2029.git
git push -u origin main
```

## 8. Enable GitHub Pages

1. In the repo: **Settings → Pages**.
2. Source: **Deploy from a branch**. Branch: `main`, folder: `/ (root)` → **Save**.
3. Wait about a minute, then reload. Your site is at:
   `https://YOUR-USERNAME.github.io/bba-batch-2029/`
4. Back in Firebase: **Authentication → Settings → Authorised domains → Add domain**
   and add `YOUR-USERNAME.github.io`, or sign-in will be blocked on the live site.

All paths in the code are relative, so the site works from that sub-folder URL with no changes.

## 9. Updating the website later

- **Content** — students, courses, timetable, events, achievements, gallery, societies,
  announcements, resources, committee — is edited at `/#/admin` on the live site.
  Changes appear for everyone within a second; there's nothing to re-upload.
- **Design or code** changes mean editing the file on GitHub (pencil icon → commit) or
  pushing a new commit. GitHub Pages rebuilds in under a minute.
- If an update doesn't show, hard-refresh: `Ctrl/Cmd + Shift + R`.

---

## Using the dashboard

Sign in at `/#/admin`, then:

- **Load sample content** copies the placeholder data into Firestore so you have something
  to edit rather than empty forms. It skips any collection that already has entries.
- Pick a collection on the left, then **Add entry**, **Edit** or **Delete**. Forms are built
  from the schema in `js/app.js`, so adding a field to a collection means editing one list there.
- Photos and files are stored as **links**, not uploads. Paste any public image URL
  (Google Drive share link, Imgur, university site). This keeps the project inside the
  free tier and off Firebase Storage.

Visitors never receive the dashboard: it is hidden, and any write they attempt is
rejected by the rules.

---

## Notes on the placeholder data

All students, instructors, committee members and society officers are invented
placeholders (`Student One`, `Placeholder name`). Replace them before sharing the link.
Ask people before publishing their photo, email or LinkedIn — a public GitHub Pages site
is indexed by search engines.

---

## Setup checklist

- [ ] Firebase project created
- [ ] Email/Password sign-in enabled
- [ ] Firestore database created
- [ ] `firebaseConfig` pasted into `js/firebase-config.js`
- [ ] Admin user created in Authentication
- [ ] `admins/{uid}` document created in Firestore
- [ ] `firestore.rules` pasted and published
- [ ] Files pushed to GitHub with folders intact
- [ ] GitHub Pages enabled on `main` / root
- [ ] `YOUR-USERNAME.github.io` added to Firebase authorised domains
- [ ] Signed in at `/#/admin` and loaded or entered real content
- [ ] Placeholder names replaced; permission taken for photos and emails
