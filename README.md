# CAT — Change Africa Today — Website

This package contains your updated website with a **shared backend** for the "Client-Reported
Returns" section, plus an **admin review page** so submissions only go public after you
approve them.

Backend used: **Firebase** (Firestore for storage, Firebase Authentication for your admin
login). Both are free for this use case, need no server code, and work fine with a static
site hosted on GitHub Pages.

Files in this package:
- `index.html` — the public website
- `admin.html` — your private review dashboard (approve/reject submissions)
- `firestore.rules` — security rules enforcing the approval workflow
- `README.md` — this file

---

## 1. Create your Firebase project (5 minutes)

1. Go to https://console.firebase.google.com and sign in with a Google account.
2. Click **Add project**, name it (e.g. `cat-platform`), finish the wizard (Analytics can be
   skipped).
3. In the left sidebar: **Build → Firestore Database → Create database**.
   - Pick a location close to your users (e.g. `eur3`).
   - Start in **Production mode**.
4. Once created, go to the **Rules** tab, replace the contents with everything in
   `firestore.rules` from this package, and click **Publish**.

## 2. Turn on admin login

1. In the left sidebar: **Build → Authentication → Get started**.
2. Click the **Sign-in method** tab, enable **Email/Password**, save.
3. Go to the **Users** tab → **Add user**. Enter the email and password *you* (the admin)
   will use to log into `admin.html`. This is the only account that can approve/reject entries.
   You can add more admin accounts the same way later.

## 3. Get your config keys

1. **Project settings** (gear icon) → scroll to **Your apps** → click **</> (Web)** → register
   an app (any nickname, e.g. `cat-website`).
2. Firebase shows a code block with 6 values: `apiKey`, `authDomain`, `projectId`,
   `storageBucket`, `messagingSenderId`, `appId`. Copy them.

## 4. Paste your config into BOTH files

1. Open `index.html`, search for `firebaseConfig`, replace the placeholder values with your
   real ones.
2. Open `admin.html`, search for `firebaseConfig`, paste the **exact same** values in.
3. Save both files.

Until this is done, both pages still load fine — they just show a notice that the backend
isn't configured yet, instead of breaking.

## 5. Upload to GitHub & enable Pages

1. Create a GitHub repository (e.g. `cat-website`).
2. Upload `index.html` and `admin.html` to the root (drag-and-drop on GitHub's web UI, or
   `git add` / `commit` / `push`).
3. **Settings → Pages → Build and deployment → Source: Deploy from a branch** → branch `main`,
   folder `/ (root)` → **Save**.
4. Your public site appears at `https://yourusername.github.io/cat-website/` within a minute
   or two.
5. Your private admin page is at `https://yourusername.github.io/cat-website/admin.html`.
   **Important:** this URL is not secret — anyone who guesses or finds it can see the login
   screen. That's fine, because they still can't get in without your admin email/password,
   and Firestore rules block them from reading pending/rejected entries or changing anything
   even if they tried to call the database directly. Still, don't link to `admin.html` from
   anywhere public.

---

## How the review workflow works

1. A visitor fills out the form on the public site → their submission is saved with
   `status: "pending"`. It is **not yet visible** to other visitors.
2. You open `admin.html`, log in with your admin email/password.
3. You see the **Pending** tab with every new submission: name (already masked to "First L."),
   plan, claimed ROI, investment range, comment, and proof reference if they gave one.
4. Click **Approve** → it becomes public immediately on the main site (and counts toward the
   plan averages and the "Avg. ROI Reported by Clients" stat).
   Click **Reject** → it never appears publicly. You can still find it under the **Rejected**
   tab later if needed, and approve it afterwards if you change your mind.
5. You can also **unpublish** something you already approved, from the **Approved** tab.

Nothing is ever silently auto-published — every entry needs your explicit click before the
public sees it.

## Privacy & data notes

- Full names are never stored — the form masks them to "First L." in the browser, before
  anything is even sent to Firebase.
- Exact invested amounts are never collected, only a broad range the client picks themselves.
- The original content of a submission (name, ROI, comment, etc.) can't be edited by anyone,
  including you — only its status (pending/approved/rejected) can change. This keeps an honest
  record of what was actually submitted.
- If you ever need to permanently delete an entry (e.g. clear spam), `admin.html` doesn't have
  a delete button by default. You can delete it directly from the **Firebase console →
  Firestore Database → clientRoi collection**.

## If something doesn't work

- "Shared storage isn't configured yet" message → you haven't pasted your real Firebase
  config into that file yet (step 4).
- Can't log into `admin.html` → double check the email/password you created in step 2, and
  that Email/Password sign-in is enabled.
- Submissions never show up even after approving → check the browser console (F12) for errors,
  and double-check the rules were published exactly as given in `firestore.rules`.
