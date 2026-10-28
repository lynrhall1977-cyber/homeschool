# Homeschool Gradebook

A one-page gradebook for a homeschool year: subject gradebooks, curriculum import, weighted grades, quarters from your school calendar, attendance, and print-ready report cards and attendance records.

Grades are saved in a private **Supabase** database. You sign in with your email, and the gradebook is the same on every device you sign in on. The page itself is hosted free on **GitHub Pages**.

---

## What goes where

| File | Where it goes |
|---|---|
| `index.html` | Your GitHub repository (this is the gradebook) |
| `README.md` | Your GitHub repository (optional) |
| `supabase-setup.sql` | Supabase SQL Editor only. **Don't upload it to GitHub**, because it lists your emails. |

**Never upload** a gradebook backup (`.json`) or a file made with "Save a copy with my data". Those contain grades.

---

## 1. Create the Supabase project (5 minutes)

1. Go to **supabase.com**, sign up, and choose **New project**.
2. Name it (for example `homeschool-gradebook`), pick a region near you (East US), and set a database password. Keep the password in your password manager. The gradebook never uses it.
3. Wait a minute or two for the project to finish setting up.

## 2. Create the database tables

1. Open `supabase-setup.sql` in a text editor and replace `you@example.com` with the email you'll sign in with.
   For a second adult, remove the two dashes at the start of the `partner@example.com` line and put in their email.
2. In Supabase, open **SQL Editor → New query**, paste the whole file, and click **Run**.
3. The result at the bottom should list your email(s).

This creates one shared gradebook row and the rules that protect it:

- Only signed-in people whose email is on the member list can read or save it.
- Signed-out visitors get nothing.
- Nobody can create gradebooks or add members from the web page; that only happens in the SQL Editor.

## 3. Set up sign-in

In Supabase, open **Authentication**. Menu names move around a little; look for these settings.

1. **Add each person as a user.**
   Under **Users**, choose **Add user → Create new user**. Enter the email, and turn on **Auto Confirm User** if it's offered. (You don't need the password; sign-in uses an emailed link.)
   Do this for each email on your member list.
2. **Turn off public sign-ups.**
   Under **Sign In / Providers** (sometimes **Providers → Email** or **Auth settings**), turn **off** "Allow new users to sign up". Leave the **Email** provider on.
   The gradebook also refuses to create new accounts on its own. This setting closes the door on the Supabase side as well.
3. **Allow your site address.** You'll come back to this after step 5, once you know your GitHub Pages address.
   Under **URL Configuration**:
   - set **Site URL** to `https://lynrhall1977-cyber.github.io/homeschool/`
   - add the same address under **Redirect URLs**
4. **Optional: let people type a code instead of clicking the link.** This helps when your email is on your phone but the gradebook is open on a computer.
   Under **Emails → Templates → Magic Link**, add this line to the message:
   `Your sign-in code: {{ .Token }}`

## 4. Put your project's address and key into the gradebook

1. In Supabase, open **Project Settings → API Keys** (or click **Connect** at the top of the dashboard). Copy:
   - the **Project URL**, which looks like `https://abcdefgh.supabase.co`
   - the **anon public** key (a long `eyJ…` string), or the newer **publishable** key (`sb_publishable_…`). Either works.
   **Don't** use the `service_role` or **secret** key. It bypasses every protection and must never go in a web page.
2. Open `index.html` in a text editor (Notepad, TextEdit in plain-text mode, or VS Code) and find this block near the top of the script:

   ```js
   var CLOUD_CONFIG={
     url:'',
     anonKey:'',
     gradebookId:'homeschool'
   };
   ```
3. Paste the two values between the quotes and save the file.

The anon/publishable key is designed to be public. It's what lets the page talk to Supabase at all. The row-level security rules from step 2 are what keep the gradebook private.

## 5. Publish on GitHub Pages

1. The repository is `lynrhall1977-cyber/homeschool`, and `index.html` is already in it.
   On a free GitHub plan, Pages only publishes **public** repositories: open **Settings → General → Danger Zone → Change visibility → Public**. (GitHub Pro can publish from a private repository; the site itself is public either way.)
   The repository holds only the page; your grades stay in Supabase.
2. If you change `index.html` later, upload the new version to the repository, replacing the old one.
3. Open **Settings → Pages**. Under **Build and deployment**, set **Source: Deploy from a branch**, **Branch: main**, folder **/ (root)**, and click **Save**.
4. After a minute or two, the page shows your site address: `https://lynrhall1977-cyber.github.io/homeschool/`.
5. Go back to **Supabase → Authentication → URL Configuration** and enter that address (step 3.3).

## 6. Sign in and bring over your grades

1. Open your site address, enter your email, and click **Email me a sign-in link**. Open the link on the same device, or type the code if you added it in step 3.4.
2. The first time you sign in, the online gradebook is empty. To bring over anything you've already entered:
   - In the **old** gradebook file, go to **Settings → Backup → Export backup**.
   - On the **new** site, go to **Settings → Backup → Restore from backup** and choose that file.
   It saves to the cloud right away.
3. Sign in on your other devices. Everything appears there too.

---

## Everyday use

- **Saving is automatic.** The top bar shows "Saving…", then "Saved to cloud". Hover over it to see who saved last and when.
- **Offline:** if the internet drops, keep working. The top bar shows "Offline · will sync", and changes upload when the connection comes back.
- **Two people at once:** each save checks that nobody else saved in between. If someone did, the page loads the newest version and tells you, so nothing is silently overwritten. Re-enter your last change if it's missing.
- **Other devices:** switching back to the tab picks up changes made elsewhere.
- **Shared computer:** use **Settings → Backup → Sign out**. It also removes the copy saved in that browser.
- **Backups:** at the end of each quarter, use **Export backup** and keep the file with the portfolio.

## Good to know

- **Free plan pause:** Supabase's free plan pauses a project after a week with no activity. That's likely over summer break. Nothing is deleted. Open your Supabase dashboard and click **Restore project**, then wait a couple of minutes.
- **Sign-in emails:** Supabase's built-in email sender allows only a few emails per hour. You stay signed in for weeks at a time, so this rarely matters. If you hit the limit, wait a few minutes.
- **Updating the gradebook:** to install a new version of `index.html`, copy your two `CLOUD_CONFIG` values into it, then upload it to the repository, replacing the old file. Your grades aren't affected; they live in Supabase.
- **Curriculum import, Download PDF and fonts** load small helper files from public CDNs, so they need an internet connection.
