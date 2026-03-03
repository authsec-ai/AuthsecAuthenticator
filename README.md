# AuthSec Authenticator

AuthSec Authenticator is an open source mobile authenticator app built with Expo and React Native. It is the reference mobile client for the [AuthSec](https://authsec.ai) identity platform, demonstrating how to integrate the AuthSec SDK to enable CIBA (Client-Initiated Backchannel Authentication) push approval flows alongside TOTP, biometric unlock, and OIDC/SAML login. Use it as a starting point to build your own authenticator app powered by the AuthSec SDK.

[![App Store](https://img.shields.io/badge/App_Store-Download-black?logo=apple)](https://apps.apple.com/us/app/authsec-authenticator/id6758098427)
[![Play Store](https://img.shields.io/badge/Play_Store-Download-green?logo=google-play)](https://play.google.com/store/apps/details?id=com.authsec.app)

**Download the app:**
- [Apple App Store](https://apps.apple.com/us/app/authsec-authenticator/id6758098427)
- [Google Play Store](https://play.google.com/store/apps/details?id=com.authsec.app)

## Features

- **TOTP Authentication** — Generate time-based one-time passwords via QR code scanning (compatible with Google Authenticator, Authy, etc.)
- **CIBA Flow** — Powered by the AuthSec SDK: receive a push notification for a login request and approve/deny from the notification bar or in-app popup with biometric verification
- **Biometric Authentication** — Face ID and fingerprint unlock
- **OIDC / SAML Login** — OAuth 2.0 / OpenID Connect and SAML-based enterprise login flows
- **Push Notifications** — Real-time authentication request delivery via the Expo Push Notification Service (FCM on Android, APNs on iOS — no Firebase required for iOS)
- **App PIN Lock** — Optional PIN-based app lock with biometric fallback
- **QR Code Scanner** — Scan TOTP secrets via camera
- **Dark / Light Theme** — Automatic theme based on system preference
- **Secure Storage** — Tokens and secrets stored in Keychain (iOS) / Keystore (Android)

## Prerequisites

- **Node.js** 20 or later
- **npm** 10 or later
- **EAS CLI** — `npm install -g eas-cli`
- **For iOS builds**: Xcode 15+ with iOS Simulator or a physical iOS device
- **For Android builds**: Android Studio with an emulator or a physical Android device
- **Firebase project** — required for push notifications (see [Firebase Configuration](#firebase-configuration))

## Setup

### 1. Clone and install dependencies

```bash
git clone https://github.com/YOUR_USERNAME/authsec-authenticator.git
cd authsec-authenticator
npm install
```

### 2. Push Notification Configuration

Push notifications are delivered via the **Expo Push Notification Service**. The underlying delivery mechanism differs per platform:

- **Android** — Expo uses **Firebase Cloud Messaging (FCM)**. You must provide a `google-services.json` from your Firebase project.
- **iOS** — Expo communicates directly with **APNs** using your Apple Push Notification key (`.p8`). No Firebase project is needed for iOS.

#### Android — `google-services.json`

1. Go to [Firebase Console](https://console.firebase.google.com/) and create a project
2. Add an **Android app** with your package name (default: `com.authsec.app`)
3. Download `google-services.json` and place it at the root of this project: `./google-services.json`

The `android.googleServicesFile` reference in `app.json` already points to this file. See `google-services.json.example` for the expected structure.

> This file is listed in `.gitignore` and will never be committed.

#### iOS — APNs Key

No Firebase file is required for iOS. Push notifications on iOS use APNs, managed through your Apple Developer account. Upload your APNs key (`.p8`) via EAS credentials:

```bash
eas credentials
```

EAS will handle APNs configuration automatically during the build process.

### 3. Configure your EAS project

The app uses EAS (Expo Application Services) for builds and push notification delivery.

1. Log in: `eas login`
2. Create or link a project: `eas init`
3. Replace `YOUR_EAS_PROJECT_ID` in `app.json` with your actual project ID:
   ```json
   "extra": {
     "eas": {
       "projectId": "your-actual-project-id"
     }
   }
   ```
4. Search for `YOUR_EAS_PROJECT_ID` in the source files and replace with the same value:
   - `App.tsx`
   - `src/screens/LoginScreen.tsx`
   - `src/screens/EndUserLoginScreen.tsx`
   - `src/screens/DeviceRegistrationScreen.tsx`
   - `src/screens/OIDCSAMLLoginScreen.tsx`

### 4. Configure the backend URL

Update the `BASE_URL` in `src/services/api.ts` to point to your AuthSec backend:

```typescript
export const BASE_URL = 'https://your-backend.example.com';
```

For local development, use your machine's local IP:

```typescript
export const BASE_URL = 'http://192.168.1.YOUR_IP:7468';
```

## Running Locally

```bash
npx expo start
```

Press `i` to open the iOS Simulator, `a` for the Android emulator, or scan the QR code with Expo Go on a physical device.

To run directly on a simulator/device (requires native prebuild):

```bash
npx expo run:ios      # Requires Xcode
npx expo run:android  # Requires Android Studio
```

> Push notifications and biometric authentication require a **physical device**.

## Building for Production

This project uses EAS Build for cloud-based native builds.

```bash
# Development build (for testing on a physical device)
eas build --profile development --platform ios
eas build --profile development --platform android

# Preview / internal distribution (.apk for Android)
eas build --profile preview --platform android

# Production (App Store / Play Store)
eas build --profile production --platform ios
eas build --profile production --platform android
```

## Configuration Notes

### Bundle ID / Package Name

The default is `com.authsec.app` for both iOS and Android. To use your own, update `ios.bundleIdentifier` and `android.package` in `app.json`, then regenerate the native projects:

```bash
npx expo prebuild --clean
```

### Android native project

The `android/` folder contains the pre-generated native Android project. Build artifacts and Gradle caches are excluded from this repo (see `.gitignore`). If you need to regenerate from scratch:

```bash
npx expo prebuild --platform android
```

### Apple Push Notification Key (.p8)

APNs keys are managed by EAS credentials — you do not need to include an `.p8` file in the project. Upload your APNs key through the Expo credentials manager:

```bash
eas credentials
```

## Tech Stack

| Category | Technology |
|---|---|
| Framework | Expo 54 + React Native 0.81.5 |
| Language | TypeScript |
| Navigation | React Navigation 7 |
| TOTP | otplib 13 |
| Secure storage | expo-secure-store |
| Biometrics | expo-local-authentication |
| Push notifications | Expo Push Service (FCM for Android, APNs for iOS) |
| HTTP client | axios |
| QR scanning | expo-camera |
| Auth sessions | expo-auth-session |

## License

MIT
