# [Your Brand Name] — Sustainable Home & Eco-Lifestyle E-commerce (A–Z Build Prompt)

> Replace `[Your Brand Name]` and `[Your Location]` throughout before using. Example name directions: "Shonali Earth" ("shonali" = golden, as in golden jute fibre), "RootHome", "GreenNest". Bangladesh is a major global source of jute and handwoven natural fibre — a genuinely strong local angle for this niche, not just an imported trend.

Create a comprehensive, detailed, end-to-end (A–Z) plan and specification — and, where possible, a working implementation — for a full-stack e-commerce website with a fully-featured admin dashboard, for:

- **Brand name:** [Your Brand Name]
- **Business type:** Sustainable home & eco-lifestyle retail (bamboo/jute kitchenware, reusable bags/bottles, natural cleaning products, handwoven jute/cane home decor)
- **Location:** [Your Location], Bangladesh
- **Primary market:** Bangladesh, eco-conscious urban shoppers and gift buyers, mobile-first, Bangla + English speaking

The system must be built on **Cloudflare** for hosting/runtime (Workers, D1, KV, R2), with source code hosted on **GitHub** and deployed via GitHub Actions — one Cloudflare Worker (Hono + TypeScript) serving both the static storefront/admin app and all `/api/*` routes. The admin dashboard must support full CRUD for every resource below, with validation, confirmation prompts, and success/failure feedback.

The deliverable must include the following:

## 1. Brand, Market & Business Requirements
- Define the brand identity, a short tagline, and an earthy, grounded, premium-eco logo concept direction.
- Target customer: eco-conscious urban shoppers who compare brands on materials and sourcing, plus gift buyers looking for distinctive, story-backed items.
- All customer-facing and admin UI text bilingual (Bangla + English) with a language toggle, plain language throughout.
- Business model: Cash on Delivery as default, plus mobile financial services and card payment.
- Feature [Your Location] on the Contact/About page, footer, and structured data.
- Team is small and non-technical — every admin workflow must be operable with no coding knowledge, large touch targets, plain-language labels.
- **Trust note — treat as a hard requirement:** only display a sustainability badge (plastic-free, biodegradable, handmade, fair-trade, etc.) on a product if it's genuinely true for that product; never use a badge as decoration.

