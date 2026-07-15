# Shipping GameOS to Google Play

GameOS is **100% offline** — no backend, no accounts, no network calls,
no data collection. That makes the Play Store data-safety and content-rating
steps about as easy as they get.

## 1. Before you build

- [ ] **Change the package id.** In `app.json` set:
  - `expo.android.package` → your real reverse-domain, e.g. `com.yourname.gamehub`
  - `expo.ios.bundleIdentifier` → same idea for iOS (`com.yourname.gamehub`)
  - `expo.name` is the display name on the device ("GameOS").
  Two apps can't share a package id, so make it yours.
- [ ] **App signing.** EAS generates a keystore for you on first build. Keep the
  credentials it prints — you'll need them for every future update of this app.
- [ ] **Privacy policy URL.** Google requires one even with no data collection.
  The cheapest valid option: create a public GitHub repo, add `PRIVACY.md`
  (template below), enable **Pages**, and use that URL.
- [ ] **Content rating = Everyone.** Do **not** opt into *Designed for Families* —
  that program adds ad/safety obligations you don't need here.
  Fill the rating questionnaire honestly (no user data, no ads).

## 2. Build the AAB

```bash
cd gamehub
npm install
npx eas login            # one-time; creates the build on EAS
npx eas build -p android --profile production
```

`eas.json` is already configured: `production` builds an **Android App Bundle (.aab)**,
which is what Play Console wants. Download the `.aab` from the EAS dashboard
when the build finishes, then upload it to Play Console → **Production** → **Create release**.

Other profiles (already in `eas.json`):
- `preview` → an `.apk` for quick side-loading / internal testing.
- `development` → a dev-client build.

## 3. Store listing essentials

- **Title:** GameOS
- **Short description:** 10 classic childhood games, fully offline. No sign-up, no ads.
- **Category:** Games → Puzzle / Casual
- **Screenshots:** run `npx expo start --android` on a device/emulator, capture the
  Hub + a few games (Tic-Tac-Toe, Snake, 2048, Memory, Simon).
- **Icon / feature graphic:** already generated in `assets/` (`icon.png`, `splash.png`,
  adaptive `android-icon-*`).

## 4. Minimal privacy policy (copy to `PRIVACY.md`)

> **GameOS Privacy**
>
> GameOS is a collection of offline mini-games. The app:
> - does **not** connect to any server;
> - does **not** collect, transmit, or store any personal information;
> - stores game scores and your settings **only on your device** using local storage,
>   and you can clear them anytime from Settings → Reset all scores.
>
> No analytics, no advertising SDKs, no third-party data sharing.

## 5. Notes / gotchas

- Scores live in `AsyncStorage`; uninstalling the app erases them (expected).
- The app targets Android; iOS works too (`eas build -p ios`) if you add an Apple
  dev account — only Android/Play Store was requested for v1.
- First launch shows a branded splash (configured via the `expo-splash-screen` plugin in `app.json`).
