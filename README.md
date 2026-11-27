# League Auction: host on GitHub and publish on Google Play

This folder is the complete app. It runs on GitHub Pages (free). Live viewing uses Firebase (free plan), and the Play Store app is made from the website with PWABuilder.

Total time: about 1 hour for the website and live viewing, then at least 14 days of testing before Google Play lets you publish (explained in Part 4).

## Files in this folder

| File | What it is |
| --- | --- |
| `index.html` | The whole app |
| `firebase-config.js` | Your Firebase settings (Part 2) |
| `manifest.webmanifest`, `sw.js`, `icons/` | Make the site installable and usable offline (needed for the Play Store app) |
| `firestore.rules` | Security rules you paste into Firebase |
| `privacy.html` | Privacy policy page (Google Play requires one). Fill in the parts in [BRACKETS] |
| `feature-graphic-1024x500.png` | Banner image for your Play Store listing |

---

## Part 1: Put the site on GitHub Pages

1. **Create a GitHub account** at https://github.com/signup if you don't have one.

2. **Create the repository.** Click **+** (top right) > **New repository**.
   - Repository name: **`YOUR-USERNAME.github.io`** (your exact GitHub username, then `.github.io`).
   - Set it to **Public**. Click **Create repository**.
   - Why this name: it puts the site at the root of `https://YOUR-USERNAME.github.io`, which the Play Store app needs in Part 3.

3. **Upload the files.** In the new repository, click **uploading an existing file** (or **Add file > Upload files**). Drag in everything from this folder, including the `icons` folder. Click **Commit changes**.

4. **Add an empty `.nojekyll` file.** Click **Add file > Create new file**, name it `.nojekyll` (with the dot), leave it empty, and click **Commit changes**. Without it, GitHub hides the `.well-known` folder you'll add in Part 3.

5. **Turn on GitHub Pages.** Go to **Settings > Pages**. Under **Build and deployment**, set Source to **Deploy from a branch**, Branch to **main** and folder **/(root)**, then click **Save**.

6. **Open your site.** After a minute or two it's live at `https://YOUR-USERNAME.github.io`. You can already run auctions on it. Live viewing starts working after Part 2.

To change any file later, open it on GitHub, click the pencil icon, edit and click **Commit changes**. The site updates within a couple of minutes.

---

## Part 2: Set up live viewing with Firebase

1. Go to https://console.firebase.google.com and sign in with a Google account. Click **Create a project**, give it a name (for example `league-auction`), and finish the steps. You can turn Google Analytics off.

2. **Create the database.** In the left menu open **Build > Firestore Database > Create database**. Pick a location near your players (for example `asia-south1 (Mumbai)` in India), choose **Start in production mode**, and click **Create**.

3. **Add the security rules.** In Firestore, open the **Rules** tab. Delete what's there, paste the whole contents of `firestore.rules`, and click **Publish**. These rules let anyone watch and sign up, but only an auction's owner can change it, see its sign-ups, or open its saved data. **Whenever you download a new version of the app, paste the rules again** in case they changed. Old rules make saving to accounts or sign-ups fail.

4. **Turn on sign-in.** Open **Build > Authentication > Get started > Sign-in method** and turn on these providers, saving each one:
   - **Email/Password**: organizers create accounts with an email and password.
   - **Google** (optional): lets organizers tap "Continue with Google". Pick a support email when asked.
   - **Anonymous**: lets players sign up and lets people run an auction without an account. Viewers never need to sign in.

5. **Allow your site's address.** In **Authentication > Settings > Authorized domains**, click **Add domain** and add `YOUR-USERNAME.github.io`.

6. **Get your settings.** Click the gear icon > **Project settings**. Under **Your apps**, click the web icon **</>**, give the app a nickname and click **Register app**. Firebase shows a `firebaseConfig` block with values like `apiKey`, `projectId` and `appId`.

