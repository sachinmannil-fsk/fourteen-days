# Setting up live mode

Live mode lets each team submit their round from their own laptop. The mentor screen stays in charge — it shows who has submitted, and the mentor can still override anything.

**You do not need this to run the session.** Without it, everything works exactly as before, with the mentor entering cards. Set this up only if you want teams submitting themselves.

**Time needed:** about fifteen minutes, once.

---

## Part 1 — Create the Firebase project

Firebase is a free Google service that lets the laptops talk to each other. The free tier is far more than this session needs.

1. Go to **console.firebase.google.com** and sign in with a Google account.
2. Click **Create a project**.
3. Name it `fourteen-days`. Continue.
4. **Turn Google Analytics off** — you don't need it. Create project.
5. Wait for it to finish, then click **Continue**.

---

## Part 2 — Create the database

1. Navigate to **Databases & Storage** → **Realtime Database**.
   *(Depending on your console version this may sit under **Build** instead. Either way: **Realtime Database**, not Cloud Firestore. They are different products and the app needs this one.)*
2. Click **Create Database**.
3. Choose a location near you. **You cannot change this later.** Next.
4. Select **Start in test mode**. Enable.

You now have an empty database.

---

## Part 3 — Set the access rules

**Do this straight away.** If test-mode rules are left untouched, Firebase emails a warning and then starts denying all requests — which would break the app mid-term.

1. In Realtime Database, click the **Rules** tab.
2. Delete what's there and paste this exactly:

```json
{
  "rules": {
    "rooms": {
      "$code": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

3. Click **Publish**.

**What this means:** anyone who knows a four-letter room code can read and write that room. The only data stored is team names and card choices for one lesson — no student names, no personal information. Rooms are created fresh each session. This is an acceptable trade for a classroom game, but don't put anything sensitive in team names.

---

## Part 4 — Get your settings

1. Click the **gear icon** (top left) → **Project settings**.
2. Scroll to **Your apps**. Click the **web icon** `</>`.
3. App nickname: `fourteen-days`. **Do not** tick Firebase Hosting. Register app.
4. You'll see a block of code. Copy the part inside the curly braces — it looks like this:

```
apiKey: "AIzaSy...",
authDomain: "fourteen-days-xxxx.firebaseapp.com",
databaseURL: "https://fourteen-days-xxxx-default-rtdb.firebaseio.com",
projectId: "fourteen-days-xxxx",
...
```

**Check that `databaseURL` is in the list.** If it isn't, you created Firestore instead of Realtime Database — go back to Part 2.

Your URL will end in either `.firebaseio.com` (us-central1) or `.firebasedatabase.app` (all other regions). Both are fine.

---

## Part 5 — Paste it into the app

1. Open `index.html` in a text editor (Notepad, TextEdit, or edit it directly on GitHub).
2. Near the top, find:

```js
const FIREBASE_CONFIG = {
  // apiKey: "...",
  // authDomain: "...",
  // databaseURL: "...",
  // projectId: "..."
};
```

3. Replace everything between `{` and `}` with your settings. Remove the `//` marks. The result should look like:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIzaSy...",
  authDomain: "fourteen-days-xxxx.firebaseapp.com",
  databaseURL: "https://fourteen-days-xxxx-default-rtdb.firebaseio.com",
  projectId: "fourteen-days-xxxx"
};
```

4. Save, and upload to GitHub as usual.

> **Is it safe to have these in a public repository?** Yes. Firebase web settings are designed to be public — they identify your project, they aren't passwords. Your rules control what anyone can actually do.

---

## Part 6 — Test it before the lesson

Test with two browser windows on your own laptop first.

**Test on school wifi as well as at home.** Some school networks block outside services, and you want to find that out now rather than mid-lesson.

**Window 1 (mentor):**
1. Open your site
2. Enter team names
3. Click **Go live** — a four-letter code appears in the top bar
4. Click **Begin**, step through the opening, then **Enter cards**

**Window 2 (team):**
1. Open the same site
2. Click **open the team screen**
3. Type the code, pick a team
4. Choose some cards, click **Submit round**

**Back in window 1:** that team should now show **submitted ✓**.

If it does, you're ready.

---

## Running the session live

1. Enter team names, click **Go live**, write the code on the board.
2. Teams open the site and click **open the team screen**, or go straight to `your-site-url/?team`.
3. Each round: click **Enter cards** — this opens the round to the teams and starts the timer.
4. Watch the submitted list. Click **Score round** when everyone's in, or when the timer runs out.
5. Late or missing teams: their choices are simply whatever they submitted. You can still open any team and change it yourself before scoring.

---

## If it fails on the day

**This is the important part.** If the network drops or a team can't join, **just enter their cards yourself**. The entry screen works exactly as it always did. Nothing is lost, and the session continues.

Tell mentors this in advance so nobody panics.

---

## Running several classes at the same time

Firebase's free tier allows **100 simultaneous connections**, where a connection is one browser tab.

| Setup | Tabs | |
|---|---|---|
| One class | 6 | fine |
| 7 classes, **one laptop per team** | 42 | fine |
| 7 classes, every student joins | 175 | **over the limit — joins get rejected** |

**So "one laptop per team" is a capacity requirement, not just a tidiness one.** Nominate one student per team to be the scribe, and say so when you send the link.

**If connections do climb near the limit**, the fix is to create a second Firebase project and run some classes from a second copy of the app. You are very unlikely to need this — but check **Realtime Database → Usage** in the Firebase console five minutes into your first live session. If it reads around 42, you are fine and can stop thinking about it.

## Between sessions

Rooms are created fresh each time you click **Go live**, so old ones don't interfere. If you want to clear them out, delete the `rooms` node in the Firebase console occasionally. Nothing breaks if you don't.
