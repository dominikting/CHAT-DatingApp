# BOND — first version and Meta setup

BOND is a responsive, interactive prototype of friend-led dating. It uses fictional adult profiles, browser-local data, and a “View as” control to demonstrate each participant’s experience. It is not yet a production dating service or a native mobile app.

## Open the app

Clone this repository or download and extract its ZIP, then open `index.html` in a current browser. Keep `style.css` and `app.js` alongside it. Photos and fonts load from the internet. If your browser restricts local-file storage, serve the folder using a local web server. With Python installed, run `python -m http.server 8765` from the extracted folder and visit `http://localhost:8765`.

## Try both scenarios

**Scenario 1 — one matchmaker**

1. Start as Jules. Choose **Make a match**, leaving **Just me** selected.
2. Select Emma and Alex. Add why they might connect, and optionally write the introduction. A blank introduction uses the generic template.
3. Create the introduction. Use **View invitations** to preview it as either candidate, or use **View as** in the top bar.
4. As Emma, choose **Look at my introduction** and accept. Repeat as Alex. Neither can enter the conversation until both accept.
5. Open the introduction thread, which includes Jules. Candidates can separately choose **Just the two of us**.

**Scenario 2 — two matchmakers**

1. As Jules, choose **Team up with a friend**. Sam is the second matchmaker.
2. Nominate Sophia as Candidate A and Daniel as Candidate B. Each belongs to the corresponding sample friend circle.
3. Switch to Sam. In **My matches**, choose **Agree & nominate Daniel** or decline the proposal.
4. After agreement, switch to Sophia and Daniel to accept or decline. Both must accept before the introduction opens with all four people.

Declining closes the introduction and creates in-app updates. Matchmakers can cancel an outstanding invitation. The Messages view supports text, clickable web links, and small image/PDF/text attachments. Profile editing supports a photo, bio, phone, interests, and a Facebook profile URL. The phone is not displayed on candidate cards. Demo storage can be cleared in Settings.

## What is implemented

- Responsive dashboard, searchable friend cards, and candidate details.
- Both nomination flows, including co-matchmaker agreement.
- Look, accept, decline, cancel, and in-app notifications.
- Custom or generic introductions after mutual acceptance.
- Group and candidate-only conversation views.
- Local profile editing, image previews, file downloads, and persistence across reloads.
- Explicit Facebook connection status and integration guidance.

All identities and access checks are demonstrations in browser code. A person who controls the browser can inspect every stored message, including candidate-only messages. Never use this prototype for real private conversations or sensitive information. Files are limited to 700 KB each because browser storage is small. No external notifications are sent.

## Meta/Facebook setup

Meta’s current documentation could not be retrieved during this build. The following is an implementation plan, not confirmation that Meta will approve every requested capability. Do not promise full friend-list access, automatic personal Facebook notifications, Messenger delivery, automated friend requests, private-photo access, or embedded Facebook profiles until current documentation and your app’s approvals establish support.

