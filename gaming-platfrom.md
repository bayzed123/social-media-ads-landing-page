# CWB Gaming — Multi-Vendor Game Top-Up Marketplace (A–Z Build Prompt)

> **Scope note:** this covers gaming currency, top-ups, and redeem codes only — PUBG Mobile UC, Free Fire Diamonds, Mobile Legends Diamonds, Call of Duty Mobile CP, and gift/redeem codes (Google Play, Steam, etc.). It does **not** cover buying/selling cryptocurrency, USD, or PayPal balances — Bangladesh Bank has repeatedly stated cryptocurrency transactions aren't authorized there and may fall under the Money Laundering Prevention Act, and foreign-currency dealing outside a licensed money changer is restricted under the Foreign Exchange Regulation Act. Keep this build to game-currency and digital gift codes.

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack, multi-vendor marketplace with a platform admin dashboard, a gated seller dashboard, and high-security payment handling, for:

- **Brand name:** CWB Gaming
- **Business type:** Multi-vendor marketplace for gaming currency, top-ups, and redeem codes
- **Location:** [Your Location], Bangladesh
- **Primary market:** Bangladesh mobile gamers (PUBG Mobile, Free Fire, Mobile Legends, and other popular titles), mobile-first, Bangla + English speaking

The system must be built on **Cloudflare** for hosting/runtime (Workers, D1, KV, R2), with source code hosted on **GitHub** and deployed via GitHub Actions — one Cloudflare Worker (Hono + TypeScript) serving the public marketplace, the platform admin dashboard, and the seller dashboard as role-routed surfaces of the same app, with all `/api/*` routes behind it. Every dashboard must support full CRUD for its resources, with validation, confirmation prompts, and success/failure feedback.

The deliverable must include the following:

## 1. Brand, Scope & Business Requirements
- Define the brand identity, a short tagline, and a gaming-culture-appropriate logo concept direction.
- Target customer: mobile gamers buying in-game currency and gift/redeem codes, price- and trust-sensitive, usually buying on impulse mid-session.
- All customer-facing and dashboard UI text bilingual (Bangla + English) with a language toggle, plain language throughout.
- **Marketplace model:** the platform itself can list an "Official Store" (platform-fulfilled), and independent sellers can register, get KYC-approved, and list their own offers against the platform's master game/product catalog.
- Business model: payment-before-delivery only (bKash, Nagad, Rocket, card via SSLCommerz) — **no Cash on Delivery**, since there's nothing to physically deliver and releasing a code before payment clears is the single biggest fraud exposure in this business.
- Team is small and non-technical — every admin workflow must be operable with no coding knowledge, large touch targets, plain-language labels.
- SEO is a first-class requirement here, not an afterthought — see Section 10.

## 2. Public Marketplace Website — Description & Layout
- **Visual style:** dark-mode-first, energetic gamer aesthetic — near-black base (`#0D0E12`-ish) with electric-cyan and neon-purple accents and a vivid magenta highlight for calls to action; sharp edges, subtle glow on hover, bold condensed headline type.
- **2026 baseline UX expectations:** mobile-first and thumb-friendly; adaptive light/dark theming via color tokens (gaming audiences skew dark-mode by default, so make light mode the override, not the default); fast, lightweight pages — this audience bounces instantly on slow load.
- Site map: Home, Browse by Game, Product/Denomination listing (per game), Product Detail (multiple seller offers, sorted by price), Cart, Checkout, Account (Order History, Wishlist), public Seller storefront pages, About/Contact, Search.
- **Homepage:** hero, a grid of popular games, trending top-ups, top-rated sellers, a simple "how it works" strip (pick game → enter your player ID → pay → instant delivery).
- **Product listing (per game + denomination):** every seller currently offering that exact product, sorted by price by default, each row showing seller name/rating/fulfillment speed — this price-comparison view is itself a strong trust and SEO asset (see Section 10).
- **Product detail / checkout:** a **player ID / in-game username field**, validated against that game's known ID format where possible; delivery-method choice where a seller offers both (direct top-up vs. redeem code); payment method selection (bKash, Nagad, Rocket, card); an order summary repeating the player ID back for the buyer to double-check before paying, since a wrong ID is unrecoverable once delivered.
- **Order status page:** live state — Payment Pending → Payment Confirmed → Delivering → Delivered (or Held for Review, see Section 8) — with the redeem code or top-up confirmation shown here and emailed/SMS'd once released.
- **Account area:** order history (with past delivered codes still visible), wishlist, saved player IDs per game for faster repeat checkout.

