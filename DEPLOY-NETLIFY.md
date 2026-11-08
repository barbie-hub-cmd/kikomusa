# Putting your candle shop on Netlify

Everything the shop needs is inside this folder: the pages, the 60 candle listings with their
photos, the payment QR, and the delivery rules (₹50 inside Jaipur, quoted by you elsewhere).
Your orders, customer accounts and product list live in the shop's own backend, so the Netlify
site shows the *same* orders and products you see here — nothing has to be re-entered.

## What you need first

1. A free Netlify account: <https://app.netlify.com/signup>
2. A free GitHub account (Netlify builds straight from a GitHub repository): <https://github.com/signup>

## Step 1 — Put the shop on GitHub

1. On GitHub, press **New repository** → give it any name (for example `kikomusa`) → **Create repository**.
2. On that empty repository page, press **uploading an existing file**.
3. Drag in **every file and folder** from this folder (the whole contents, not a zip).
4. Press **Commit changes**.

## Step 2 — Let Netlify build it

1. In Netlify: **Add new project** → **Import an existing project** → **Deploy with GitHub** →
   choose the repository you just uploaded.
2. Netlify reads `netlify.toml` in this folder, so the build settings arrive on their own:
   build command `bun run build`, publish folder `dist`. Leave them as they are and press
   **Deploy site**.
3. The first build takes a couple of minutes. When it finishes you get a address like
   `https://your-site.netlify.app`.

## Step 3 — Tell Netlify where your shop's data lives

Two settings are needed, or the pages will open but the shop won't be able to save orders.

1. In Netlify: your site → **Site configuration** → **Environment variables** → **Add a variable**.
2. Add these two, taking each value from the `.env` file that is in this folder:

   | Name | Value |
   | --- | --- |
   | `SUPABASE_URL` | the `SUPABASE_URL` line in `.env` |
   | `SUPABASE_PUBLISHABLE_KEY` | the `SUPABASE_PUBLISHABLE_KEY` line in `.env` |

3. Press **Rebuild and redeploy** (or **Deploys → Trigger deploy → Deploy site**) so the
   new settings take effect.

Optional third variable: `LOVABLE_API_KEY` — without it the "You may also like" row on each
product page simply falls back to showing candles from the same collection.

## Step 4 — Check it

Open the new Netlify address and:

- [ ] Home page and photos load.
- [ ] Shop shows the 20 newest candles first; a product page shows "You may also like".
- [ ] Add a candle → choose a fragrance → cart → fill the delivery address.
  - Jaipur address → payment QR appears with ₹50 added.
  - Any other city → "Contact the owner for delivery charges" with the WhatsApp button first.
- [ ] Upload a payment screenshot → you are taken straight to the printable receipt.
- [ ] Sign in at `/admin` with `kikomusa642025@gmail.com` → orders, payment screenshots,
  status changes and **Delete order** all work.

## Renaming your address

Netlify → **Site configuration** → **Domain management** → **Options** → **Edit site name**
gives `https://kikomusa.netlify.app` if that name is free. A own domain (for example
`kikomusa.in`) can be added on any paid Netlify plan.

## If Netlify shows "Page not found"

That means Netlify is showing your files without ever running the shop's build — the shop
needs a server, so a folder of files alone cannot answer any page request.

1. Netlify → your site → **Deploys**. If the newest entry is a "Published" deploy from a
   drag-and-drop upload rather than a build, that is the cause.
2. Fix it by deploying **from GitHub** (Steps 1–2 above). Do not drag the folder or the zip
   onto Netlify's "Deploy manually" box — that skips the build entirely.
3. If you did deploy from GitHub, open the newest entry under **Deploys** and check the log
   says the build finished (`$ node scripts/check-netlify-output.mjs` should print
   `ok: server handler present`). Then press **Deploys → Trigger deploy → Clear cache and
   deploy site**.
4. Confirm the server is live: Netlify → **Functions** should list a function called
   "server handler". If it is missing, the build never produced it.

## If a build fails

- "Command not found: bun" → in `netlify.toml`, the `BUN_VERSION` line must name a version that
  exists (currently `1.4.4`); Netlify installs it for you.
- Pages open but nothing saves → the two environment variables in Step 3 are missing or
  mistyped, or the site was not redeployed after adding them.
- A product photo is missing → the `public/product-images` folder must be uploaded in Step 1;
  it holds every picture the shop shows.
