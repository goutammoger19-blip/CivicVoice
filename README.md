# CivicVoice — Enhanced Digital Suggestion Box

Light Liquid Glass college-project prototype using HTML, CSS, JavaScript, Firebase Authentication, and Cloud Firestore (no Firebase Storage required).

## Included features

- Citizen email/password registration and sign-in
- Submit civic suggestions and receive a reference ID
- Track your own submissions after signing in
- Community suggestion feed with one support/like per signed-in account
- Manual locality / ward / street selection through a text field
- Normal / High / Urgent priority and a public-safety flag
- In-app notifications when an administrator changes status or replies
- English / Kannada toggle for the main landing-page text
- Admin-only dashboard with summary counts, search and filters
- Admin status updates and replies to citizens
- Emergency disclaimer and clear prototype limitations

## 1. Open the project

1. Extract the ZIP file.
2. Open the extracted `CivicVoice-Enhanced` folder in VS Code.
3. Open `index.html` and choose **Open with Live Server**.
4. The page can render before Firebase is connected, but cloud features require the setup below.

## 2. Configure Firebase

Use the Firebase project you already created for CivicVoice if you still have access to it.

1. Open https://console.firebase.google.com/ and select your CivicVoice project.
2. In Project Overview, open the Web app settings and copy the Firebase web configuration.
3. Open `firebase-config.js` in VS Code and replace every `PASTE_...` value. Do not paste a service-account private key here.
4. In **Build → Authentication → Sign-in method**, enable **Email/Password**.
5. In **Build → Firestore Database**, create/use your database. Open **Rules**, replace the rules with the full contents of `firestore.rules`, then click **Publish**.
6. In **Build → Storage**, get started / create the default bucket. Open Storage **Rules**, replace them with the full contents of `storage.rules`, then **Publish**. 
7. In Authentication → Users, create an admin account. Copy its UID.
8. In Firestore → Data, create a collection named `users`. Create a document whose document ID is exactly that admin UID, with a field `role` (string) = `admin`.
9. Refresh the website and test citizen registration first. Then sign in through the Admin button using the admin account.

If you already created a Firestore database and admin user for the earlier CivicVoice version, keep them; just publish the new rules and add/verify the `users/{admin-UID}` role document.

## 3. Test the features

1. Register a citizen account using an email and password of at least 6 characters.
2. Submit a suggestion with a title, category, locality and description, then set priority / safety flag.
3. Check that a reference ID appears and the submission is shown in Track your suggestions.
4. Open Community Voices and press the support button. A signed-in user can support a particular suggestion once.
5. Sign out of the citizen account and sign in using the administrator account.
6. In the admin dashboard, search/filter submissions, change a status and save an administrator reply.
7. Sign back in as the citizen. The status/reply and in-app notification should appear.

## Database collections

- `users/{uid}` — trusted role document; create admin role manually in Firebase Console.
- `suggestions/{suggestionId}` — submission, owner UID, status, priority, reply and support count.
- `suggestions/{suggestionId}/supports/{uid}` — one support record per authenticated user.
- `notifications/{notificationId}` — in-app status/reply notifications for a citizen.

## Important implementation notes

- Location is a manually entered locality / ward / street, not GPS or a map pin.
- Notifications are **in-app Firestore notifications**. Email, SMS, browser push and WhatsApp notifications are not included; those require a trusted backend such as Cloud Functions and a provider/service configuration.
- Kannada toggle translates the main headline and key call-to-action labels. Some form labels, admin controls and descriptive text remain in English and can be translated in a later localization pass.
- Analytics are the admin summary counts (total, pending, under review and resolved) plus searchable/filterable records. It does not yet export charts or reports.
- Emergency flag is only a marker for administrator attention. This website is not monitored as an emergency service. Contact the appropriate emergency service for immediate danger.
- This is a college prototype, not an official government portal. It does not automatically deliver reports to a municipality or guarantee resolution.

## Security and privacy

- Firestore rules allow public reads of suggestion documents so the community feed works. Do not add citizen email, phone number, home address or other private data to suggestion documents. The app stores the Firebase UID for owner-only tracking; treat it as an identifier.
- Signed-in citizens can create their own suggestions and read their own notifications. Admins can read/manage suggestions and create notifications.
- Admin role documents must only be granted manually by the trusted project owner. Do not create a public registration flow for admin roles.
- This version does not use Firebase Storage. Citizens submit text-based suggestions, so you do not need to upgrade your Firebase plan for photo storage.
- Before real public deployment, add Firebase App Check, abuse/spam controls, a privacy notice, data retention rules and formal approval from the relevant organization. Test all rules with the Firebase Emulator / Rules Playground.

## Project structure

```text
CivicVoice-Enhanced/
├── index.html
├── styles.css
├── app.js
├── firebase-config.js
├── firestore.rules
├── storage.rules
└── README.md
```


## Important: publish Firestore rules
This text-only version still requires Firestore security rules. In Firebase Console open Firestore Database → Rules, replace the editor contents with the included `firestore.rules`, then click Publish. Do not paste these rules into Firebase Storage. The app writes `imageUrl: ""` for text-only suggestions so the document matches the required Firestore fields. Keep your existing Firebase Web App config in `firebase-config.js`.