## 2. Public Website — Description & Layout
- **Visual style:** earthy, natural, premium. A Cloud-Dancer off-white base (Pantone's 2026 Color of the Year) layered with Mocha Mousse (a warm, sophisticated brown still trending into 2026) and Verdant Green as accents; woven/textile-inspired background textures used sparingly; a clean serif-plus-sans pairing that reads as craft, not clinical.
- **2026 baseline UX expectations to build in from the start:** mobile-first and thumb-friendly; adaptive light/dark "mood mode" theming driven by color tokens; short videos showing materials/craftsmanship (how a jute basket is woven, a bamboo product in use) alongside photos; fast, lightweight pages.
- Site map: Home, Shop (category & subcategory), Product Detail, Cart, Checkout, Account (Login/Register, Orders, Wishlist, Addresses), About/Contact (with a sourcing story), Search Results, optional "Living Simply" blog.
- **Homepage:** hero banner telling the sourcing/craftsmanship story, category tiles (Kitchen & Dining, Cleaning, Bags & Totes, Home Decor, Personal Care, Gift Sets), best sellers, a sustainability-impact strip (e.g. "X plastic bags avoided this month" — only if genuinely tracked), customer reviews, newsletter signup, footer with location and policies.
- **Product listing:** filters by category, material (jute/bamboo/cane/cotton), price, and genuinely-held certification; sort by price/newest/popularity/rating.
- **Product detail page:** image gallery plus a short craftsmanship video where available, a **"material & sourcing story"** block (what it's made from, where — e.g. handwoven jute from a named region), genuinely-earned sustainability badges, price, stock status, Add to Cart/Buy Now, care instructions, customer reviews, related/upsell products.
- **Gift-set builder:** let a customer bundle a few items into a gift set with a note card option.
- **Cart & checkout:** guest checkout, Bangladesh address form with tiered delivery fee, a gift-wrap/note-card add-on, payment method selection (COD, bKash, Nagad, Rocket, card via SSLCommerz), order summary and confirmation with SMS/WhatsApp notification.
- **Account area:** order history with tracking, wishlist, saved addresses.

## 3. Admin Dashboard — Description & Layout
- **Visual style:** warm and grounded — off-white background, Mocha Mousse and Verdant Green accents, soft rounded cards, calm and uncluttered.
- **Three-pane layout:**
  - **Left sidebar** (collapsible): Dashboard, Products, Categories, Gift Sets, Orders, Customers, Coupons, Certifications, Inventory, Reviews, Reports/Analytics, Staff & Roles, System Settings, Help Center.
  - **Center panel:** dashboard home with a monthly sales chart, KPI cards (today's orders, revenue, pending COD, low-stock alerts), recent-orders table, top-selling products.
  - **Right sidebar:** search, admin profile dropdown, notifications (new order, low stock, new review), staff online status.
- **CRUD modules:**
  - **Products** — name, bilingual description, category, material, price, discount, variants, sourcing-story text, certification badges (checkboxes, each requiring an attached certificate/evidence before it can be enabled), images (plus optional short video), status, SEO slug, auto-generated SKU (see Section 5); bulk CSV import/export.
  - **Categories & Subcategories** — nested, drag-to-reorder.
  - **Gift Sets** — bundle multiple products into a purchasable set, with an optional note-card field.
  - **Orders** — line items with SKUs, customer, address, payment method/status, gift-wrap flag, delivery pipeline (Pending → Confirmation Attempted [logged call/OTP outcomes, one-tap buttons — see Section 20] → Confirmed → Packed → Shipped [courier + tracking, ingesting courier sub-statuses like Out for Delivery] → Delivered, with three distinct off-ramps: Cancelled, Refused at Delivery, and Returned, kept separate for risk scoring and stock), **auto-generated invoice number and a printable PDF invoice** (see Section 5), refund handling.
  - **Customers** — profile, order history, block/unblock.
  - **Coupons/Discounts** — percentage/flat, minimum order, expiry, usage limits.
  - **Certifications** — manageable list of certification types with their badge icons.
  - **Inventory** — per-SKU stock, low-stock alerts, adjustment log.
  - **Reviews** — approve/reject/reply.
  - **Staff & Roles** — Super Admin, Manager, Order Processor, Read-only Viewer.
  - **Reports/Analytics** — sales by category/product/date range, best sellers, CSV export.
  - **System Settings** — store info, delivery zones & rates, payment-gateway keys, notification templates.
  - **Activity/Audit Log.**
- **CRUD interaction requirements:** modal/slide-over create-edit forms; inline bilingual validation; confirmation dialog before delete (soft-delete/trash); toast success/failure notifications; role-based hiding of restricted actions; search/filter/pagination on every list. A certification checkbox must be blocked from saving until its supporting evidence is attached.

## 4. Full-Stack Architecture
One Cloudflare Worker (Hono + TypeScript) serves the storefront/admin assets and all `/api/*` routes, backed by D1, KV, and R2. GitHub is the source of truth and CI/CD pipeline (GitHub Actions deploys on push to `main`); Cloudflare is the runtime. Provide a Mermaid architecture diagram showing browsers → Worker → D1/KV/R2, outbound calls to payment/courier/notification providers, and the GitHub → Actions → Cloudflare deploy path.

## 5. Data Models
Provide schema tables for: `products`, `product_variants`, `categories`, `gift_sets`, `orders`, `order_items`, `customers`, `addresses`, `reviews`, `coupons`, `admins`, `inventory_log`, `certifications`, `delivery_zones`.

**SKU & Invoice Numbering (required):**
- Every product variant gets an auto-generated, unique **SKU** on creation, following a pattern such as `ECO-[CategoryCode]-[MaterialCode]-[Sequence]` (e.g. `ECO-KIT-JUTE-0011`). Enforce uniqueness at the database level.
- Every confirmed order gets an auto-generated, unique, sequential **invoice number**, e.g. `INV-ECO-YYYYMMDD-####`, generated at order confirmation. Provide a downloadable/printable PDF invoice per order with itemized SKUs, quantities, unit prices, discounts, delivery fee, and total.

**Additional tables for fraud/marketing/premium features:** `abandoned_checkouts`; `order_confirmation_attempts`; `return_requests`; `stock_notify_requests`; `referral_codes`. Add `utm_source`/`utm_medium`/`utm_campaign` and `confirmation_method` to `orders`; add `total_orders`, `delivered_count`, `refused_or_returned_count`, and `risk_level` to `customers`.

Include one worked example: an **Order** model as a TypeScript interface, including `sku` on each line item and an `invoiceNumber` field.

## 6. Payments, Delivery & Third-Party Integrations
- Payments: COD, bKash, Nagad, Rocket, card via SSLCommerz (or similar).
- Delivery: Steadfast as primary courier, Pathao Courier and RedX as alternatives; tracking-ID sync and status webhooks; consider extra packaging-protection instructions for fragile handwoven items.
- Live delivery-fee calculation by Division/District/Upazila.
- SMS/WhatsApp order-confirmation and status-update notifications.
- Optional: Cloudflare Workers AI for an auto-generated bilingual product description from material/sourcing attributes.

## 7. Security & Compliance
Align with the NIST Cybersecurity Framework (Govern, Identify, Protect, Detect, Respond, Recover), scoped for retail e-commerce: password hashing, HTTPS/HSTS, RBAC on every admin action, secrets in Wrangler secrets, PCI-scope minimization via hosted payment redirects, admin audit logging, rate-limiting on auth/checkout, an incident-response outline, and a D1 backup/restore procedure. Treat the certification-integrity rule in Section 1 as part of this section too.

## 8. Testing Strategy
Unit/integration tests for Worker routes (Vitest + Miniflare or `@cloudflare/vitest-pool-workers`); Playwright E2E for the checkout flow; JSON fixtures for product-list, create-order, and admin-login endpoints; TypeScript build check required before deploy; a test confirming a certification badge cannot be saved without supporting evidence attached.

## 9. Dashboard Development Plan
Lightweight stack (vanilla JS/HTML/CSS or Alpine.js/htmx), a small charting library (Chart.js) for analytics, full usability on tablet/large phone, and defined loading/empty/error states for every view.

## 10. File & Folder Structure
```
[your-brand-slug]/
├── worker/
│   ├── src/
│   │   ├── routes/          (products, orders, customers, auth, admin, gift-sets)
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
Kitchen & Dining, Cleaning & Household, Bags & Totes, Home Decor, Personal Care, Gift Sets — adjustable starting points.

## 14. Phased Roadmap
- **Phase 1 (MVP):** catalog, cart, COD checkout, basic admin CRUD for products/orders/categories, SKU + invoice generation live.
- **Phase 2:** bKash/Nagad/SSLCommerz, Steadfast API, coupons, reviews, gift-set builder, certification management, reports.
- **Phase 3:** AI-assisted (fact-checked) sourcing descriptions, loyalty program, PWA/offline support.

## 15. Development & Delivery Conventions
Output complete, untruncated files (never diffs); validate with a TypeScript/build check before presenting code as finished; keep all copy bilingual and plain-language with large touch targets; start from a clean checkout before editing existing code.

## 16. Fraud Prevention & Abandoned-Checkout Capture
COD-heavy markets see a meaningful share of fake or prank orders, and a lot of recoverable revenue sits in checkouts that never finish. Build for both:
- **Phone verification at checkout:** send an SMS OTP before an order can move past "Pending"; an unverified number blocks automatic confirmation.
- **Courier fraud-check lookup:** check the customer's phone number against a courier/fraud-checker service before confirming a COD order, and surface that history on the order screen.
- **Order-risk scoring:** a Low/Medium/High badge per customer based on delivered-vs-refused history; anything above "Low" requires a manual confirmation call before dispatch.
- **Trusted-customer fast lane:** skip manual confirmation for customers with a strong delivery history.
- **Velocity & bot checks:** flag multiple orders in a short window from the same phone/address/IP; add Cloudflare Turnstile to checkout.
- **Abandoned-checkout capture:** autosave partial checkout data server-side as the customer types; if unconfirmed within a set window, it surfaces in the admin dashboard as an **Abandoned Checkout** with follow-up actions.

## 17. Marketing, Ads & Conversion Tracking
- **Meta Pixel + Conversions API (CAPI):** fire the standard event set from the browser and, in parallel, from the Worker via CAPI — shared `event_id` for deduplication, hashed phone/email for matching.
- **Google Ads conversion tracking + GA4:** standard e-commerce events plus a conversion tag on order confirmation.
- **Microsoft Clarity:** heatmaps and session recordings.
- **UTM capture & attribution** stored against each order.
- **Campaign landing pages** for specific ad campaigns.
- **Click-to-WhatsApp ad support** with referring-ad attribution.

## 18. Additional Premium Features
- **Abandoned-checkout recovery automation** (SMS/WhatsApp nudge).
- **Automated post-delivery review request.**
- **Back-in-stock notifications.**
- **Return/refund self-service.**
- **Referral program.**
- **Admin two-factor authentication** for Super Admin and Manager.
- **Google Merchant Center feed.**
- **PWA push notifications.**
- **VAT/tax-inclusive invoicing**, off by default.
- **Honesty guardrail on urgency signals:** real stock counts only, never fabricated — and the same honesty standard applies to any "impact" counter (plastic avoided, trees saved), which must be computed, not invented.

## 19. Acceptance Checklist
- [ ] Bilingual storefront and admin, working language toggle
- [ ] Guest checkout with Division/District/Upazila address and live delivery-fee calculation
- [ ] COD works end-to-end; at least one MFS integration functional or clearly stubbed
- [ ] Every product has a unique, correctly-formatted SKU generated automatically
- [ ] Every order gets a unique, sequential invoice number and a downloadable PDF invoice
- [ ] Full CRUD on products (with material/sourcing/certifications), categories, gift sets, orders, customers, coupons
- [ ] No certification badge can be saved without supporting evidence attached
- [ ] A COD order can't reach "Confirmed" without OTP verification or a logged manual confirmation call
- [ ] Abandoned checkouts are visible and actionable in the admin dashboard
- [ ] Meta Pixel and the Conversions API fire matching events with a shared event_id
- [ ] "Refused at delivery," "Returned," and "Cancelled" are tracked as distinct order outcomes
- [ ] A non-technical owner can complete first-time setup without outside help
- [ ] Every list view has a CSV export
- [ ] No secrets committed to the repository

## 20. Owner-Friendly Operations & Onboarding
The person running this day to day has no coding background, so the system needs to guide them, not just expose settings to them:
- **Guided first-run setup:** first product, delivery zone, payment method, WhatsApp — one simple action per step.
- **A plain-language "Health Check" strip** on the dashboard home.
- **Plain-language risk badges, not scores:** 🟢 Trusted / 🟡 New / 🔴 Verify before shipping.
- **A "Needs your attention today" home view** ahead of chart-heavy analytics.
- **One-tap actions on every order:** `tel:` "Call customer" and a WhatsApp button with pre-written, editable bilingual templates.
- **Bulk actions with a plain confirmation.**
- **CSV/Excel export everywhere there's a list.**
- **Optional phone + OTP login** for day-to-day staff roles, password + 2FA kept for Super Admin/Manager.

## Output Format
Single structured document: labeled sections matching the numbering above; Mermaid diagrams where applicable; tables for every data model; TypeScript/JSON code snippets embedded contextually; the folder structure as an indented tree; a short glossary for any Bangla UI terms used.

## Notes
- Treat COD plus MFS as the primary payment reality; card payments are secondary.
- The SKU and invoice-numbering system is a hard requirement, not optional.
- The certification-integrity rule from Section 1 is a hard requirement — badges and impact counters must reflect real, documented facts only.
- Fraud-prevention checks and honest urgency-signal data are hard requirements too.
- Flag any assumption (palette details, exact delivery-fee tiers, exact category list) as adjustable and confirm with the requester before finalizing.