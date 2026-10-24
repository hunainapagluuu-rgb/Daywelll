# Daywell Together — setup guide

This update adds the Daywell Together interface and the first cloud-sync implementation. The HTML includes email/password sign-in, invitation-code pairing, a private shared dashboard, daily progress summaries, optional sleep/mood/water sharing, encouragement messages, and shared goals.

**Important:** syncing is not live until you configure your own Supabase project and run the SQL below. The app has not been tested against a real Supabase project or in a full browser end-to-end test yet. Back up your current Daywell data before replacing the file.

## 1. Create a Supabase project

1. Open the official Supabase dashboard and create a project: https://supabase.com/dashboard
2. Open **Project Settings → API** (the exact menu label may vary).
3. Copy the **Project URL** and the **publishable/anon client key**. Do not use a database password, `service_role` key, or secret key in the HTML.
4. Open **SQL Editor**, create a new query, paste all of `daywell_together_schema.sql`, and run it once.
5. In **Authentication → URL Configuration**, set your local development URL as the Site URL while testing (for example `http://localhost:8000`) and add it to allowed redirect URLs. Change these to your deployed HTTPS site before you and your partner use it from separate devices.
6. Review the email confirmation setting in Authentication. If confirmations are enabled, each account must confirm its email before signing in.

## 2. Deploy to Vercel

This bundle includes `index.html` at the project root, which is required for the Vercel root URL to work. Upload/extract these files into the root of your GitHub repository (not inside an extra nested folder). In Vercel, import that repository, choose **Other** as the framework preset, leave the Root Directory as `./`, and leave Build Command empty. Set the Output Directory to `.` if Vercel asks for one, then redeploy. The SQL file is setup material and is not served by the app.

## 3. Run Daywell over localhost or HTTPS

The app uses Supabase's browser client and browser authentication. Don't open it as `file://`.

Place these files together in one folder:

- `my_day_editorial.html`
- `manifest.webmanifest`
- `sw.js`
- `daywell-icon.svg`
- `daywell_together_schema.sql` (setup reference; not needed by the running page)

In that folder, run:

```bash
python -m http.server 8000
```

Then open `http://localhost:8000/my_day_editorial.html`. For your partner to access it on another device, publish the app files to an HTTPS host and add that exact site URL to Supabase's allowed redirect URLs.

## 4. Configure the app

1. Open **Settings → Cloud account & syncing**.
2. Paste the Supabase Project URL and public publishable/anon key, then save.
3. Create an account with your email and a password of at least 8 characters, or sign in if you already have one.
4. Your partner should open the same hosted app and create a separate account.
5. One person chooses **Create my invite** and privately sends the generated code to the other. The other person enters it and chooses **Connect with code**. Codes expire after 48 hours and are single-use.
6. Review **Choose what you share** on both accounts. Daily habit/task totals are enabled by default. Sleep, mood, and water are off unless each person explicitly enables them.

## What syncs in this version

- Daily habit completion totals and task completion totals (not task names), for up to the last 30 days already recorded locally and subsequent daily changes.
- Optional sleep duration, mood level, and water-glass count, each in a separate database table with its own access policy.
- Encouragement messages and shared goal titles/progress.

## What stays private

Journal entries, quick notes, individual task titles, bedtime/wake time, and detailed personal routines are not uploaded by this implementation. Keep in mind that a backup export contains your local private data, so protect the file.

## Important notes

- The app checks for partner updates about every 30 seconds while the Together view is connected; this first implementation uses polling rather than instant push notifications.
- The browser stores the Supabase URL and public client key in local storage. The public key is designed for browser use; security depends on the Row Level Security rules in the SQL schema. Never paste a service-role/secret key.
- A free Supabase project may be enough for a small personal project, but usage and inactivity limits can change. Check current terms: https://supabase.com/pricing
- Before relying on this for important records, test account creation, invite acceptance, sharing toggles, disconnecting, and access from both accounts. This is a starter implementation, not a security audit.
