# [Your Brand Name] — Pet Care & Supplies E-commerce (A–Z Build Prompt)

> Replace `[Your Brand Name]` and `[Your Location]` throughout before using. Example name directions: "Pawsome", "Tailwag", "Bondhu Pets" ("bondhu" = friend). Colors below follow Pantone's 2026 Color of the Year ("Cloud Dancer," a soft off-white) and WGSN/Coloro's "Transformative Teal."

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack e-commerce website with a fully-featured admin dashboard, for:

- **Brand name:** [Your Brand Name]
- **Business type:** Pet care & supplies retail (dog/cat food and treats, toys, grooming, beds, leashes/collars, small bird/fish supplies)
- **Location:** [Your Location], Bangladesh
- **Primary market:** Bangladesh, urban pet owners (dogs and cats primarily), mobile-first, Bangla + English speaking

The system must be built on **Cloudflare** for hosting/runtime (Workers, D1, KV, R2), with source code hosted on **GitHub** and deployed via GitHub Actions — one Cloudflare Worker (Hono + TypeScript) serving both the static storefront/admin app and all `/api/*` routes. The admin dashboard must support full CRUD for every resource below, with validation, confirmation prompts, and success/failure feedback.

The deliverable must include the following:

## 1. Brand, Market & Business Requirements
- Define the brand identity, a short tagline, and a warm, trustworthy (not childish) logo concept direction — think premium pet boutique, not a toy store.
- Target customer: urban dog and cat owners who treat pets as family and are willing to pay for quality food and health-conscious products.
- All customer-facing and admin UI text bilingual (Bangla + English) with a language toggle, plain language throughout.
- Business model: Cash on Delivery as default, plus mobile financial services and card payment; a subscription/auto-reorder option for consumables (food, litter) since those are genuinely recurring purchases.
- Feature [Your Location] on the Contact/About page, footer, and structured data.
- Team is small and non-technical — every admin workflow must be operable with no coding knowledge, large touch targets, plain-language labels.