7. **Paste them into `firebase-config.js`.** On GitHub, open `firebase-config.js`, click the pencil icon, and copy each value from Firebase into the matching line, keeping the quotes and commas:

   ```js
   window.FIREBASE_CONFIG = {
     apiKey: "AIza...",
     authDomain: "league-auction.firebaseapp.com",
     projectId: "league-auction",
     storageBucket: "league-auction.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abc123"
   };
   ```

   Click **Commit changes**. These values aren't passwords; the security rules from step 3 are what protect your data. You can also paste Firebase's own `const firebaseConfig = { ... };` block instead, but **not** the `import` lines above it.

   Because the settings live in their own file, you can replace `index.html` with a newer version at any time without re-entering them. If this file has a typo, the app still works and shows a yellow notice that live features are off.

8. **Test it.**
   - On your laptop, open the site, choose **Run an auction**, add two teams and a few players, and start the auction.
   - Click **Live to viewers** at the top and **Copy link**.
   - Open that link on your phone. You should see the auction; draw a player and place a bid on the laptop and watch the phone update.

The free Firebase plan has daily limits on reads and writes. A local league auction with a normal-sized audience fits comfortably, but if hundreds of people watch at once, check the usage page in the Firebase console.

---

## Part 3: Turn the site into an Android app (PWABuilder)

1. Go to https://www.pwabuilder.com, enter `https://YOUR-USERNAME.github.io` and click **Start**. It checks your manifest, icons and offline support (all included).

2. Click **Package for stores**, then **Generate package** under **Android**.

3. Fill in the options:
   - **Package ID**: a unique name such as `io.github.yourusername.auction`. You can never change it after publishing.
   - **App name**: `League Auction` (or your own name).
   - **Signing key**: choose **Create new**, enter your name and organization, and keep the passwords it shows.

4. Click **Download**. The zip contains:
   - an `.aab` file: this is what you upload to Google Play,
   - an `.apk` file: install this on your own phone to test,
   - `assetlinks.json`,
   - your **signing key** and a text file with its passwords.

   **Back up the signing key and passwords somewhere safe** (for example a private cloud folder). You need them for every future update.

5. **Add `assetlinks.json` to your site.** This proves the app and the website belong together, so the app opens full screen without a browser address bar.
   - On GitHub: **Add file > Create new file**.
   - Type the name `.well-known/assetlinks.json` (typing the `/` creates the folder).
   - Paste the contents of the `assetlinks.json` from the PWABuilder zip and click **Commit changes**.
   - Check that `https://YOUR-USERNAME.github.io/.well-known/assetlinks.json` opens in a browser.

6. Install the `.apk` on your Android phone to try the app.

---

## Part 4: Publish on Google Play

### Create your developer account

1. Go to https://play.google.com/console and sign up with the Google account that will own the app.
2. Pay the one-time US$25 registration fee and complete identity verification (a government ID may be requested).
3. Choose **Personal** or **Organization**. New personal accounts must run a closed test with at least 12 testers for 14 days in a row before they can publish publicly (below). Organization accounts skip that but need a D-U-N-S number.

### Create the app

1. In Play Console click **Create app**. Enter the app name, default language, choose **App** and **Free**, accept the declarations and click **Create app**.

2. Complete the **Dashboard** setup tasks:
   - **Privacy policy**: first fill in the [BRACKETS] in `privacy.html` on GitHub, then enter `https://YOUR-USERNAME.github.io/privacy.html`.
   - **App access**: all functionality is available without special access.
   - **Ads**: no ads.
   - **Content rating**: answer the questionnaire (a sports auction tool with no violence or user chat).
   - **Target audience**: 18 and over is the simplest choice.
   - **Data safety**: the app stores the names and prices organizers enter and shares them with viewers through Firebase; it uses an anonymous app ID; no data is sold or used for ads. Use `privacy.html` as your guide when answering.
   - **Store listing**: a short description, a full description, the 512×512 app icon (`icons/icon-512.png`), the feature graphic (`feature-graphic-1024x500.png`), and at least 2 phone screenshots (take them on your phone from the test app).

### Closed test (new personal accounts)

