# Dinner planner: setup guide (Firebase + GitHub Pages)

Everything here is free and needs no credit card. Total time: about 20 minutes. The Firebase and GitHub screens change over time, so button wording may differ slightly.

You need: a Google account (for Firebase) and a GitHub account (for hosting).

## 1. Create the Firebase project
1. Go to https://console.firebase.google.com and sign in.
2. Click **Create a project**, name it (for example `dinner-planner`). Turn Google Analytics **off**. Create.

## 2. Create the database
1. In the left menu choose **Build > Firestore Database** and click **Create database**.
2. Pick a location close to you (a European one, for example). **This cannot be changed later.**
3. Choose **production mode** and finish.

## 3. Set the access rules
1. In Firestore, open the **Rules** tab.
2. Replace everything with the rules below and click **Publish**.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null
        && request.auth.token.email == "koti@ruokaa.app";
    }
  }
}
```

What this means: only the one shared household account (set up in the next step) can read or change data. Anyone with the link just sees a password box.

### 3b. Create the household password
1. In Firebase open **Build > Authentication**, click **Get started**, then **Sign-in method > Email/Password**, enable it and save.
2. Open the **Users** tab, click **Add user**, use the email `koti@ruokaa.app` (it does not need to be a real address; it must match `householdEmail` in `firebase-config.js` and the rules above) and choose your password.
3. Each phone asks for the password once and then stays signed in. To change the password, edit the user in the Users tab.

## 4. Get your Firebase settings
1. Click the gear icon, **Project settings**, then scroll to **Your apps**.
2. Click the web icon `</>`, give the app a name and register it (do not set up Firebase Hosting).
3. Copy the `firebaseConfig` values shown.
4. Open `firebase-config.js` in a text editor and replace each `PASTE_HERE` with the matching value. Keep the quotes. Save.

## 5. Put the app on GitHub Pages
1. On https://github.com click **New repository**. Name it (for example `dinner`), set it to **Public**, create it.
2. Click **uploading an existing file** (or Add file > Upload files) and drag in **all** files from the `dinner-planner-site` folder: `index.html`, `firebase-config.js`, `manifest.webmanifest` and the three `.png` icons. Commit.
3. Go to **Settings > Pages**. Under **Build and deployment** choose **Deploy from a branch**, branch `main`, folder `/ (root)`, Save.
4. After a minute or two the page shows your link, like `https://yourname.github.io/dinner/`.

The repository is public but only holds the app's code. Your recipes live in Firestore, not in GitHub.

## 6. Open it on both phones
- **iPhone (Safari):** open the link, tap Share, then **Add to Home Screen**.
- **Android (Chrome):** open the link, tap the menu, then **Install app** or **Add to Home screen**.

## 7. Check it works
Add a recipe on one phone. It should appear on the other within a second or two.

## Backups
At the bottom of the Recipes tab, open **Backup**. **Export backup** downloads a JSON file with all recipes and the plan. **Import backup** loads such a file back. It adds or overwrites items with the same id and never deletes anything. Do an export once in a while.

## Good to know
- Free limit (Firebase Spark plan): the docs give 20,000 writes and 50,000 reads per day as an example Firestore quota. That is far above household use.
- The app remembers data on the phone, so it still opens with your recipes when offline, and changes sync when you're back online. The page itself still needs a connection to load the first time.
- To update the app later, upload the new `index.html` to the repository again (Add file > Upload files, same name).
