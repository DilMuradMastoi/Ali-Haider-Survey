# Fieldwise — React + Firebase Research Survey

A modern bilingual (English / Urdu) research survey app based on the supplied 40-question questionnaire for Dera Murad Jamali.

## Features
- React + Vite, responsive polished UI, Lucide icons (no AI branding).
- Firebase Google sign-in.
- Six colour-coded survey sections.
- Required answers: users cannot move to the next question without answering the current one.
- No timer, score, marks, or percentage.
- Switch English/Urdu at any time without losing the current question or answers.
- One submission document per Firebase UID; submitted responses can be reviewed and edited.
- Officer analytics with per-question option counts and written-answer summaries.
- Contact form automatically uses the signed-in Google account's name and email.
- Owner can view all messages, send a message to a survey respondent, and set each message to Pending or Success. The message appears in the selected user's dashboard.
- Owner analytics counts completed survey submissions and counts each selected option for every question.

## 1. Install
Install Node.js LTS, then from this folder run:

```bash
npm install
```

## 2. Configure Firebase
1. Create a project at https://console.firebase.google.com/
2. Add a **Web App** in Project settings and copy its config values.
3. In **Authentication → Sign-in method**, enable **Google**.
4. In **Authentication → Settings → Authorized domains**, add `localhost` and your deployed domain.
5. Create a **Cloud Firestore** database.
6. Copy `.env.example` to `.env` and replace all `your_...` values with the Web App config values. Do not add a service-account private key to a browser `.env` file. Firebase web config is intended for client use; access is protected by Auth and Firestore rules.
7. Put officer Google email(s) into `VITE_OFFICER_EMAILS`, comma-separated.
8. Publish `firestore.rules` in Firebase Console → Firestore Database → Rules. **Also replace the example officer emails in the rules file with the same real officer emails.** Rules do not read Vite environment variables.
9. Restart the dev server after editing `.env`.

```bash
npm run dev
```

Open the local URL printed by Vite.

## 3. Officer setup and security
The UI's officer route checks the email list in `VITE_OFFICER_EMAILS`. Firestore rules independently restrict reading all submissions and messages. Change the sample email allowlist in both places. The rules allow an authorized officer to reply inside a user's message thread; publish the updated `firestore.rules` in Firebase Console → Firestore Database → Rules after changing the sample emails. For a production deployment, prefer Firebase Admin SDK custom claims set by a trusted server, and make `isOfficer()` check `request.auth.token.officer == true`; never allow users to grant themselves an officer role.

The included rules demonstrate the requested access boundaries, but should be reviewed and tested in Firebase Rules Playground before collecting real research data. The `create` rule prevents a second submission document for the same UID; `update` permits editing the existing response. The client checks before creating, but the Firestore rule is the enforcement layer.

## 4. Deploy
Build with `npm run build`. Deploy the `dist` folder to Firebase Hosting, Vercel, or another static host. Set the same `VITE_FIREBASE_*` and `VITE_OFFICER_EMAILS` variables in the hosting provider's environment settings. Add the deployed domain to Firebase Auth's authorized domains. Configure SPA fallback to `index.html`.

## Notes
- `.env.example` is included instead of a working `.env` because your actual Firebase project config and officer email addresses were not provided. Copy it to `.env` and fill your own values.
- This project includes English and Urdu labels/questions; have a fluent Urdu reviewer validate the translations before field research.
- The questionnaire includes open text fields for questions 39 and 40 in the uploaded source; this implementation follows the visible questionnaire wording. Question 13 and 14 include “Do not know” / “No transport cost” choices alongside amount entry.
- The app does not display marks or a score.


## Owner messages
The owner can send messages to users who have completed a survey (these users appear in the recipient list). The message is saved in Firestore with that user's UID, so it appears in the user's Messages page. The owner can change its status between Pending and Success. User messages are visible to the owner in the owner Messages page. No Gmail or Resend delivery is used.

Before deploying, replace `replace-with-owner@gmail.com` in `firestore.rules` with the exact owner Google email, publish the rules in Firebase Console, and set `VITE_OFFICER_EMAILS` to the same email in `.env` and your hosting environment.
