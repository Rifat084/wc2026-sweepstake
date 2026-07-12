# Beginner setup guide  
## Host the WC 2026 Sweepstake (GitHub Pages + Firebase live data)

You will end up with:

1. A **website link** anyone can open (no install).  
2. **Live standings** that update for everyone when **you** add a match.  
3. **Only you** can edit (Firebase login + security rules).  
4. The site is **unlisted** (not advertised to search engines). Anyone with the link can view.

**Time:** about 45–75 minutes the first time.  
**Cost:** free for this use.  
**Accounts needed:** GitHub (free) + Google/Firebase (free).

---

## Big picture (simple)

| Piece | What it does |
|--------|----------------|
| **GitHub** | Stores your website files |
| **GitHub Pages** | Turns those files into a public HTTPS link |
| **Firebase Authentication** | Your private admin login |
| **Firebase Firestore** | Shared live database of match results |
| **Security rules** | Public can *read*; only your email can *write* |

```
Friends with link  →  open website  →  READ standings (live)
You (admin)        →  sign in       →  WRITE new scores
```

> **Important honesty about “only people with the link”:**  
> GitHub Pages URLs are **public unlisted** (like an unlisted YouTube video).  
> Google is told not to index the page, and you never post the link publicly.  
> Anyone who *gets* the link can still view it — they just cannot edit.  
> True password-gated viewing needs a paid private host; for an office pool, unlisted + admin-only edit is the usual safe free setup.

---

# PART 1 — Create a GitHub account & put the site files online

## Step 1.1 — Create a GitHub account