## 2. Public Website — Description & Layout
- **Visual style:** warm and premium, not cartoonish. A Cloud-Dancer-off-white base (`#F0EEE9`-ish, per Pantone's 2026 Color of the Year) with Transformative Teal as the primary accent and a warm terracotta secondary accent; soft rounded corners, generous photography of real pets (not stock-illustration paw prints everywhere).
- **2026 baseline UX expectations to build in from the start:** mobile-first and thumb-friendly layout; adaptive light/dark "mood mode" theming that follows the visitor's system setting, using color tokens rather than a hard-coded palette; short product videos (a dog eating the food, a toy in use) alongside photos, since video converts meaningfully better than photos alone; lightweight, fast-loading pages (compressed images, minimal scripts) over heavy animation.
- Site map: Home, Shop (category & subcategory), Product Detail, Cart, Checkout, Account (Login/Register, Orders, Wishlist, Addresses, **My Pets**), About/Contact, Search Results.
- **Homepage:** hero banner, category tiles (Dog, Cat, Toys, Grooming, Beds & Accessories), new arrivals, best sellers, a "subscribe & save" callout for food/litter, customer pet photos/reviews, newsletter signup, footer with location and policies.
- **Product listing:** filters by category, pet type (dog/cat/bird/fish), pet size/breed group (small/medium/large for dogs), price, and ingredient flags (grain-free, no artificial preservatives); sort by price/newest/popularity/rating.
- **Product detail page:** image gallery plus a short video where available, ingredient/nutrition panel, pet-size or life-stage guidance (puppy/adult/senior), price, stock status, Add to Cart/Buy Now, a **"subscribe and save X%"** toggle for consumables, customer reviews (optionally tagged with pet type), related/upsell products.
- **My Pets (account feature):** a customer can save each pet's name, type, breed, and age; product pages can show "good for [pet name]" guidance once a pet profile exists, and reorder reminders can reference the pet by name.
- **Cart & checkout:** guest checkout, Bangladesh address form with tiered delivery fee, payment method selection (COD, bKash, Nagad, Rocket, card via SSLCommerz), order summary and confirmation with SMS/WhatsApp notification.
- **Account area:** order history with tracking, wishlist, saved addresses, My Pets, active subscriptions (pause/skip/cancel).

## 3. Admin Dashboard — Description & Layout
- **Visual style:** calm and professional — Cloud-Dancer off-white background, Transformative Teal accents on active states and key stats, rounded cards, soft shadows.
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Products, Categories, Orders, Subscriptions, Customers, Coupons, Inventory, Reviews, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with a monthly sales chart, KPI cards (today's orders, revenue, pending COD, low-stock alerts, active subscriptions), recent-orders table, top-selling products.
  - **Right sidebar:** search, admin profile dropdown, notifications (new order, low stock, new review, subscription renewal failure), staff online status.
- **CRUD modules:**
  - **Products** — name, bilingual description, category, pet-type/size tags, price, discount, variants (size/weight), ingredient/nutrition panel, images (plus optional short video upload), subscription-eligible flag, status, SEO slug, auto-generated SKU (see Section 5); bulk CSV import/export.
  - **Categories & Subcategories** — nested, drag-to-reorder.
  - **Orders** — line items with SKUs, customer, address, payment method/status, delivery pipeline (Pending → Confirmation Attempted [logged call/OTP outcomes, one-tap buttons — see Section 20] → Confirmed → Packed → Shipped [courier + tracking, ingesting courier sub-statuses like Out for Delivery] → Delivered, with three distinct off-ramps: Cancelled, Refused at Delivery, and Returned, kept separate for risk scoring and stock), **auto-generated invoice number and a printable PDF invoice** (see Section 5), refund handling.
  - **Subscriptions** — view/manage active auto-reorder subscriptions, their next-charge date, and failures.
  - **Customers** — profile, saved pets, order history, block/unblock.
  - **Coupons/Discounts** — percentage/flat, minimum order, expiry, usage limits.
  - **Inventory** — per-SKU stock, batch/expiry tracking for perishable pet food, low-stock alerts, adjustment log.
  - **Reviews** — approve/reject/reply.
  - **Staff & Roles** — Super Admin, Manager, Order Processor, Read-only Viewer.
  - **Reports/Analytics** — sales by category/product/date range, best sellers, subscription churn, CSV export.
  - **System Settings** — store info, delivery zones & rates, payment-gateway keys, notification templates.
  - **Activity/Audit Log.**
- **CRUD interaction requirements:** modal/slide-over create-edit forms; inline bilingual validation; confirmation dialog before delete (soft-delete/trash); toast success/failure notifications; role-based hiding of restricted actions; search/filter/pagination on every list.

## 4. Full-Stack Architecture
One Cloudflare Worker (Hono + TypeScript) serves the storefront/admin assets and all `/api/*` routes, backed by D1, KV, and R2. GitHub is the source of truth and CI/CD pipeline (GitHub Actions deploys on push to `main`); Cloudflare is the runtime. Provide a Mermaid architecture diagram showing browsers → Worker → D1/KV/R2, outbound calls to payment/courier/notification providers, a recurring-billing job for subscriptions, and the GitHub → Actions → Cloudflare deploy path.

## 5. Data Models
Provide schema tables for: `products`, `product_variants`, `categories`, `orders`, `order_items`, `customers`, `pets`, `subscriptions`, `addresses`, `reviews`, `coupons`, `admins`, `inventory_batches`, `delivery_zones`.

**SKU & Invoice Numbering (required):**
- Every product variant gets an auto-generated, unique **SKU** on creation, following a pattern such as `PET-[CategoryCode]-[PetType]-[Sequence]` (e.g. `PET-FOOD-DOG-0019`). Enforce uniqueness at the database level.
- Every confirmed order gets an auto-generated, unique, sequential **invoice number**, e.g. `INV-PET-YYYYMMDD-####`, generated at order confirmation. Provide a downloadable/printable PDF invoice per order with itemized SKUs, quantities, unit prices, discounts, delivery fee, and total.

**Additional tables for fraud/marketing/premium features:** `abandoned_checkouts` (session id, partial name/phone/address, cart snapshot, last step reached, status: Open/Recovered/Ignored, linked order id); `order_confirmation_attempts` (order id, attempted at, outcome, staff member); `return_requests`; `stock_notify_requests`; `referral_codes`. Add `utm_source`/`utm_medium`/`utm_campaign` and `confirmation_method` to `orders`; add `total_orders`, `delivered_count`, `refused_or_returned_count`, and `risk_level` to `customers`.

Include one worked example: an **Order** model as a TypeScript interface, including `sku` on each line item, `invoiceNumber`, and an optional `subscriptionId` for recurring orders.

## 6. Payments, Delivery & Third-Party Integrations
- Payments: COD, bKash, Nagad, Rocket, card via SSLCommerz (or similar); recurring charging for subscriptions where the gateway supports tokenization.
- Delivery: Steadfast as primary courier, Pathao Courier and RedX as alternatives; tracking-ID sync and status webhooks.
- Live delivery-fee calculation by Division/District/Upazila.
- SMS/WhatsApp order-confirmation, status-update, and subscription-reminder notifications.
- Optional: Cloudflare Workers AI for a "which food fits my pet" recommender based on the saved pet profile.

## 7. Security & Compliance
Align with the NIST Cybersecurity Framework (Govern, Identify, Protect, Detect, Respond, Recover), scoped for retail e-commerce: password hashing, HTTPS/HSTS, RBAC on every admin action, secrets in Wrangler secrets, PCI-scope minimization via hosted payment redirects, admin audit logging, rate-limiting on auth/checkout, an incident-response outline, and a D1 backup/restore procedure.

## 8. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E for the checkout and subscription-signup flows; JSON fixtures for product-list, create-order, and admin-login endpoints; TypeScript build check required before deploy.

## 9. Dashboard Development Plan
Lightweight stack (vanilla JS/HTML/CSS or Alpine.js/htmx), a small charting library (Chart.js) for analytics, full usability on tablet/large phone, and defined loading/empty/error states for every view.

## 10. File & Folder Structure
```
[your-brand-slug]/
├── worker/
│   ├── src/
│   │   ├── routes/          (products, orders, customers, pets, subscriptions, auth, admin)
│   │   ├── lib/              (payment, courier, sms integrations, sku + invoice generators, billing job)
│   │   └── index.ts
│   ├── migrations/
│   └── wrangler.toml
├── public/
├── admin/
├── tests/
├── .github/workflows/
└── docs/
```

## 11. Development Environment & Deployment Setup
Node.js version, `wrangler` CLI install/login, `wrangler d1 create`, KV namespace, R2 bucket, sample `wrangler.toml` and `.dev.vars`, GitHub branch strategy, a GitHub Actions workflow that tests/builds/migrates/deploys on push to `main`, and custom-domain setup via Cloudflare DNS.

## 12. SEO, Performance & Marketing
Meta tags, Open Graph/Twitter cards, sitemap.xml, robots.txt, `Product` JSON-LD for listings, Core Web Vitals targets for mobile, WhatsApp click-to-chat, Facebook/Instagram Shop feed.

## 13. Suggested Product Categories & Content Plan
Dog Food & Treats, Cat Food & Treats, Toys, Grooming & Health, Beds & Accessories, Leashes & Collars, Bird & Fish Supplies — adjustable starting points.

## 14. Phased Roadmap
- **Phase 1 (MVP):** catalog, cart, COD checkout, basic admin CRUD for products/orders/categories, SKU + invoice generation live.
- **Phase 2:** bKash/Nagad/SSLCommerz, Steadfast API, coupons, reviews, subscriptions/auto-reorder, reports.
- **Phase 3:** My Pets-based AI recommender, loyalty program, PWA/offline support.

## 15. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; keep all copy bilingual and plain-language with large touch targets; start from a clean checkout before editing existing code.

## 16. Fraud Prevention & Abandoned-Checkout Capture
COD-heavy markets see a meaningful share of fake or prank orders, and a lot of recoverable revenue sits in checkouts that never finish. Build for both:
- **Phone verification at checkout:** send an SMS OTP to the phone number entered at checkout before an order can move past "Pending"; an unverified number blocks automatic confirmation.
- **Courier fraud-check lookup:** before confirming a COD order, check the customer's phone number against a courier/fraud-checker service (Steadfast, Pathao, and third-party aggregators expose phone-number lookups showing past delivery-success vs. return/refusal rates) and surface that history to the admin on the order screen.
- **Order-risk scoring:** track each customer's lifetime orders placed vs. delivered vs. returned/refused; compute a simple risk score (Low/Medium/High) shown as a badge next to the customer and on their orders; require a manual confirmation call before dispatch for anything above "Low."
- **Trusted-customer fast lane:** customers with a strong delivery history can skip the manual-confirmation step.
- **Velocity & bot checks:** flag multiple orders in a short window from the same phone number, address, or IP; add Cloudflare Turnstile to the checkout form.
- **Abandoned-checkout capture:** autosave partial checkout data (name, phone, address) server-side as the customer types, tied to a session; if unconfirmed within a set window, it surfaces in the admin dashboard as an **Abandoned Checkout** with actions to call/message, mark "Recovered," or mark "Not interested."

## 17. Marketing, Ads & Conversion Tracking
- **Meta Pixel + Conversions API (CAPI):** fire the standard event set (PageView, ViewContent, AddToCart, InitiateCheckout, Purchase, Lead on abandoned-checkout capture) from the browser via Pixel and, in parallel, from the Worker via the Conversions API — same `event_id` on both for deduplication, hashed phone/email for matching.
- **Google Ads conversion tracking + GA4:** standard e-commerce events in GA4's schema, plus a Google Ads conversion tag on the order-confirmation page.
- **Microsoft Clarity:** heatmaps and session recordings for UX insight.
- **UTM capture & attribution:** store utm_source/medium/campaign against the resulting order.
- **Campaign landing pages:** a lightweight, nav-free template for specific ad campaigns.
- **Click-to-WhatsApp ad support:** carry a referring-ad identifier through to the WhatsApp conversation.

## 18. Additional Premium Features
- **Abandoned-checkout recovery automation:** an automatic SMS/WhatsApp nudge a set number of minutes after abandonment.
- **Automated post-delivery review request.**
- **Back-in-stock notifications.**
- **Return/refund self-service** from the customer's account.
- **Referral program.**
- **Admin two-factor authentication** for Super Admin and Manager roles.
- **Google Merchant Center feed.**
- **PWA push notifications** for order status and subscription reminders.
- **VAT/tax-inclusive invoicing**, off by default.
- **Honesty guardrail on urgency signals:** any "X left in stock" display must reflect real data.

## 19. Acceptance Checklist
- [ ] Bilingual storefront and admin, working language toggle
- [ ] Guest checkout with Division/District/Upazila address and live delivery-fee calculation
- [ ] COD works end-to-end; at least one MFS integration functional or clearly stubbed
- [ ] Every product has a unique, correctly-formatted SKU generated automatically
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice
- [ ] Full CRUD on products (with pet-type tags), categories, orders, subscriptions, customers, coupons
- [ ] My Pets profiles save correctly and can inform a product recommendation
- [ ] A COD order can't reach "Confirmed" without either OTP verification or a logged manual confirmation call
- [ ] Abandoned checkouts are visible and actionable in the admin dashboard
- [ ] Meta Pixel and the Conversions API fire matching events with a shared event_id
- [ ] "Refused at delivery," "Returned," and "Cancelled" are tracked as distinct order outcomes
- [ ] A non-technical owner can complete first-time setup without outside help
- [ ] Every list view has a CSV export
- [ ] No urgency/stock-count display is fabricated
- [ ] No secrets committed to the repository

## 20. Owner-Friendly Operations & Onboarding
The person running this day to day has no coding background, so the system needs to guide them, not just expose settings to them:
- **Guided first-run setup:** add your first product, set a delivery zone, connect a payment method, connect WhatsApp — one simple action per step.
- **A plain-language "Health Check" strip** on the dashboard home ("Payment: connected" / "Facebook tracking: not connected yet").
- **Plain-language risk badges, not scores:** 🟢 Trusted / 🟡 New / 🔴 Verify before shipping.
- **A "Needs your attention today" home view:** confirmation calls due, abandoned checkouts, low stock, pending reviews — ahead of chart-heavy analytics.
- **One-tap actions on every order:** a `tel:` "Call customer" button and a WhatsApp button with pre-written, editable bilingual templates.
- **Bulk actions with a plain confirmation.**
- **CSV/Excel export everywhere there's a list.**
- **Optional phone + OTP login for day-to-day staff roles**, with password + 2FA kept for Super Admin/Manager.

## Output Format
Single structured document: labeled sections matching the numbering above; Mermaid diagrams where applicable; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree; a short glossary for any Bangla UI terms used.

## Notes
- Treat COD plus MFS as the primary payment reality; card payments are secondary.
- The SKU and invoice-numbering system is a hard requirement, not optional.
- Fraud-prevention checks and honest urgency-signal data are hard requirements too — don't drop them for a "simpler" build.
- Flag any assumption (palette details, exact delivery-fee tiers, exact category list) as adjustable and confirm with the requester before finalizing.