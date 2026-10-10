# Setup guide: profile picture, cover photo and /admin

This guide is written in simple English. Follow the steps in order. It takes about 15 minutes.

## What was added

- `/admin` – a private page where you log in and manage two pictures: your profile picture and your cover photo.
- `api/` – small server programs that run on Vercel. They check your login and save the pictures. The secret keys live here, never in the web page.
- Supabase Storage – the place where the pictures are saved, so every visitor sees the same pictures and they stay after every redeploy.
- `index.html` – your website. Only the code that shows the two pictures was changed. (One small layout fix is described at the bottom.)

---

## Step 1 – Put the new files on your website

Copy these into your website project, keeping the same folders:

```
index.html        (replace your old one)
vercel.json
package.json
.gitignore
api/              (whole folder)
admin/            (whole folder)
```

- If your site is connected to **GitHub**: add the files to your repository and commit. Vercel then deploys by itself.
- If you already have a `vercel.json` or `package.json`, do not overwrite them blindly. Add the lines from mine into yours.
- Do **not** upload a file with your real passwords to GitHub.

## Step 2 – Create a free Supabase project

1. Go to https://supabase.com and sign up (free).
2. Click **New project**. Choose any name and a region near you. Wait until it finishes.
3. Open **Project Settings** and then **API Keys** (in some older projects this page is called **API**).
4. Copy two things and keep them private for a minute:
   - **Project URL** – looks like `https://abcdxyz.supabase.co`
   - **Secret key** – starts with `sb_secret_...`. (Older projects: the **service_role** key, a very long text starting with `eyJ`.)
   - Do NOT use the "publishable" or "anon" key. It is the wrong one.

The secret key is powerful. Never put it in your website files, never send it in chat, never post it online.

## Step 3 – The storage bucket

Normally you do nothing. The first time you open `/admin`, it creates a **public** bucket called `site-media` by itself.

If you prefer to do it by hand (or if the admin page tells you the bucket is missing/private):
1. In Supabase open **Storage** and click **New bucket**.
2. Name it `site-media`.
3. Turn **Public bucket** ON. (Without this, visitors cannot see your pictures.)

## Step 4 – Add the settings in Vercel

1. Open your project on https://vercel.com.
2. Go to **Settings** and then **Environment Variables**.
3. Add these one by one (name on the left, value on the right):

| Name | Value |
|---|---|
| `ADMIN_EMAIL` | The email you will type to log in |
| `ADMIN_PASSWORD` | A long password, at least 10 characters. Use a password nobody can guess and that you use nowhere else. |
| `ADMIN_SESSION_SECRET` | A long random text, at least 32 characters. Make one with a password generator, or run `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`. You never type this one again. |
| `SUPABASE_URL` | The Project URL from Step 2 |
| `SUPABASE_SERVICE_ROLE_KEY` | The secret key from Step 2 |
| `SUPABASE_BUCKET` | Optional. Only add it if you want a different bucket name than `site-media`. |

4. **Redeploy.** New settings only work on a new deployment. Go to **Deployments**, click the three dots on the newest one, and choose **Redeploy**.

## Step 5 – Log in

1. Open `https://YOUR-WEBSITE/admin`.
2. Type the email and password you set in Step 4.
3. You will see the dashboard with two boxes: **Profile picture** and **Cover photo**.

Your login lasts 12 hours. After 5 wrong tries the login is blocked for 15 minutes.
Click **Log out** when you use a shared computer.

## Step 6 – Upload or change a picture

For the profile picture or the cover photo:

1. Click **Upload / change** and choose a JPG, PNG or WebP photo.
2. A crop area opens.
   - **Move**: drag the picture with your mouse or finger.
   - **Zoom**: use the slider, the mouse wheel, or pinch with two fingers.
   - **Rotate**: use the Rotate buttons.
   - **Saved size**: Standard is fine. Large is sharper but loads slower.
3. Look at **Preview on website**. For the cover photo it shows Desktop, Tablet and Mobile. On phones only the middle of the cover is visible, so keep the important part of the picture inside the dashed **Mobile** box.
4. Click **Save & publish**.
5. When you see "Saved", the new picture is already live for every visitor. If you still see the old one on your own phone or computer, refresh the page.

To go back to the default look, click **Remove**. The website then shows the original blue banner and the "SM" circle.

## Troubleshooting

| What you see | What to do |
|---|---|
| `/admin` shows "not found" | The `admin` folder was not deployed. Check Step 1. |
| "Server is not configured: missing environment variable ..." | Add that variable in Vercel (Step 4) and redeploy. |
| "ADMIN_PASSWORD must be at least 10 characters" | Choose a longer password in Vercel, then redeploy. |
| "The Supabase bucket ... is private" | Supabase > Storage > edit the bucket > turn on **Public bucket**. |
| "Could not read the storage bucket ..." | The `SUPABASE_URL` or the secret key is wrong. Copy them again from Supabase. |
| Upload says the picture is too big | Zoom in a little, or choose Standard size. |
| Everything saved but visitors see the old picture | Refresh. If it persists, check the bucket is Public. |

## Safety checklist

- Passwords and keys exist only in Vercel's Environment Variables, never in the code.
- Only a logged-in admin can upload or delete. The server checks this on every request.
- Never commit a `.env` file. `.gitignore` already protects it.
- To change the password, change `ADMIN_PASSWORD` in Vercel and redeploy. To log everyone out at once, change `ADMIN_SESSION_SECRET` and redeploy.

## One layout fix in index.html

In your original file the profile circle was placed at the bottom of the whole intro block, so on desktop it sat on top of the statistics row and on phones it sat on top of the "Contact Me" button. I changed one CSS value (`bottom:-70px` became `top:180px` in the `.avatar` rule) so it sits on the bottom edge of the cover photo, as the spacing around it was clearly designed to do. Nothing else in the design was touched.