1. Create a developer account and register an app at [Meta for Developers](https://developers.facebook.com/). Select the currently supported app type and Facebook Login configuration for this use case. Confirm any dating-service eligibility requirements before investing in the integration.
2. Set the app’s domain and exact HTTPS OAuth redirect URI for the production backend. Configure development/test identities before live access. Meta’s dashboard requirements may differ by app type.
3. Provide public privacy-policy and account/data-deletion pages and the contact/business information Meta requests. Describe friend-led matchmaking, data use, retention, and consent accurately.
4. Start with the minimum login permissions. Check the current [Facebook Login documentation](https://developers.facebook.com/docs/facebook-login/) and [permissions reference](https://developers.facebook.com/docs/permissions/) before requesting additional scopes such as `user_friends` or profile-link access. Treat friend discovery as limited to eligible, participating, consenting users unless Meta explicitly documents broader access.
5. Submit required permissions and the use case for App Review. Supply test access and a screencast demonstrating exactly how data is used. Complete business verification if requested by Meta.
6. Implement OAuth on a server, including state/CSRF protection, secure sessions, token validation, expiration, permission revocation, and deletion handling. Store the app secret and tokens only in a server secret store. Never put them in these static files, browser storage, or chat.
7. Keep a user-supplied Facebook profile link as a separate feature. Open it on Facebook, where Facebook’s privacy rules apply. A link is not proof of friendship. Do not scrape a Facebook page or automatically submit friend requests.
8. Use app notifications as the primary invitation channel. Add voluntary link sharing or an expressly opted-in delivery channel. If pursuing Messenger, review the current [Messenger Platform documentation](https://developers.facebook.com/docs/messenger-platform/) for supported recipients, consent, messaging windows, and permissions. Do not assume it can message arbitrary friends or deliver native personal-account notifications.
9. On invitation redemption, let each candidate explicitly sign in or create an account, confirm they are an adult, accept terms/privacy choices, and decide whether to view/accept. Avoid creating a real candidate account without their participation.

## Production backend plan

Replace the role switcher and local storage with authenticated identities and durable storage. If continuing on Sites, preserve Sites' supported host authentication and confirm the platform’s support for linking an external Meta identity before selecting an OAuth approach. Alternatively, deploy on infrastructure that explicitly supports your chosen Facebook Login flow.

Suggested records:

| Record | Important fields and rules |
|---|---|
| User | Internal ID, authenticated identity, adult eligibility, consent timestamps, account status |
| Profile | Bio, interests, photos, optional phone and explicit visibility settings |
| Facebook connection | App-scoped identifier, granted permissions, encrypted tokens, expiry/revocation state |
| Friendship | Both participants, verification source, consent and current validity |
| Match | Two distinct candidate IDs, one or two distinct matchmakers, reason, introduction, lifecycle state |
| Nomination | Matchmaker, their candidate, agreement timestamp; candidates and matchmakers are distinct per match |
| Invitation | Hashed single-use token, intended recipient, expiry, response, delivery state |
| Thread/member | Group: candidates + matchmakers. Private: exactly the two candidates |
| Message/attachment | Author, thread, timestamp, private file object reference, moderation state |
| Notification | Recipient, event, delivery channel/status, read timestamp |

Enforce all transitions on the server: draft → co-matchmaker approval (when needed) → invitations → mutual acceptance → introduced. Decline, withdrawal, cancellation, blocking, and expiry must prevent later acceptance or thread creation. Use transactions and idempotent events so duplicate clicks or delivery retries do not create duplicate threads.

Authorize every profile, match, message, and file request. Never rely on hiding a button. Only the two candidates can access their private thread. Matchmakers cannot download its files or query its messages. Use private object storage and expiring download links; validate uploads, scan files, enforce quotas, and remove executable content.

Before a public dating launch, add reporting/blocking, moderation operations, account recovery, deletion/export, notification preferences, rate limits, abuse prevention, retention rules, security monitoring, and reliable backups. Review applicable legal/privacy obligations for the jurisdictions you serve. A deployable native mobile app, app-store listing, push notifications, and deep links are separate work from this web prototype.

## Verification and delivery status

Verified JavaScript syntax and a successful local HTTP response. Browser checks covered the solo flow through mutual acceptance, generic group introduction, candidate-only message visibility in the UI, second-matchmaker approval, candidate decline, profile save, and desktop/phone layouts. Later fixes preserved Candidate B selection while editing other form fields and added downloadable attachments and clickable links. File uploads/downloads, custom introductions, and a complete two-matchmaker mutual-acceptance journey were not end-to-end tested. No live Meta behavior or secure multiuser access was tested.

A private Site record was registered, but no version was published: automatic approval review rejected passing the source credential to the publishing helper because that approval route was disabled. No live website URL is claimed. The source and local runnable files are delivered independently.

Sample portrait photos load from Unsplash and names/biographies are fictional; photos do not identify or imply that the depicted people are actual dating-app members. Replace these assets with consented user uploads before a real launch. Fonts load from Google Fonts.


