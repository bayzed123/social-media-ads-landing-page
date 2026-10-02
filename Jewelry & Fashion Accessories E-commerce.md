# [Your Brand Name] — Jewelry & Fashion Accessories E-commerce (A–Z Build Prompt)

> Replace `[Your Brand Name]` and `[Your Location]` throughout before using. Example name directions: "Zircona", "Adorn House", "Charu Jewels". Scope below is fashion/imitation jewelry; genuine gold/precious-metal jewelry would need additional hallmark/purity certification and insured-shipping handling not covered here.

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack e-commerce website with a fully-featured admin dashboard, for:

- **Brand name:** [Your Brand Name]
- **Business type:** Jewelry & fashion accessories retail (earrings, necklaces, bangles, rings, anklets, hair accessories)
- **Location:** [Your Location], Bangladesh
- **Primary market:** Bangladesh, style-conscious women shopping for everyday and festive/wedding-season pieces, mobile-first, Bangla + English speaking

The system must be built on **Cloudflare** for hosting/runtime (Workers, D1, KV, R2), with source code hosted on **GitHub** and deployed via GitHub Actions — one Cloudflare Worker (Hono + TypeScript) serving both the static storefront/admin app and all `/api/*` routes. The admin dashboard must support full CRUD for every resource below, with validation, confirmation prompts, and success/failure feedback.

The deliverable must include the following:

## 1. Brand, Market & Business Requirements
- Define the brand identity, a short tagline, and a luxe logo concept direction.
- Target customer: women buying everyday fashion jewelry as well as festive/wedding-season statement pieces.
- All customer-facing and admin UI text bilingual (Bangla + English) with a language toggle, plain language throughout.
- Business model: Cash on Delivery as default, plus mobile financial services and card payment.
- Feature [Your Location] on the Contact/About page, footer, and structured data.
- Team is small and non-technical — every admin workflow must be operable with no coding knowledge, large touch targets, plain-language labels.

## 2. Public Website — Description & Layout
- **Visual style:** luxe and elegant, high contrast. Deep black/charcoal background with gold/champagne accents, macro close-up product photography emphasis, a serif display typeface for headings paired with a clean sans-serif for body text, generous negative space, a subtle shimmer/glow micro-interaction on hover.
- Site map: Home, Shop (category & subcategory), Product Detail, Cart, Checkout, Account (Login/Register, Orders, Wishlist, Addresses), About/Contact, Search Results.
- **Homepage:** hero banner, category tiles (Earrings, Necklaces, Bangles, Rings, Sets), a festive/wedding-collection banner, best sellers, customer photos/reviews, newsletter signup, footer with location and policies.
- **Product listing:** filters by category, material/plating, price range, and occasion (everyday/festive/bridal); sort by price/newest/popularity/rating.
- **Product detail page:** macro image gallery with zoom, material/plating info, weight (grams), a ring-size guide where relevant, price, stock status, Add to Cart/Buy Now, a **"complete the look"** cross-sell block suggesting matching pieces from the same set, gift-wrap option, customer reviews, related products.
- **Cart & checkout:** guest checkout, Bangladesh address form with tiered delivery fee, a **gift-wrap add-on**, payment method selection (COD, bKash, Nagad, Rocket, card via SSLCommerz), order summary and confirmation with SMS/WhatsApp notification.
- **Account area:** order history with tracking, wishlist, saved addresses.