1. Open [https://github.com/signup](https://github.com/signup)  
2. Sign up with email, choose a username/password.  
3. Verify your email if GitHub asks.

## Step 1.2 — Create a new repository (folder for the website)

1. After login, click the **+** (top right) → **New repository**.  
2. Fill in:
   - **Repository name:** `wc2026-sweepstake` (or any name without spaces)  
   - **Description:** optional, e.g. `Office WC 2026 live tracker`  
   - **Public** ← choose Public (required for free GitHub Pages)  
   - **Do NOT** tick “Add a README” (we already have files)  
3. Click **Create repository**.

You will see an empty repo page with setup instructions. Leave this tab open.

## Step 1.3 — Upload the website files

On your computer you need the folder:

```text
wc2026-site/
  index.html
  firestore.rules
  SETUP-GUIDE.md
  README.md
  .gitignore
  seed-data.json
```

### Easy way (no Git commands): upload in the browser

1. On the empty repo page, click **uploading an existing file**.  
2. Drag **all files inside** `wc2026-site` into the browser (not the outer folder name if it creates a nested folder — the repo root should contain `index.html`).  
3. At the bottom:
   - Commit message: `Initial site upload`  
   - Click **Commit changes**.

Check that on the repo home page you can see **`index.html`** at the top level (not inside another folder).

> If you accidentally uploaded a nested `wc2026-site/wc2026-site/index.html`, delete and re-upload so `index.html` is at the root.

## Step 1.4 — Turn on GitHub Pages (this creates your public link)

1. In your repo, click **Settings**.  
2. Left sidebar → **Pages**.  
3. Under **Build and deployment**:
   - **Source:** Deploy from a branch  
   - **Branch:** `main` (or `master`) → folder **/ (root)**  
4. Click **Save**.  
5. Wait 1–2 minutes, refresh the Pages settings page.  
6. You should see something like:

   **Your site is live at**  
   `https://YOUR_USERNAME.github.io/wc2026-sweepstake/`

7. Open that link. You should see the sweepstake standings.

**Bookmark this link** — this is what you share with the group.

> At this stage the site works, but live multi-user editing is not configured yet (you’ll still see a yellow “Setup needed” banner). Continue to Part 2.

---

# PART 2 — Create Firebase (live database + admin login)

## Step 2.1 — Open Firebase

1. Go to [https://console.firebase.google.com/](https://console.firebase.google.com/)  
2. Sign in with a **Google account** (use the email you want as admin).  
3. Click **Add project** / **Create a project**.  
4. Project name: e.g. `wc2026-sweepstake`  
5. Google Analytics: you can **disable** it (not needed).  
6. Create project → wait → **Continue**.

## Step 2.2 — Register a Web app

1. On the project overview, click the **</>** (Web) icon.  
2. App nickname: `sweepstake-web`  
3. **Do not** tick Firebase Hosting (we use GitHub Pages).  
4. Click **Register app**.  
5. You will see a config object like:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "wc2026-sweepstake.firebaseapp.com",
  projectId: "wc2026-sweepstake",
  storageBucket: "wc2026-sweepstake.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

6. **Copy these values** into a notepad. Click Continue to console.

## Step 2.3 — Enable Email/Password login

1. Left menu → **Build** → **Authentication**.  
2. Click **Get started**.  
3. Open the **Sign-in method** tab.  
4. Click **Email/Password** → enable the first switch → **Save**.

## Step 2.4 — Create your admin user

1. Still in **Authentication** → **Users** tab.  
2. Click **Add user**.  
3. Enter:
   - **Email:** the email only you will use (e.g. `you@gmail.com`)  
   - **Password:** a strong password (save it in a password manager)  
4. Click **Add user**.

This is the only account that will be allowed to edit scores.

## Step 2.5 — Create Firestore database

1. Left menu → **Build** → **Firestore Database**.  
2. Click **Create database**.  
3. Choose **Start in production mode** → Next.  
4. Pick a location close to you (e.g. `asia-south1` or `us-central`) → **Enable**.  
5. Wait until the database is ready.

## Step 2.6 — Paste security rules (critical for safety)

1. In Firestore, open the **Rules** tab.  
2. Replace everything with the contents of your file `firestore.rules`.  
3. **Change this line** to your real admin email (same as Step 2.4):

```
&& request.auth.token.email == "YOUR_ADMIN_EMAIL@example.com";
```

Example:

```
&& request.auth.token.email == "you@gmail.com";
```

4. Click **Publish**.

### What these rules do

| Who | Can view standings | Can change scores |
|-----|--------------------|-------------------|
| Anyone with the link | Yes | No |
| You (signed in with admin email) | Yes | Yes |
| Random internet users | Yes if they have link | No |
| Hackers without your password | No write access | Blocked by rules |

> Never put your password in the website code. Login happens through Firebase only.

---

# PART 3 — Connect the website to Firebase

## Step 3.1 — Edit `index.html` on GitHub

1. Open your GitHub repo.  
2. Click **`index.html`**.  
3. Click the pencil icon (**Edit this file**).  
4. Near the top of the `<script type="module">` section, find:

```js
const FIREBASE_CONFIG = {
  apiKey: "PASTE_API_KEY",
  authDomain: "PASTE_PROJECT_ID.firebaseapp.com",
  projectId: "PASTE_PROJECT_ID",
  storageBucket: "PASTE_PROJECT_ID.appspot.com",
  messagingSenderId: "PASTE_SENDER_ID",
  appId: "PASTE_APP_ID"
};
```

5. Replace each `PASTE_...` value with the real values from Step 2.2.  
   Keep the quotes. Example shape:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIzaSyBxxxxxxxx",
  authDomain: "wc2026-sweepstake.firebaseapp.com",
  projectId: "wc2026-sweepstake",
  storageBucket: "wc2026-sweepstake.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef123456"
};
```

6. Scroll down → **Commit changes**  
   Message: `Add Firebase config`

## Step 3.2 — Wait for Pages to rebuild

1. Wait ~1 minute.  
2. Hard-refresh your site (`Ctrl+Shift+R` / `Cmd+Shift+R`).  
3. The yellow banner should now say something like  
   **Firebase connected, cloud empty**  
   (not “Setup needed”).

If it still says Setup needed, double-check you didn’t leave any `PASTE_` text.

---

# PART 4 — Initialize live data & test as admin

## Step 4.1 — Sign in as admin

1. On your live site, click the **Admin** tab.  
2. Enter the email + password from Step 2.4.  
3. Click **Sign in**.  
4. Top-right badge should change to **Admin**.

## Step 4.2 — Upload the seed matches (one-time)

1. Still on Admin tab, click **Initialize cloud from seed**.  
2. Confirm.  
3. Badge should become **LIVE sync**.  
4. Match count should show **100 matches** (or similar).

## Step 4.3 — Test that friends see the same data

1. Open your site link in a **private/incognito** window (not signed in).  
2. You should see the same standings (Viewer mode).  
3. Back in your admin window, record a **test** match or a real SF result.  
4. In the incognito window, standings should update within 1–2 seconds **without refreshing**.

If that works — you’re done. Share the link with the group.

## Step 4.4 — Clean up a mistaken test match

- Admin can delete only matches whose id starts with `extra_` (user-added).  
- Seed matches stay unless you re-initialize carefully.  
- Prefer recording real semis only when they finish.

---

# PART 5 — Day-to-day use (after semis / final)

When a match finishes:

1. Open your site.  
2. **Admin** → sign in (if needed).  
3. Click a **suggested fixture** (France–Spain / England–Argentina) or fill the form.  
4. Enter scores (include AET goals in the score boxes).  
5. If it went to **penalties**, set **Winner** manually and note `pens 4-3`.  
6. Click **Record match (live for everyone)**.  
7. Everyone’s points table updates automatically.

### Points still applied automatically

| Result | Points |
|--------|--------|
| Group W/D/L | 2 / 1 / 0 |
| R32 / R16 / QF win | 4 / 6 / 8 |
| SF win / SF loss | 10 / 5 |
| 3rd place win | 7 |
| Final win / Final loss | 15 / 8 |

---

# PART 6 — Safety checklist (do these)

- [ ] Firestore rules published with **your** email only  
- [ ] Strong unique admin password  
- [ ] You did **not** share your admin password with the group  
- [ ] Site link shared only in private office chat / email  
- [ ] You did **not** post the link on public social media  
- [ ] Firebase Console → Project settings: only you have owner access  
- [ ] Optional: Authentication → only one user exists (you)

### What is protected

| Threat | Protection |
|--------|------------|
| Friend changes scores | Needs your password + rules block non-admin writes |
| Random person finds link | Can view only |
| Someone edits GitHub files | Needs your GitHub password (don’t share) |
| Password in page source | Not stored there — Firebase Auth handles login |

### What is *not* military-grade privacy

- The link is still a normal HTTPS public URL (unlisted, not secret like a bank vault).  
- Determined people who obtain the link can view names/teams/scores (expected for a pool).  
- Do not store money amounts, bank details, or private addresses on this site.

---

# PART 7 — Common problems

### “Setup needed” banner never goes away  
→ `FIREBASE_CONFIG` still has `PASTE_...` values, or you edited the wrong file / wrong branch.

### Login fails  
→ Email/Password provider not enabled, or wrong password, or user not created in Authentication → Users.

### “Missing or insufficient permissions” when saving  
→ Firestore rules still have `YOUR_ADMIN_EMAIL@example.com`, or the signed-in email doesn’t match the rules email **exactly** (case matters for some setups — use the exact address).

### Friends don’t see my new match  
→ You aren’t signed in, or cloud was never initialized, or they are looking at an old cached tab (hard refresh).  
→ Confirm top badge says **LIVE sync**.

### GitHub Pages 404  
→ `index.html` not at repo root, or Pages source branch wrong, or wait a few more minutes.

### I need to change Firebase config later  
→ Edit `index.html` on GitHub again → Commit → wait for Pages rebuild.

---

# PART 8 — Optional improvements later

| Idea | How |
|------|-----|
| Nicer URL | Buy a domain → point it to GitHub Pages |
| Second admin | Create another Auth user + add their email to `isAdmin()` rules with `\|\|` |
| Hide Admin tab from friends | In `index.html` set `SHOW_ADMIN_TAB_TO_EVERYONE = false` (you can still open Admin by temporarily setting it true, or always keep it true and rely on login) |
| Backup | Download Firestore data or keep `seed-data.json` + export after Final |

---

# Quick command summary (optional — advanced)

If you later install Git, you can update from your computer:

```bash
cd wc2026-site
git init
git add .
git commit -m "Update site"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/wc2026-sweepstake.git
git push -u origin main
```

Beginners can ignore this and keep using **GitHub’s web Upload / Edit** buttons.

---

## You’re finished when…

1. Site opens at `https://YOUR_USERNAME.github.io/wc2026-sweepstake/`  
2. Badge shows **LIVE sync**  
3. You can sign in as Admin  
4. A private window (not logged in) sees updates after you save a match  
5. Friends only need the link — no accounts  

If you get stuck on a specific step, note the **step number** and the **exact error message** on screen — that makes it easy to fix.
