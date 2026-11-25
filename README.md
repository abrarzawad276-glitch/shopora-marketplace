# Shopora — Multi-vendor marketplace starter

বাংলাদেশি বাজারের জন্য একটি responsive multi-vendor ecommerce starter। এটি একটি চলমান MVP foundation, Daraz-এর কোনো কপি নয়।

## কী কী কাজ করে

- Responsive home page, product search, category filter, sorting
- Product details, image/video upload, stock, variants, timed discount badge/countdown
- Buyer and seller registration/login with hashed passwords and JWT sessions
- Seller product dashboard and product publishing
- Cart (browser local storage), checkout, Cash on Delivery demo order creation
- Product reviews with optional customer photo
- Order history and support ticket submission
- WhatsApp support link placeholder
- SQLite database for quick local development

## চালু করার নিয়ম

প্রয়োজন: Node.js 18 বা নতুন সংস্করণ।

```bash
cp .env.example .env
npm install
npm run seed
npm start
```

তারপর `http://localhost:3000` খুলুন।

ডেমো বিক্রেতা লগইন:
- Email: `seller@shopora.demo`
- Password: `ShoporaDemo123`

ডেমো পণ্যগুলোতে ছবি না থাকলে ইমোজি placeholder দেখাবে। বিক্রেতা লগইন করে নিজের ছবি/ভিডিও আপলোড করতে পারবেন।

## গুরুত্বপূর্ণ: Production-এ নেওয়ার আগে

এই repository-টি একটি কার্যকর starter/demo; বাস্তব অর্থ লেনদেন বা production authentication-এর জন্য নিচের কাজগুলো বাধ্যতামূলক।

1. **Database:** SQLite থেকে PostgreSQL-এ migration, automated backups, migrations ও connection pooling যোগ করুন।
2. **OTP:** `/api/auth/otp/request` বর্তমানে development OTP তৈরি করে সার্ভার টার্মিনালে দেখায়। Production-এ SMS provider API, Redis-backed OTP storage, expiry, resend cooldown, attempt limits ও abuse monitoring যোগ করতে হবে। API response-এ OTP দেওয়া হয় না।
3. **Google/Facebook:** OAuth buttons বর্তমানে placeholder। Google Identity Services/Facebook Login-এর server-side token verification, redirect/callback, state/nonce validation এবং account-linking যোগ করতে হবে। শুধু email/ID মিলিয়ে লগইন অনুমতি দেওয়া নিরাপদ নয়।
4. **Payments:** bKash/Nagad/Rocket/card options এখন selection placeholder মাত্র। Merchant onboarding, official gateway integration, server-side payment verification, webhook signature validation, idempotency, refunds ও reconciliation ছাড়া টাকা নেওয়া যাবে না। Demo order-এ payment status pending থাকে।
5. **Media:** Local uploads development-এর জন্য। Production-এ Cloudinary/S3, file scanning, image transformation, video transcoding এবং access controls ব্যবহার করুন।
6. **Security:** Strong `JWT_SECRET`, HTTPS, strict CORS, CSRF strategy where applicable, audit logs, rate limits, input validation, authorization checks, admin roles, secrets manager এবং dependency updates configure করুন।
7. **Marketplace operations:** Seller verification/KYC, commission, seller payouts, shipping/courier API, cancellation, returns, refunds, dispute handling, tax/VAT, invoice, fraud controls এবং admin moderation এখনও পূর্ণাঙ্গভাবে implement করা হয়নি।
8. **WhatsApp:** `public/app.js`-এ demo নম্বর `8801700000000` বদলে নিজের business WhatsApp নম্বর দিন (দেশের কোডসহ, + ছাড়া)।
9. **Admin panel:** production launch-এর আগে role-protected admin dashboard, seller approval, product moderation, order management এবং support inbox তৈরি করতে হবে।

## Environment variables

`.env.example`-এ SMS, OAuth, payment gateway, Cloudinary, WhatsApp এবং admin-এর placeholder রাখা আছে। এগুলো বাস্তব credentials নয়। কোনো secret frontend code-এ রাখবেন না বা Git-এ commit করবেন না।

## Project structure

```text
shopora-marketplace/
├── public/
│   ├── index.html       # Pages, layout and forms
│   ├── style.css        # Responsive UI styles
│   └── app.js           # Browser interactions and API calls
├── src/
│   ├── db.js            # SQLite schema
│   ├── server.js        # Express API, auth, products, orders, reviews, support
│   └── seed.js          # Demo seller and products
├── uploads/             # Created automatically for local uploads
├── .env.example
└── package.json
```

## API overview

- `POST /api/auth/register`, `POST /api/auth/login`
- `POST /api/auth/otp/request`, `POST /api/auth/otp/verify` (demo OTP only)
- `GET /api/products`, `GET /api/products/:id`, `POST /api/products`
- `POST /api/products/:id/reviews`
- `GET /api/sellers/:id`, `GET /api/seller/products`
- `POST /api/orders`, `GET /api/orders`
- `POST /api/support`

All money values are stored as numeric BDT amounts. This starter uses a flat demo delivery fee of ৳60; configure delivery pricing by zone/weight in a real deployment.