## 3. Admin Dashboard — Description & Layout
- **Visual style:** elegant dark theme — near-black background with gold accent highlights, serif headings, rounded cards with a subtle metallic-gradient border on key stat cards.
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Products, Categories, Collections/Sets, Orders, Customers, Coupons, Inventory, Reviews, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with a monthly sales chart, KPI cards (today's orders, revenue, pending COD, low-stock alerts, new customers), recent-orders table, top-selling products.
  - **Right sidebar:** search, admin profile dropdown, notifications (new order, low stock, new review), staff online status.
- **CRUD modules:**
  - **Products** — name, bilingual description, category, price, discount, material/plating, weight (grams), color/finish variants, images, occasion tags, status, SEO slug, auto-generated SKU (see Section 5); bulk CSV import/export.
  - **Categories & Subcategories** — nested, drag-to-reorder.
  - **Collections/Sets** — group multiple products into a matching "set" for the storefront's cross-sell block.
  - **Orders** — line items with SKUs, customer, address, payment method/status, gift-wrap flag, delivery pipeline (Pending → Confirmation Attempted [logged call/OTP outcomes, one-tap buttons — see Section 20] → Confirmed → Packed → Shipped [courier + tracking, ingesting courier sub-statuses like Out for Delivery] → Delivered, with three distinct off-ramps: Cancelled (before shipping), Refused at Delivery (courier attempted, customer declined), and Returned (accepted, then sent back) — kept separate because they mean different things for risk scoring and stock), **auto-generated invoice number and a printable PDF invoice** (see Section 5), refund handling.
  - **Customers** — profile, order history, block/unblock.
  - **Coupons/Discounts** — percentage/flat, minimum order, expiry, usage limits.
  - **Reviews** — approve/reject/reply.
  - **Inventory** — per-SKU stock, low-stock alerts, adjustment log.
  - **Staff & Roles** — Super Admin, Manager, Order Processor, Read-only Viewer.
  - **Reports/Analytics** — sales by category/product/date range, best sellers, CSV export.
  - **System Settings** — store info, delivery zones & rates, payment-gateway keys, notification templates.
  - **Activity/Audit Log.**
- **CRUD interaction requirements:** modal/slide-over create-edit forms; inline bilingual validation; confirmation dialog before delete (soft-delete/trash); toast success/failure notifications; role-based hiding of restricted actions; search/filter/pagination on every list.

## 4. Full-Stack Architecture
One Cloudflare Worker (Hono + TypeScript) serves the storefront/admin assets and all `/api/*` routes, backed by D1, KV, and R2. GitHub is the source of truth and CI/CD pipeline (GitHub Actions deploys on push to `main`); Cloudflare is the runtime. Provide a Mermaid architecture diagram showing browsers → Worker → D1/KV/R2, outbound calls to payment/courier/notification providers, and the GitHub → Actions → Cloudflare deploy path.

## 5. Data Models
Provide schema tables for: `products`, `product_variants`, `categories`, `collections`, `orders`, `order_items`, `customers`, `addresses`, `reviews`, `coupons`, `admins`, `inventory_log`, `delivery_zones`.

**SKU & Invoice Numbering (required):**
- Every product variant gets an auto-generated, unique **SKU** on creation, editable if needed, following a pattern such as `JWL-[CategoryCode]-[MaterialCode]-[Sequence]` (e.g. `JWL-EAR-GLD-0088`). Enforce uniqueness at the database level.
- Every confirmed order gets an auto-generated, unique, sequential **invoice number**, e.g. `INV-JWL-YYYYMMDD-####`, generated at order confirmation. Provide a downloadable/printable PDF invoice per order with itemized SKUs, quantities, unit prices, discounts, gift-wrap fee, delivery fee, and total.

Include one worked example: an **Order** model as a TypeScript interface, including `sku` on each line item and an `invoiceNumber` field on the order.

**Additional tables required by Sections 16–18:** `abandoned_checkouts` (session id, partial name/phone/address, cart snapshot, last step reached, status: Open/Recovered/Ignored, linked order id if later completed); `order_confirmation_attempts` (order id, attempted at, outcome: no-answer/confirmed/declined, staff member); `return_requests` (order id, reason, status, refund amount); `stock_notify_requests` (product/variant id, phone or email, notified at); `referral_codes` (code, owning customer, uses, reward). Add `utm_source`, `utm_medium`, `utm_campaign`, and `confirmation_method` columns to `orders`; add `total_orders`, `delivered_count`, `refused_or_returned_count`, and `risk_level` to `customers`.

## 6. Payments, Delivery & Third-Party Integrations
- Payments: COD, bKash, Nagad, Rocket, card via SSLCommerz (or similar).
- Delivery: Steadfast as primary courier, Pathao Courier and RedX as alternatives; tracking-ID sync and status webhooks; consider signature-on-delivery for higher-value orders.
- Live delivery-fee calculation by Division/District/Upazila.
- SMS/WhatsApp order-confirmation and status-update notifications.
- Optional: Cloudflare Workers AI for an auto-generated bilingual product description from material/style attributes, or a "style match" recommendation widget.

## 7. Security & Compliance
Align with the NIST Cybersecurity Framework (Govern, Identify, Protect, Detect, Respond, Recover), scoped for retail e-commerce: password hashing, HTTPS/HSTS, RBAC on every admin action, secrets in Wrangler secrets, PCI-scope minimization via hosted payment redirects, admin audit logging, rate-limiting on auth/checkout, an incident-response outline, and a D1 backup/restore procedure.

## 8. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E for the checkout flow; JSON fixtures for product-list, create-order, and admin-login endpoints; TypeScript build check required before deploy.

## 9. Dashboard Development Plan
Lightweight stack (vanilla JS/HTML/CSS or Alpine.js/htmx), a small charting library (Chart.js) for analytics, full usability on tablet/large phone, and defined loading/empty/error states for every view.

## 10. File & Folder Structure
```
[your-brand-slug]/
├── worker/
│   ├── src/
│   │   ├── routes/          (products, orders, customers, auth, admin, collections)
│   │   ├── lib/              (payment, courier, sms integrations, sku + invoice generators)
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
Earrings, Necklaces & Pendants, Bangles & Bracelets, Rings, Anklets, Hair Accessories, Jewelry Sets — adjustable starting points.

## 14. Phased Roadmap
- **Phase 1 (MVP):** catalog, cart, COD checkout, basic admin CRUD for products/orders/categories, SKU + invoice generation live.
- **Phase 2:** bKash/Nagad/SSLCommerz, Steadfast API, coupons, reviews, collections/sets, reports.
- **Phase 3:** AI-assisted descriptions, style-match recommender, loyalty program, PWA/offline support.

## 15. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; keep all copy bilingual and plain-language with large touch targets; start from a clean checkout before editing existing code.

## 16. Fraud Prevention & Abandoned-Checkout Capture
COD-heavy markets see a meaningful share of fake or prank orders, and a lot of recoverable revenue sits in checkouts that never finish. Build for both:
- **Phone verification at checkout:** send an SMS OTP to the phone number entered at checkout before an order can move past "Pending"; an unverified number blocks automatic confirmation.
- **Courier fraud-check lookup:** before confirming a COD order, check the customer's phone number against a courier/fraud-checker service (Steadfast, Pathao, and third-party aggregators expose phone-number lookups showing past delivery-success vs. return/refusal rates) and surface that history to the admin on the order screen.
- **Order-risk scoring:** track each customer's lifetime orders placed vs. delivered vs. returned/refused; compute a simple risk score (Low/Medium/High) shown as a badge next to the customer and on their orders; require a manual confirmation call before dispatch for anything above "Low."
- **Trusted-customer fast lane:** customers with a strong delivery history (e.g. 3+ successfully delivered orders, no refusals) can skip the manual-confirmation step, so good customers aren't slowed down by friction built for risky ones.
- **Velocity & bot checks:** flag multiple orders in a short window from the same phone number, address, or IP; add Cloudflare Turnstile to the checkout form to block scripted/bot order spam.
- **Abandoned-checkout capture:** as a customer fills in the checkout form (name, phone, address) but before they confirm, autosave that partial data server-side (debounced on field blur), tied to a session. If the order isn't confirmed within a set window (e.g. 30 minutes), it surfaces in the admin dashboard as an **Abandoned Checkout** — captured contact info, cart contents, last step reached, and a timestamp — with actions to call/message the customer, mark it "Recovered," or mark it "Not interested." Purge old abandoned sessions automatically after a set retention period.

## 17. Marketing, Ads & Conversion Tracking
Wire in the tracking a real ad budget needs, both client-side and server-side, so it survives ad-blockers and iOS tracking restrictions:
- **Meta Pixel + Conversions API (CAPI):** fire the standard event set (PageView, ViewContent, AddToCart, InitiateCheckout, Purchase, and a Lead event on abandoned-checkout capture) from the browser via Pixel and, in parallel, from the Worker itself via the Conversions API at the moment each event actually happens server-side — same `event_id` on both so Meta deduplicates them, and hash any phone/email sent to CAPI (SHA-256) for matching.
- **Google Ads conversion tracking + GA4:** standard e-commerce events (view_item, add_to_cart, begin_checkout, purchase) in GA4's schema, plus a Google Ads conversion tag on the order-confirmation page.
- **Microsoft Clarity:** heatmaps and session recordings on the storefront for UX insight.
- **UTM capture & attribution:** capture utm_source/medium/campaign on landing and store it against the resulting order, so admin reports can show revenue by campaign, not just by product.
- **Campaign landing pages:** support a lightweight, nav-free landing-page template for specific ad campaigns (a single hero + offer + one CTA), separate from the main site.
- **Click-to-WhatsApp ad support:** Click-to-WhatsApp ads are widely used for Bangladeshi e-commerce marketing — make sure the WhatsApp entry point can carry a referring-ad identifier through to the conversation for attribution.

## 18. Additional Premium Features
Features worth having ready for a client who wants the "advanced" version, not just the MVP:
- **Abandoned-checkout recovery automation:** an SMS/WhatsApp message sent automatically a set number of minutes after an abandoned checkout, with a link back to resume it (optionally with a small time-limited discount).
- **Automated post-delivery review request:** an SMS/WhatsApp message a few days after "Delivered," asking for a review with a direct link.
- **Back-in-stock notifications:** a "notify me" option on out-of-stock products; admin can bulk-notify everyone waiting once restocked.
- **Return/refund self-service:** customers can request a return from their account; admin approves/rejects and it updates order status automatically.
- **Referral program:** a give-and-get discount code customers can share.
- **Admin two-factor authentication:** required for Super Admin and Manager roles, since these accounts touch money and customer data.
- **Google Merchant Center feed:** an auto-generated product feed for free (and, later, paid) Google Shopping listings.
- **PWA push notifications:** order-status and promotional pushes with no SMS cost, for customers who install the site as an app.
- **VAT/tax-inclusive invoicing:** an optional tax line on the invoice for a VAT-registered business, off by default, configurable in System Settings.
- **Honesty guardrail on urgency signals:** if the storefront shows "X left in stock" or "X people viewing," that number must come from real data, never hardcoded or randomly generated — fabricated urgency is a trust risk, not a conversion trick worth taking.

## 19. Acceptance Checklist
- [ ] Bilingual storefront and admin, working language toggle
- [ ] A COD order can't reach "Confirmed" without either OTP verification or a logged manual confirmation call
- [ ] Abandoned checkouts (partial name/phone/address with no confirmed order) are visible and actionable in the admin dashboard
- [ ] Meta Pixel and the Conversions API fire matching events with a shared event_id
- [ ] GA4 and Microsoft Clarity are both wired up on the live storefront
- [ ] No urgency/stock-count display is fabricated — it reflects real data
- [ ] "Refused at delivery," "Returned," and "Cancelled" are tracked as distinct order outcomes, not lumped together
- [ ] A non-technical owner can complete first-time setup (first product, delivery zone, one payment method) without outside help
- [ ] Every list view (orders, customers, abandoned checkouts) has a CSV export
- [ ] Guest checkout with Division/District/Upazila address and live delivery-fee calculation
- [ ] COD works end-to-end; at least one MFS integration functional or clearly stubbed
- [ ] Every product has a unique, correctly-formatted SKU generated automatically
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice
- [ ] Full CRUD on products (with material/weight), categories, collections, orders, customers, coupons
- [ ] "Complete the look" cross-sell shows correctly-matched set items
- [ ] Admin usable on tablet/large phone
- [ ] No secrets committed to the repository

## 20. Owner-Friendly Operations & Onboarding
The person running this day to day has no coding background, so the system needs to guide them, not just expose settings to them:
- **Guided first-run setup:** a short checklist on first login — add your first product, set a delivery zone and rate, connect a payment method, connect WhatsApp — each step a single simple action, not a settings form to interpret.
- **A plain-language "Health Check" strip on the dashboard home:** simple status lines like "Payment: connected" / "Facebook tracking: not connected yet" / "Courier: connected," so the owner knows what's live without understanding what any of it technically does.
- **Plain-language risk badges, not scores:** show a customer's risk as 🟢 Trusted / 🟡 New / 🔴 Verify before shipping, with a one-line plain-language reason on tap — never a raw number or technical term.
- **A "Needs your attention today" home view:** one prioritized list — orders awaiting a confirmation call, abandoned checkouts to follow up, low-stock items, pending reviews — ahead of (or instead of) chart-heavy analytics, since that's what a busy owner actually needs first each morning.
- **One-tap actions on every order:** a `tel:` "Call customer" button, and a WhatsApp button with pre-written, editable bilingual templates (order confirmed / shipped / out for delivery), so staff never have to type a message from scratch.
- **Bulk actions with a plain confirmation:** select several orders and mark them all "Shipped" (or similar) in one step, with a one-line "You're about to mark 5 orders as Shipped — continue?" check.
- **CSV/Excel export everywhere there's a list**, not just in Reports — orders, customers, abandoned checkouts — since exporting to a spreadsheet is often more comfortable than reading an on-screen dashboard.
- **Optional phone + OTP login for day-to-day staff roles** (Order Processor, Read-only Viewer), so people unused to managing passwords aren't blocked by a forgotten one; keep password + 2FA for Super Admin/Manager, per Section 18.

## Output Format
Single structured document: labeled sections matching the numbering above; Mermaid diagrams where applicable; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree; a short glossary for any Bangla UI terms used.

## Notes
- Treat COD plus MFS as the primary payment reality; card payments are secondary.
- The SKU and invoice-numbering system is a hard requirement, not optional.
- Fraud-prevention checks (OTP or a logged manual confirmation before COD dispatch) and honest urgency-signal data are hard requirements too — don't drop them for a "simpler" build.
- Keep this scoped to fashion/imitation jewelry; flag to the requester if genuine gold/precious-metal inventory is intended, since that needs hallmarking and insured-shipping handling beyond this spec.
- Flag any assumption (palette details, exact delivery-fee tiers, exact category list) as adjustable and confirm with the requester before finalizing.