## 3. Platform Admin Dashboard — Description & Layout
- **Visual style:** dark theme matching the storefront, cyan/purple accents on active states, dense data-forward layout (platform operators need to scan a lot of orders fast).
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Seller Applications, Sellers, Games & Products, Orders, Disputes, Commission & Payouts, Fraud & Risk, Reviews, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with platform-wide KPIs (today's GMV, active sellers, pending seller applications, open disputes, orders held for fraud review), a sales chart, and a recent-orders table.
  - **Right sidebar:** search, admin profile dropdown, notifications (new seller application, new dispute, order flagged for review), staff online status.
- **CRUD modules:**
  - **Seller Applications** — review submitted KYC documents and business info, approve/reject with a reason, request more info.
  - **Sellers** — manage all seller accounts: suspend/reactivate, view their full order and dispute history, adjust their commission tier.
  - **Games & Products** — the master catalog of games and the exact denominations/products sellers are allowed to list against (platform controls this list, sellers can't invent arbitrary products).
  - **Orders** — platform-wide view across all sellers; ability to intervene (force-refund, reassign to another seller, manually release a held order after review).
  - **Disputes** — a queue of buyer-raised disputes with order context, seller response, and a resolution action (refund buyer, pay seller, split).
  - **Commission & Payouts** — set commission rules (per game or per seller tier), review and approve/reject payout requests, see pending-payable balances.
  - **Fraud & Risk** — orders held for manual review, flagged buyers (chargeback history, velocity triggers), flagged sellers (complaint rate).
  - **Reviews** — moderate seller reviews.
  - **Staff & Roles** — Super Admin, Operations Manager, Order/Dispute Reviewer, Read-only Viewer.
  - **Reports/Analytics** — GMV by game/seller/date range, commission earned, dispute rate, CSV export.
  - **System Settings** — supported games list, default commission rates, payment-gateway keys, notification templates, fraud-review thresholds.
  - **Activity/Audit Log.**

## 4. Seller Dashboard — Description & Layout
- **Access is gated:** a new seller signs up, submits KYC (ID document, contact verification, payout bank/mobile-wallet details, agreement to platform terms), and the dashboard stays locked to a read-only "Application Pending" screen until a platform admin approves it (Section 3). This gate is the "advanced verification" step — it exists to keep the marketplace trustworthy, not to be bureaucracy for its own sake.
- **Visual style:** same dark theme as the admin dashboard, scoped strictly to that seller's own data.
- **Once approved, modules:**
  - **Dashboard home** — this seller's own sales KPIs, pending orders awaiting fulfillment, current payable balance.
  - **My Listings** — CRUD on which game+denomination products they sell, their price, delivery method (direct top-up or redeem code), stock/availability toggle (and redeem-code stock count where applicable); auto-generated SKU per listing (see Section 5).
  - **My Orders** — orders routed to them: mark fulfilled, enter/send the redeem code or confirm a direct top-up is complete, with an SLA timer (e.g. 30 minutes) and auto-escalation to the platform if missed.
  - **Payouts** — request a payout of their available balance, see payout history and status.
  - **My Reviews** — see buyer feedback.
  - **Settings** — public store name/profile shown to buyers, notification preferences.
- Sellers never see a buyer's payment details (card/wallet info) — only what they need to fulfill: player ID, product, quantity. This separation is a hard requirement, not a nice-to-have.

## 5. Full-Stack Architecture
One Cloudflare Worker (Hono + TypeScript) serves the public marketplace, the admin dashboard, and the seller dashboard as role-routed surfaces, backed by D1 (relational data), KV (sessions/cache), and R2 (seller KYC documents, stored access-controlled, not public). GitHub is the source of truth and CI/CD pipeline (GitHub Actions deploys on push to `main`); Cloudflare is the runtime. Provide a Mermaid architecture diagram showing buyer/seller/admin browsers → Worker → D1/KV/R2, outbound calls to payment and notification providers, and the GitHub → Actions → Cloudflare deploy path.

## 6. Data Models
Provide schema tables for: `games` (name, icon, player-ID format rule), `products` (game id, denomination/description — the master catalog), `sellers` (business info, KYC status, payout details, commission tier, rating), `seller_listings` (seller id, product id, price, stock/availability, delivery method, SKU), `orders`, `order_items` (player id entered, seller_listing fulfilled, delivered code/confirmation, fulfillment status), `payouts`, `commission_rules`, `disputes`, `reviews`, `admins`.

**SKU & Invoice Numbering (required):**
- Every seller listing gets an auto-generated, unique **SKU** on creation, following a pattern such as `GAME-[GameCode]-[Denomination]-[SellerCode]` (e.g. `GAME-PUBG-660UC-S014`). Enforce uniqueness at the database level.
- Every confirmed order gets an auto-generated, unique, sequential **invoice number**, e.g. `INV-CWB-YYYYMMDD-####`, generated the moment payment is confirmed. Provide a downloadable/printable PDF invoice per order.

**Additional tables:** `abandoned_checkouts` (session id, partial player-ID/product selection, status); `fraud_flags` (order id, reason, triggered at); `return_requests`/dispute linkage. Add `utm_source`/`utm_medium`/`utm_campaign` to `orders`; add `total_orders`, `chargeback_count`, and `risk_level` to `customers`.

Include one worked example: an **Order** model as a TypeScript interface, including the `playerId`, `sellerListingSku`, `deliveryMethod`, `invoiceNumber`, and a `fulfillmentStatus` enum.

## 7. Payments & Fulfillment
- Payment methods: bKash, Nagad, Rocket, card via SSLCommerz (or similar) — **payment confirmation, not just initiation, must be received from the gateway before any code or top-up is released.** This is the single most important rule in this build.
- Fulfillment mechanics, support all three: (a) API-integrated instant top-up where an official provider API exists; (b) manual seller fulfillment with an SLA timer and auto-escalation; (c) pre-stocked redeem-code delivery, revealed to the buyer the instant payment clears, decrementing the seller's code inventory.
- Webhooks from the payment gateway should be the source of truth for "paid," not the client-side redirect, to avoid delivery-before-payment race conditions.

## 8. Fraud Prevention & Transaction Security
Instant-delivery digital goods are a classic target for chargeback and card-testing ("carding") fraud — someone pays with a stolen card, the code is irreversible the moment it's shown, and the chargeback lands weeks later. Build for that reality:
- **Payment-before-delivery, enforced server-side**, never trusted from the client.
- **Purchase-velocity limits:** cap total order value for a new customer until they build a clean history; flag repeated small-value attempts from the same card/IP/device in a short window (classic card-testing pattern).
- **Delayed release for first-time buyers or unusually large orders:** hold the code/top-up for a short manual-review window instead of instant release.
- **Chargeback monitoring:** log dispute/chargeback outcomes per buyer and per payment method; auto-flag buyers with prior chargebacks for manual review on future orders.
- **Player-ID sanity checks:** validate the entered ID's format (and existence via API where the game supports a lookup) before accepting payment, to cut down on "wrong ID" disputes.
- **Device/IP fingerprinting** and Cloudflare Turnstile on checkout.
- **Two-factor authentication required for both Admin and Seller accounts** — seller accounts control payout banking details and are a real takeover target.
- **Seller-side fraud monitoring:** track each seller's buyer-complaint rate and "marked fulfilled but buyer says not received" rate; auto-flag for review above a threshold.
- Standard platform security: password hashing, HTTPS/HSTS, RBAC on every admin and seller action, secrets in Wrangler secrets, admin/seller audit logging, rate-limiting on auth and checkout, an incident-response outline, and a D1 backup/restore procedure — aligned to the NIST Cybersecurity Framework's Govern/Identify/Protect/Detect/Respond/Recover functions.

## 9. Seller Onboarding, KYC & Trust
- KYC requirements for approval: ID verification, contact verification, payout account details, agreement to platform terms; optionally a refundable security deposit held by the platform as a dispute-resolution buffer, common practice on real marketplaces of this kind.
- Public seller rating/review system, visible on both the seller's storefront and in the price-comparison listing.
- A "Verified Seller" badge unlocked only after a minimum clean order history (e.g. 50 orders, under a set dispute rate) — never awarded on request.
- Dispute process: buyer raises a dispute from their order, the seller gets a chance to respond, platform admin mediates and can hold or release the seller's payout for that order pending resolution.

## 10. Marketing, SEO & Conversion Tracking
This niche is won largely on search ranking, so treat it as core, not an add-on:
- **Dedicated landing page per game + denomination** (e.g. "660 UC price in Bangladesh," "Free Fire Diamond bKash") — this is how top-up sites actually rank; the price-comparison table from Section 2 is genuine unique content for each of these pages, not duplicate content.
- `Product` JSON-LD with live price on every listing page; fast page load (Core Web Vitals matter heavily for ranking here); fresh, genuine review content.
- Meta tags, Open Graph/Twitter cards, sitemap.xml, robots.txt.
- **Meta Pixel + Conversions API (CAPI):** fire the standard event set (PageView, ViewContent, AddToCart, InitiateCheckout, Purchase) from the browser and, in parallel, from the Worker via CAPI — shared `event_id` for deduplication, hashed phone/email for matching.
- **Google Ads conversion tracking + GA4**, **Microsoft Clarity** for heatmaps/session recordings, **UTM capture & attribution** stored against each order.
- Click-to-WhatsApp support button with referring-ad attribution.

## 11. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E specifically covering the payment-confirmation-before-delivery path, since that's the highest-stakes part of the system; JSON fixtures for product-list, create-order, seller-approval, and payout endpoints; TypeScript build check required before deploy.

## 12. Dashboard Development Plan
Lightweight stack (vanilla JS/HTML/CSS or Alpine.js/htmx) across all three surfaces (storefront, admin, seller); a small charting library (Chart.js) for analytics; full usability on tablet/large phone for all three dashboards; defined loading/empty/error states everywhere.

## 13. File & Folder Structure
```
cwb-gaming/
├── worker/
│   ├── src/
│   │   ├── routes/          (games, products, listings, orders, sellers, payouts, disputes, auth, admin)
│   │   ├── lib/              (payment, sms integrations, sku + invoice generators, fraud checks)
│   │   └── index.ts
│   ├── migrations/
│   └── wrangler.toml
├── public/                   (buyer-facing marketplace)
├── admin/                    (platform admin dashboard)
├── seller/                   (seller dashboard)
├── tests/
├── .github/workflows/
└── docs/
```

## 14. Development Environment & Deployment Setup
Node.js version, `wrangler` CLI install/login, `wrangler d1 create`, KV namespace, R2 bucket (with access control for KYC documents), sample `wrangler.toml` and `.dev.vars`, GitHub branch strategy, a GitHub Actions workflow that tests/builds/migrates/deploys on push to `main`, and custom-domain setup via Cloudflare DNS.

## 15. Suggested Game & Product Catalog
PUBG Mobile (UC denominations), Free Fire (Diamond denominations), Mobile Legends (Diamond denominations), Call of Duty Mobile (CP), plus general redeem codes (Google Play gift cards, Steam wallet codes) — adjustable starting points, expand as the platform grows.

## 16. Phased Roadmap
- **Phase 1 (MVP):** a single "Official Store" seller (the platform itself), a handful of popular games, manual fulfillment, basic storefront + admin, payment-confirmation-before-delivery enforced from day one.
- **Phase 2:** open the marketplace — seller KYC onboarding, seller dashboard, commission/payout system, redeem-code inventory, dispute handling.
- **Phase 3:** API-integrated instant top-up for games with official provider APIs, AI-assisted fraud scoring, loyalty/cashback points.

## 17. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; keep all copy bilingual and plain-language with large touch targets; start from a clean checkout before editing existing code.

## 18. Additional Premium Features
- **Live order-status progress** on the order page (Paid → Delivering → Delivered).
- **Price-comparison sorting** across sellers for the same product (already core to Section 2, worth calling out as a retention feature too).
- **Loyalty/cashback points** on repeat purchases.
- **Referral program.**
- **Automated low-stock alerts to sellers** for redeem-code inventory.
- **Admin dispute-resolution toolkit:** side-by-side buyer/seller evidence, one-click partial-refund options.
- **Honesty guardrail on urgency signals:** any "X left," "Y people buying now" display must reflect real data, never fabricated.

## 19. Owner/Platform-Operator-Friendly Operations & Onboarding
The person running this day to day has no coding background, so the system needs to guide them, not just expose settings to them:
- **Guided first-run setup:** add the first game/product to the catalog, connect a payment method, set a commission rate — one simple action per step.
- **A plain-language "Health Check" strip** on the admin dashboard home ("Payment: connected" / "Facebook tracking: not connected yet").
- **Plain-language risk badges, not scores**, for both buyers (🟢 Trusted / 🟡 New / 🔴 Verify) and sellers (🟢 Good standing / 🟡 Watch / 🔴 Under review).
- **A "Needs your attention today" home view:** pending seller applications, open disputes, orders held for fraud review, pending payout requests — ahead of chart-heavy analytics.
- **One-tap actions:** approve/reject a seller application, release a held order, approve a payout — each a single click with a plain confirmation, not a multi-step form.
- **CSV/Excel export everywhere there's a list** — orders, sellers, payouts, disputes.
- **Optional phone + OTP login for day-to-day staff roles**, password + 2FA kept mandatory for Super Admin and for all Seller accounts given the payout-security stakes.

## 20. Acceptance Checklist
- [ ] Bilingual storefront, admin, and seller dashboards, working language toggle
- [ ] No code or top-up is ever released before the payment gateway confirms success server-side
- [ ] A new seller cannot access their dashboard beyond "Application Pending" until KYC is approved by an admin
- [ ] Every seller listing has a unique, correctly-formatted SKU generated automatically
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice
- [ ] Full CRUD on games/products, seller listings, orders, sellers, payouts, disputes, commission rules
- [ ] Purchase-velocity and card-testing checks are active on checkout, with a manual-review hold path
- [ ] Sellers cannot see buyer payment details, only fulfillment-relevant data
- [ ] A dedicated landing page exists per game + denomination with live price comparison
- [ ] Meta Pixel and the Conversions API fire matching events with a shared event_id
- [ ] A non-technical owner can approve a seller, release a held order, and approve a payout without outside help
- [ ] Every list view has a CSV export
- [ ] No urgency signal is fabricated
- [ ] No secrets or KYC documents are committed to the repository or exposed publicly via R2

## Output Format
Single structured document: labeled sections matching the numbering above; Mermaid diagrams where applicable; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree; a short glossary for any Bangla UI terms used.

## Notes
- No Cash on Delivery anywhere in this build — payment-before-delivery is the core fraud control and isn't optional.
- The SKU and invoice-numbering system is a hard requirement, not optional.
- The fraud-prevention and seller-KYC-gating requirements in Sections 8–9 are hard requirements — don't drop them for a "simpler" build; they're the difference between a trustworthy marketplace and one that gets drained by carding fraud in its first month.
- This build does not include cryptocurrency or foreign-currency (USD/PayPal) trading — see the scope note at the top.
- Flag any assumption (palette details, exact commission rates, exact game list) as adjustable and confirm with the requester before finalizing.