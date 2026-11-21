# RuffMission — your own store (no Shopify, no Woo)

Plain HTML/CSS/JS + one small serverless function that creates a Square checkout.
You own every file. Hosting on Netlify's free plan; you pay only Square's card fees + your domain.

## What's in here
| File | What it does |
|---|---|
| `data/catalog.json` | **Everything you edit**: products, prices, designs, sizes, shipping, FAQ, videos, reviews, announcement bar |
| `images/` | Product photos (drop yours in with the names used in catalog.json, e.g. `army-1.jpg`) |
| `videos/` | Your vertical clips for the "Our Viral Costumes" slider (`clip-1.mp4` …) |
| `index.html` | Home = featured product page (like your Shrine homepage) |
| `shop.html`, `product.html`, `faq.html`, `contact.html`, `policies.html`, `thank-you.html` | Other pages |
| `netlify/functions/checkout.mjs` | Recalculates prices server-side and opens Square checkout |

## 1. Fill in your store (catalog.json)
Prices are in **cents**: `4999` = $49.99.
- `price_cents` / `compare_at_cents` – the main price and crossed-out price (the SAVE % badge is calculated from them).
- `bundles.tiers` – the "Build Your Soldier" boxes: **Solo** $49.99, **Battle Buddy** $89.99 (MOST POPULAR), **Squad** $119.99 (BEST VALUE). Edit `title`, `subtitle`, `chip`, `badge`, `price_cents`, `compare_at_cents`. Square charges exactly the box price.
- `bundles.addons` – paid add-ons with a checkbox (Military Glasses $9.99).
- `bundles.gifts` + each tier's `gifts` – free items offered in that tier (Free Tactical Chew Toy on Battle Buddy and Squad).
- `options` – the dropdowns in each box: **Size** (S–2XL) and **Variant** (US Army, US Marines, US Navy, US Airforce, Police Officer, Ice Agent, Trump). Each variant has its own `image` (the little thumbnail next to the dropdowns).
- `images` – the swipeable product gallery at the top.
- Sold out: `"sold_out": [{ "Size": "XXL", "Variant": "Police" }]`
- `reviews` (under Add to Cart) and `store.reviews` ("Approved by the troops") – only keep reviews from real buyers.
- `store.shipping` – currently free shipping (`flat_fee_cents: 0`). Set e.g. `599` for $5.99 and `free_over_cents` for a free-shipping threshold.

### Photos & videos
- **Product photos** (all 14 gallery photos, the 7 variant photos, glasses and chew toy) currently load straight from your Shopify file links in `catalog.json`. They work as long as your Shopify files exist. **Before you close Shopify**: download them, put them in `images/`, and change the links in `catalog.json` to `images/yourfile.jpg` (or send them to Claude to do it).
- Stand-ins cropped from your screenshots: `logo.jpg`, `featured-poster.jpg`, `clip-1/2-poster.jpg`, `testimonial-1.jpg` — replace with originals (same names).
- Still needed: videos `videos/featured.mp4`, `videos/clip-1.mp4`, `clip-2.mp4`, `clip-3.mp4` (vertical MP4, under ~10 MB each), and `testimonial-2.jpg`, `testimonial-3.jpg`.

## 2. Get Square keys
1. Go to https://developer.squareup.com/apps → **Create app** (free).
2. Under **Sandbox** copy the *Sandbox Access token* and, under **Locations**, the sandbox *Location ID*.
3. Later, for real money: switch to **Production** and copy the production token + location ID.

## 3. Put it online (Netlify, free)
Functions need a Git deploy (drag-and-drop uploads skip the checkout function):
1. Create a GitHub repo and upload this folder's contents (GitHub web → "Add file → Upload files" works).
2. https://app.netlify.com → **Add new site → Import an existing project** → pick the repo. Build command: *leave empty*. Publish directory: `.`
3. Site configuration → **Environment variables**, add:
   - `SQUARE_ACCESS_TOKEN` = your token
   - `SQUARE_LOCATION_ID` = your location ID
   - `SQUARE_ENV` = `sandbox` (change to `production` when going live)
4. **Deploys → Trigger deploy**. Your site is live at `something.netlify.app`.
5. Test a purchase with Square's sandbox card `4111 1111 1111 1111`, any future date, any CVV, ZIP 94103.
6. Go live: swap the env vars to production values, set `SQUARE_ENV=production`, redeploy.
7. Domain: Netlify → **Domain management → Add a domain** (buy there or point your existing one).

Every edit you push to GitHub (e.g. a new price in catalog.json) auto-redeploys in ~30 seconds.

## Where orders, messages and emails go
- **Orders & payments**: Square Dashboard → Orders / Transactions. Customers get a Square receipt; shipping address is collected at checkout.
- **Contact form + newsletter signups**: Netlify → your site → **Forms** (turn on email notifications under Forms → Settings).
- **Tracking**: the thank-you page tells customers tracking comes by email — send it from Square (Orders → mark fulfilled) or your supplier.

## Notes
- Policies in `policies.html` are based on your Shopify shipping/returns copy plus a basic privacy/terms template — read them over and adjust.
- Fees: Square's hosted checkout charges its standard online card rate (check your Square plan).