1. Go to **Test and release > Testing > Closed testing** and create a track.
2. Add testers: create an email list with **at least 12 people** (more is safer, as some drop out). Their emails must be the Google accounts they use on Android.
3. **Create release**, upload the `.aab` from PWABuilder, add a short release note, and roll it out.
4. Send testers the opt-in link from the track's **Testers** tab. Each tester must accept, then install the app from the Play Store.
5. Keep at least 12 testers opted in for **14 days in a row**. Ask them to actually use it, for example to watch a practice auction.

### Go live

1. After 14 days, open the **Dashboard** and click **Apply for production access**. Answer the questions about your test honestly and in detail.
2. Once approved, go to **Production > Create new release**, upload the `.aab` (or a newer one), and send it for review. Review usually takes a few days.

### After your first upload: fix the address bar

Google Play re-signs your app with its own key, so you need to add that key to `assetlinks.json` too:

1. In Play Console open **Test and release > App integrity** (or **Setup > App integrity**), under **App signing**.
2. Copy the **SHA-256 certificate fingerprint** of the **App signing key certificate**.
3. On GitHub, edit `.well-known/assetlinks.json` and add it to the `sha256_cert_fingerprints` list, after the one already there, separated by a comma:

   ```json
   "sha256_cert_fingerprints": [
     "AA:BB:...:ZZ",
     "PASTE:THE:PLAY:FINGERPRINT:HERE"
   ]
   ```

4. Commit. Reinstall the app from Google Play; it should open without the browser address bar.

---

## Updating the app later

- **Changes to the app itself** (editing `index.html`): commit them on GitHub. Both the website and the Play Store app pick them up automatically, because the app loads your site. No new Play Store release is needed.
- **Changes to the Android wrapper** (app name, icon, package settings): generate a new package in PWABuilder **using your existing signing key**, increase the version number, and upload the new `.aab` as a new release.

## Organizer accounts and saved auctions

1. On the site or app, tap **Organize an auction** and create an account (email and password, or Google).
2. **My auctions** lists every auction you've created. Search by name, or filter by **Live**, **Setting up** or **Finished**.
3. **New auction** starts a fresh one. To reuse last season's teams, logos and bidding rules, pick it under **Copy teams and settings from**. Players aren't copied; share the new sign-up link instead.
4. Everything is saved to your account as you work, so you can sign in on another phone or laptop and carry on. Use **My auctions** at the top to switch between auctions.
5. Finished auctions stay in the list, so you can reopen them to see squads or download the PDF. **Delete** removes one for good.
6. **Browse live auctions** shows what viewers see: every live auction on the site.
7. Sign out from the bottom of **My auctions**, or from the **⋯** menu inside an auction.

Prefer not to have an account? Choose **Continue without an account** on the sign-in screen; the auction then stays on that device only, as before. If you sign in later, that auction is added to your account automatically.

Run each auction from one device at a time. Two devices editing the same auction at once can overwrite each other's changes.

## Player sign-up

Every auction gets its own sign-up link automatically.

1. Create the league and teams in **Teams**. The link appears there and at the top of **Players**. Copy it or tap **Share on WhatsApp**.
2. Players open the link, enter their name, age, role and an optional photo, and tap **Sign up**. No account is needed.
3. Each sign-up goes straight into your player list at the default base price, ready for the auction. Change any player's price right in the list, or remove anyone you don't want.
4. Players who sign up after the auction has started are added to the pool too, so they come up in later draws.
5. Prefer to check each one first? Untick **Add sign-ups to the player list automatically**. New sign-ups then wait under **Waiting for a base price** until you add or decline them.
6. Click **Close sign-up** when you're done. You can still add or edit players yourself at any time.

Sign-ups are added on the device that runs the auction, whenever the app is open there. Sign-ups made while it's closed are added the next time you open it.

## Using the app on auction night

- Run the auction on one laptop or phone. Teams, players and results are saved in that browser; use **⋯ > Save backup** to move them to another device.
- Make sure the top button says **Live to viewers**, and share the link from that button.
- Viewers can also open the site or the Play Store app, tap **Watch a live auction**, and search for your league.
